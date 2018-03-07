---
title: "SSH dans les nœuds de cluster Azure Container Service (AKS)"
description: "Créer une connexion SSH avec des nœuds de cluster Azure Container Service (AKS)"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>SSH dans les nœuds de cluster Azure Container Service (AKS)

Vous avez parfois besoin d’accéder à un nœud Azure Container Service (AKS) à des fins de maintenance, de collecte de journaux ou d’autres opérations de dépannage. Les nœuds Azure Container Service (AKS) ne sont pas exposés sur Internet. Utilisez les étapes décrites dans ce document pour créer une connexion SSH avec un nœud AKS.

## <a name="configure-ssh-access"></a>Configurer l’accès SSH

 Pour établir une connexion SSH à un nœud spécifique, un pod est créé avec un accès à `hostNetwork`. Un service est également créé pour l’accès au pod. Cette configuration est privilégiée et doit être supprimée après utilisation.

Créez un fichier nommé `aks-ssh.yaml` et copiez-le dans ce manifeste. Mettez à jour le nom du nœud avec le nom du nœud AKS cible.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

Exécutez le manifeste pour créer le pod et le service.

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

Obtenez l’adresse IP externe du service exposé. La configuration de l’adresse IP peut prendre quelques minutes. 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

Créez la connexion SSH. 

Le nom d’utilisateur par défaut d’un cluster AKS est `azureuser`. Si ce compte a été modifié au moment de la création du cluster, remplacez le nom d’utilisateur administrateur par celui qui convient. 

Si votre clé n’est pas dans `~/ssh/id_rsa`, indiquez l’emplacement correct à l’aide de l’argument `ssh -i`.

```azurecli-interactive
$ ssh azureuser@13.92.154.191

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>Supprimer l’accès SSH

Lorsque vous avez terminé, supprimez le pod d’accès SSH et le service.

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```