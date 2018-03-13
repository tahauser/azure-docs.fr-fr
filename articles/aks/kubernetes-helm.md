---
title: "Déployer des conteneurs avec Helm dans Kubernetes sur Azure"
description: "Utilisez l’outil d’empaquetage Helm pour déployer des conteneurs sur un cluster Kubernetes dans AKS"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f0fa4c667074a1acda70371251172acf9ff119e5
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="use-helm-with-azure-container-service-aks"></a>Utiliser Helm avec Azure Container Service (AKS)

[Helm][helm] est un outil d’empaquetage open source qui vous aide à installer et à gérer le cycle de vie d’applications Kubernetes. À l’instar de gestionnaires de package Linux tels que *APT* et *Yum*, Helm sert à gérer les graphiques Kubernetes, qui sont des packages de ressources Kubernetes préconfigurés.

Ce document explique comment configurer et utiliser Helm dans un cluster Kubernetes sur AKS.

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans ce document supposent que vous ayez créé un cluster ACS et que vous ayez établi une connexion kubectl avec le cluster. Si vous avez besoin de ces éléments, reportez-vous au [guide de démarrage rapide d’ACS][aks-quickstart].

## <a name="install-helm-cli"></a>Installer l’interface de ligne de commande (CLI) Helm

L’interface CLI Helm est un client qui s’exécute sur votre système de développement et vous permet de démarrer, d’arrêter et de gérer des applications avec des graphiques Helm.

Si vous utilisez Azure CloudShell, l’interface CLI Helm est déjà installée. Pour installer l’interface CLI Helm sur un Mac, utilisez `brew`. Pour connaître les autres options d’installation, consultez [Installation de Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Output:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="configure-helm"></a>Configurer Helm

La commande [helm init][helm-init] permet d’installer les composants Helm dans un cluster Kubernetes et d’effectuer la configuration côté client. Exécutez la commande suivante pour installer Helm sur votre cluster AKS et configurer le client Helm.

```azurecli-interactive
helm init
```

Output:

```
$HELM_HOME has been configured at /home/user/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.
Happy Helming!
```

## <a name="find-helm-charts"></a>Rechercher des graphiques Helm

Les graphiques Helm servent à déployer des applications dans un cluster Kubernetes. Pour rechercher des graphiques Helm créés au préalable, vous devez utiliser la commande [helm search][helm-search].

```azurecli-interactive
helm search
```

La sortie ressemblera à ce qui suit, avec toutefois de nombreux autres graphiques.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Pour mettre à jour la liste des graphiques, utilisez la commande [helm repo update][helm-repo-update].

```azurecli-interactive
helm repo update
```

Output:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Exécuter des graphiques Helm

Pour déployer un contrôleur d’entrée NGINX, utilisez la commande [helm install][helm-install].

```azurecli-interactive
helm install stable/nginx-ingress
```

La sortie ressemblera à ce qui suit, mais inclura des informations supplémentaires, telles que des instructions sur l’utilisation du déploiement Kubernetes.

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

Pour plus d’informations sur l’utilisation d’un contrôleur d’entrée NGINX avec Kubernetes, consultez [NGINX Ingress Controller][nginx-ingress] (Contrôleur d’entrée NGINX).

## <a name="list-helm-charts"></a>Répertorier les graphiques Helm

Pour afficher la liste des graphiques installés sur votre cluster, utilisez la commande [helm list][helm-list].

```azurecli-interactive
helm list
```

Output:

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la gestion des graphiques Kubernetes, consultez la documentation de Helm.

> [!div class="nextstepaction"]
> [Documentation de Helm][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://github.com/kubernetes/helm/blob/master/docs/index.md
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md