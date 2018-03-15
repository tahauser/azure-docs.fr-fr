---
title: "Intégrer avec des services gérés par Azure à l’aide d’Open Service Broker pour Azure (OSBA)"
description: "Intégrer avec des services gérés par Azure à l’aide d’Open Service Broker pour Azure (OSBA)"
services: container-service
author: sozercan
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 12/05/2017
ms.author: seozerca
ms.openlocfilehash: 594cb0afbdb0a44e9f092b9afc5af13b21e763a4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="integrate-with-azure-managed-services-using-open-service-broker-for-azure-osba"></a>Intégrer avec des services gérés par Azure à l’aide d’Open Service Broker pour Azure (OSBA)

Avec le [Catalogue de services Kubernetes][kubernetes-service-catalog], Open Service Broker pour Azure (OSBA) permet aux développeurs d’utiliser des services gérés par Azure dans Kubernetes. Ce guide se concentre sur le déploiement du Catalogue de services Kubernetes, d’Open Service Broker pour Azure (OSBA) et d’applications qui utilisent des services gérés par Azure à l’aide de Kubernetes.

## <a name="prerequisites"></a>Prérequis
* Abonnement Azure

* Azure CLI 2.0 : vous pouvez [l’installer localement][azure-cli-install] ou l’utiliser dans [Azure Cloud Shell][azure-cloud-shell].

* Helm CLI 2.7+ : vous pouvez [l’installer localement][helm-cli-install] ou l’utiliser dans [Azure Cloud Shell][azure-cloud-shell].

* Autorisations pour créer un principal de service avec le rôle Collaborateur pour votre abonnement Azure

* Un cluster Azure Container Service (AKS) existant. Si vous avez besoin d’un cluster AKS, suivez le guide de démarrage rapide [Créer un cluster AKS][create-aks-cluster].

## <a name="install-service-catalog"></a>Installer le Catalogue de services

La première étape consiste à installer le Catalogue de services dans votre cluster Kubernetes à l’aide d’un graphique Helm. Mettez à niveau votre installation Tiller (serveur Helm) dans votre cluster avec :

```azurecli-interactive
helm init --upgrade
```

Maintenant, ajoutez le graphique Catalogue de services au référentiel Helm :

```azurecli-interactive
helm repo add svc-cat https://svc-catalog-charts.storage.googleapis.com
```

Enfin, installez le Catalogue de services avec le graphique Helm :

```azurecli-interactive
helm install svc-cat/catalog --name catalog --namespace catalog --set rbacEnable=false
```

Une fois que le graphique Helm a été exécuté, vérifiez que `servicecatalog` s’affiche dans la sortie de la commande suivante :

```azurecli-interactive
kubectl get apiservice
```

Par exemple, le résultat doit ressembler à ce qui suit (affichage ici tronqué) :

```
NAME                                 AGE
v1.                                  10m
v1.authentication.k8s.io             10m
...
v1beta1.servicecatalog.k8s.io        34s
v1beta1.storage.k8s.io               10
```

## <a name="install-open-service-broker-for-azure"></a>Installer Open Service Broker pour Azure

L’étape suivante consiste à installer [Open Service Broker pour Azure][open-service-broker-azure], qui inclut le catalogue des services gérés par Azure. Exemples de services Azure disponibles : Azure Database pour PostgreSQL, Azure Redis Cache, Azure Database pour MySQL, Azure Cosmos DB, Azure SQL Database, etc.

Commencez par ajouter le référentiel Helm Open Service Broker pour Azure :

```azurecli-interactive
helm repo add azure https://kubernetescharts.blob.core.windows.net/azure
```

Utilisez ensuite le script suivant pour créer un [Principal de service][create-service-principal] et renseignez plusieurs variables. Ces variables sont utilisées lors de l’exécution du graphique Helm pour installer Service Broker.

```azurecli-interactive
SERVICE_PRINCIPAL=$(az ad sp create-for-rbac)
AZURE_CLIENT_ID=$(echo $SERVICE_PRINCIPAL | cut -d '"' -f 4)
AZURE_CLIENT_SECRET=$(echo $SERVICE_PRINCIPAL | cut -d '"' -f 16)
AZURE_TENANT_ID=$(echo $SERVICE_PRINCIPAL | cut -d '"' -f 20)
AZURE_SUBSCRIPTION_ID=$(az account show --query id --output tsv)
```

