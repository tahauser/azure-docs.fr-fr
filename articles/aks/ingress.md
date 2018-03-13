---
title: "Configurer l’entrée avec le cluster Azure Container Service (AKS)"
description: "Installez et configurez un contrôleur d’entrée NGINX dans un cluster Azure Container Service (AKS)."
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c25a0171bd412050a7c94e9b077436cd1ebe893b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="https-ingress-on-azure-container-service-aks"></a>Entrée HTTPS sur Azure Container Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse externe peut être utilisée pour acheminer le trafic vers plusieurs services dans un cluster Kubernetes.

Ce document décrit un exemple de déploiement du [contrôleur d’entrée NGINX][nginx-ingress] dans un cluster Azure Container Service (AKS). De plus, le projet [KUBE-LEGO][kube-lego] est utilisé pour générer et configurer automatiquement des certificats [Let’s Encrypt][lets-encrypt]. Enfin, plusieurs applications sont exécutées dans le cluster AKS, chacune étant accessible par le biais d’une adresse unique.

## <a name="install-an-ingress-controller"></a>Installer un contrôleur d’entrée

Utilisez Helm pour installer le contrôleur d’entrée NGINX. Consultez la [documentation][nginx-ingress] du contrôleur d’entrée NGINX pour plus d’informations sur le déploiement. 

```
helm install stable/nginx-ingress
```

Pendant l’installation, une adresse IP publique Azure est créée pour le contrôleur d’entrée. Pour obtenir l’adresse IP publique, utilisez la commande kubectl get service. L’assignation de l’adresse IP au service peut prendre un certain temps.

```console
$ kubectl get service

NAME                                          TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
kubernetes                                    ClusterIP      10.0.0.1       <none>           443/TCP                      3d
toned-spaniel-nginx-ingress-controller        LoadBalancer   10.0.236.223   52.224.125.195   80:30927/TCP,443:31144/TCP   18m
toned-spaniel-nginx-ingress-default-backend   ClusterIP      10.0.5.86      <none>           80/TCP                       18m
```

Étant donné qu’aucune règle d’entrée n’a été créée, si vous accédez à l’adresse IP publique, vous êtes redirigé vers la page 404 par défaut des contrôleurs d’entrée NGINX.

![Backend NGINX par défaut](media/ingress/default-back-end.png)

## <a name="configure-dns-name"></a>Configurer un nom DNS

Étant donné que des certificats HTTPS sont utilisés, vous devez configurer un nom de domaine complet pour l’adresse IP des contrôleurs d’entrée. Pour cet exemple, un nom de domaine complet Azure est créé avec Azure CLI. Mettez à jour le script avec l’adresse IP du contrôleur d’entrée et le nom à utiliser dans le nom de domaine complet.

```
#!/bin/bash

# Public IP address
IP="52.224.125.195"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get resource group and public ip name
RESOURCEGROUP=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[resourceGroup]" --output tsv)
PIPNAME=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[name]" --output tsv)

# Update public ip address with dns name
az network public-ip update --resource-group $RESOURCEGROUP --name  $PIPNAME --dns-name $DNSNAME
```

Si nécessaire, exécutez la commande suivante pour récupérer le nom de domaine complet. Mettez à jour la valeur de l’adresse IP avec celle de votre contrôleur d’entrée.

```azurecli
az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '52.224.125.195')].[dnsSettings.fqdn]" --output tsv
```

Le contrôleur d’entrée est désormais accessible par le biais du nom de domaine complet.

## <a name="install-kube-lego"></a>Installer KUBE-LEGO

Le contrôleur d’entrée NGINX prend en charge l’arrêt TLS. Alors qu’il existe plusieurs façons de récupérer et configurer des certificats pour le protocole HTTPS, ce document illustre l’utilisation de [KUBE-LEGO][kube-lego], laquelle fournit une fonctionnalité de génération et gestion de certificats automatique [Lets Encrypt][lets-encrypt].

Pour installer le contrôleur KUBE-LEGO, utilisez la commande d’installation Helm suivante. Mettez à jour l’adresse e-mail avec une adresse de votre organisation.

```
helm install stable/kube-lego \
  --set config.LEGO_EMAIL=user@contoso.com \
  --set config.LEGO_URL=https://acme-v01.api.letsencrypt.org/directory
```

Pour plus d’informations sur la configuration de KUBE-LEGO, consultez la [documentation de KUBE-LEGO][kube-lego].

## <a name="run-application"></a>Exécuter l’application

À ce stade, un contrôleur d’entrée et une solution de gestion de certificats ont été configurés. Exécutez maintenant quelques applications dans votre cluster AKS.

Pour cet exemple, Helm est utilisé pour exécuter plusieurs instances d’une simple application Hello World.

Avant d’exécuter l’application, ajoutez le dépôt Helm des exemples Azure sur votre système de développement.

```
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

 Exécutez le graphique AKS Hello World avec la commande suivante :

```
helm install azure-samples/aks-helloworld
```

Installez maintenant une deuxième instance de l’application Hello World.

Pour la deuxième instance, spécifiez un nouveau titre afin que les deux applications soient visuellement distinctes. Vous devez également spécifier un nom de service unique. Ces configurations peuvent être consultées dans la commande suivante.

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-ingress-route"></a>Créer un itinéraire d’entrée

Les deux applications sont maintenant en cours d’exécution sur votre cluster Kubernetes ; toutefois, elles ont été configurées avec un service de type `ClusterIP`. Ainsi, ces applications ne sont pas accessibles à partir d’Internet. Afin de les rendre disponibles, créez une ressource d’entrée Kubernetes. La ressource d’entrée configure les règles qui acheminent le trafic vers l’une des deux applications.

Créez un fichier nommé `hello-world-ingress.yaml` et copiez-y le YAML suivant.

Notez que le trafic vers l’adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/` est acheminé vers le service nommé `aks-helloworld`. Le trafic vers l’adresse `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` est acheminé vers le service `ingress-demo`.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/tls-acme: "true"
    ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Créez la ressource d’entrée avec la commande `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml
```

## <a name="test-the-ingress-configuration"></a>Tester la configuration d’entrée

Recherchez le nom de domaine complet de votre contrôleur d’entrée Kubernetes. Vous devez voir l’application Hello World.

![Exemple d’application numéro un](media/ingress/app-one.png)

Maintenant, accédez au nom de domaine complet du contrôleur d’entrée avec le chemin `/hello-world-two`. Vous devez voir l’application Hello World avec le titre personnalisé.

![Exemple d’application numéro deux](media/ingress/app-two.png)

Notez également que la connexion est chiffrée et qu’un certificat émis par Let’s Encrypt est utilisé.

![Certificat Lets Encrypt](media/ingress/certificate.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez le logiciel mentionné dans ce document. 

- [Contrôleur d’entrée NGINX][nginx-ingress]
- [KUBE-LEGO][kube-lego]

<!-- LINKS - external -->
[kube-lego]: https://github.com/jetstack/kube-lego
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