Maintenant que vous avez renseigné ces variables d’environnement, exécutez la commande suivante pour installer Service Broker.

```azurecli-interactive
helm install azure/open-service-broker-azure --name osba --namespace osba \
    --set azure.subscriptionId=$AZURE_SUBSCRIPTION_ID \
    --set azure.tenantId=$AZURE_TENANT_ID \
    --set azure.clientId=$AZURE_CLIENT_ID \
    --set azure.clientSecret=$AZURE_CLIENT_SECRET
```

Lorsque le déploiement d’OSBA est terminé, installez [l’interface CLI du Catalogue de services][service-catalog-cli], une interface de ligne de commande facile à utiliser pour interroger les Service Brokers, classes de services, plans de services, etc.

Exécutez les commandes suivantes pour installer l’interface CLI binaire du Catalogue de services :

```azurecli-interactive
curl -sLO https://servicecatalogcli.blob.core.windows.net/cli/latest/$(uname -s)/$(uname -m)/svcat
chmod +x ./svcat
```

Maintenant, répertoriez les répartiteurs de services installés :

```azurecli-interactive
./svcat get brokers
```

Le résultat ressemble à ce qui suit :

```
  NAME                               URL                                STATUS
+------+--------------------------------------------------------------+--------+
  osba   http://osba-open-service-broker-azure.osba.svc.cluster.local   Ready
```

Ensuite, répertoriez les classes de services disponibles. Les classes de services affichées sont les services gérés par Azure disponibles qui peuvent être provisionnés via Open Service Broker pour Azure.

```azurecli-interactive
./svcat get classes
```

Enfin, répertoriez tous les plans de services disponibles. Les plans de services sont les niveaux de service pour les services gérés par Azure. Par exemple, pour Azure Database pour MySQL, les plans sont compris entre `basic50` pour le niveau De base avec 50 DTU et `standard800` pour le niveau Standard avec 800 DTU.

```azurecli-interactive
./svcat get plans
```

## <a name="install-wordpress-from-helm-chart-using-azure-database-for-mysql"></a>Installer WordPress à partir du graphique Helm à l’aide d’Azure Database pour MySQL

Dans cette étape, vous utilisez Helm pour installer un graphique Helm mis à jour pour WordPress. Le graphique provisionne une instance Azure Database pour MySQL externe qui peut être utilisée par WordPress. Ce processus peut prendre plusieurs minutes.

```azurecli-interactive
helm install azure/wordpress --name wordpress --namespace wordpress --set resources.requests.cpu=0
```

Pour vérifier que l’installation a provisionné les bonnes ressources, répertoriez les liaisons et les instances de service installées :

```azurecli-interactive
./svcat get instances -n wordpress
./svcat get bindings -n wordpress
```

Répertoriez les secrets installés :

```azurecli-interactive
kubectl get secrets -n wordpress -o yaml
```

## <a name="next-steps"></a>Étapes suivantes

En suivant cet article, vous avez déployé le Catalogue de services dans un cluster Azure Container Service (AKS). Vous avez utilisé Open Service Broker pour Azure pour déployer une installation WordPress qui utilise des services gérés par Azure, dans ce cas Azure Database pour MySQL.

Consultez le référentiel [Azure/helm-charts][helm-charts] pour accéder à d’autres graphiques Helm basés sur OSBA mis à jour. Si vous souhaitez créer vos propres graphiques fonctionnant avec OSBA, consultez [Création d’un graphique][helm-create-new-chart].

<!-- LINKS - external -->
[helm-charts]: https://github.com/Azure/helm-charts
[helm-cli-install]: kubernetes-helm.md#install-helm-cli
[helm-create-new-chart]: https://github.com/Azure/helm-charts#creating-a-new-chart
[kubernetes-service-catalog]: https://github.com/kubernetes-incubator/service-catalog
[open-service-broker-azure]: https://github.com/Azure/open-service-broker-azure
[service-catalog-cli]: https://github.com/Azure/service-catalog-cli

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cloud-shell]: ../cloud-shell/overview.md
[create-aks-cluster]: ./kubernetes-walkthrough.md
[create-service-principal]: ./kubernetes-service-principal.md
