---
title: Principal du service de cluster Azure Kubernetes | Microsoft Docs
description: "Créer et gérer un principal de service Azure Active Directory pour un cluster Kubernetes dans AKS"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service, kubernetes
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: af27d01108cbfb3bd71023ffbce85f348abb0cfe
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2017
---
# <a name="service-principals-with-azure-container-service-aks"></a>Principaux de service avec Azure Container Service (AKS)

Un cluster AKS nécessite un [principal de service Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) pour interagir avec des API Azure. Le principal de service est nécessaire pour gérer dynamiquement des ressources telles que des [itinéraires définis par l’utilisateur](../virtual-network/virtual-networks-udr-overview.md) et [Azure Load Balancer de couche 4](../load-balancer/load-balancer-overview.md).

Cet article indique les différentes options de configuration d’un principal de service pour votre cluster Kubernetes dans AKS.

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans ce document supposent que vous ayez créé un cluster ACS et que vous ayez établi une connexion kubectl avec le cluster. Si vous avez besoin de ces éléments, reportez-vous au [démarrage rapide d’AKS](./kubernetes-walkthrough.md).

Pour créer un principal de service Azure AD, vous devez disposer des autorisations suffisantes pour inscrire une application auprès de votre client Azure AD et l’affecter à un rôle dans votre abonnement. Si vous n’avez pas les privilèges nécessaires, vous devriez demander à votre administrateur Azure AD ou administrateur d’abonnement de vous attribuer les privilèges nécessaires, de ou créer un principal de service pour le cluster Kubernetes au préalable.

Vous devez également avoir installé et configuré Azure CLI version 2.0.21 ou ultérieure. Pour déterminer la version, exécutez la commande az--version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-sp-with-aks-cluster"></a>Créer un Service Pack avec cluster AKS

Lors du déploiement d’un cluster AKS avec la commande `az aks create`, vous avez la possibilité de générer automatiquement un principal de service.

Dans l’exemple suivant, un cluster AKS est créé, et puisqu’un principal de service existant n’est pas spécifié, un principal de service est créé pour le cluster. Pour effectuer cette opération, votre compte doit disposer des droits nécessaires pour la création d’un principal de service.

```azurecli
az aks create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Utiliser un Service Pack existant

Un principal de service Azure AD existant peut être utilisé ou créé au préalable pour une utilisation avec un cluster AKS. Cela s’avère utile lors du déploiement d’un cluster à partir du portail Azure où vous devez fournir les informations du principal de service.

Lorsque vous utilisez un principal de service existant, celui-ci doit remplir les conditions suivantes :

- Portée : l’abonnement utilisé pour déployer le cluster
- Rôle : Collaborateur
- Clé secrète client : doit être un mot de passe

## <a name="pre-create-a-new-sp"></a>Créer un nouveau Service Pack au préalable

Pour créer le principal de service avec Azure CLI, utilisez la commande [az ad sp create-for-rbac]().

```azurecli
id=$(az account show --query id --output tsv)
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/$id"
```

Le résultat ressemble à ce qui suit. Notez la valeur de `appId` et `password`. Ces valeurs sont utilisées lors de la création d’un cluster AKS.

```
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Utiliser un Service Pack existant

Lorsque vous utilisez un principal de service créé au préalable, fournissez `appId` et `password` en tant que valeurs d’argument à la commande `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myK8SCluster --service-principal <appId> ----client-secret <password>
```

Si vous déployez un cluster AKS à partir du portail Azure, entrez ces valeurs dans le formulaire de configuration de cluster AKS.

![Image de la navigation vers Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Considérations supplémentaires

Lorsque vous travaillez avec des principaux de service AKS et Azure AD, gardez les points suivants à l’esprit.

* Le principal de service pour Kubernetes fait partie de la configuration du cluster. Toutefois, n’utilisez pas l’identité pour déployer le cluster.
* Chaque principal de service est associé à une application Azure AD. Le principal de service pour un cluster Kubernetes peut être associé à tout nom d’application Azure AD valide (par exemple : `https://www.contoso.org/example`). L’URL de l’application ne doit pas être un point de terminaison réel.
* Lorsque vous spécifiez **l’ID Client** du principal de service, vous pouvez utiliser la valeur de `appId` (comme indiqué dans cet article) ou le principal de service correspondant `name` (par exemple, `https://www.contoso.org/example`).
* Sur les machines virtuelles principales et de nœud du cluster Kubernetes, les informations d’identification du principal du service sont stockées dans le fichier /etc/kubernetes/azure.json.
* Si vous utilisez la commande `az aks create` pour générer automatiquement le principal de service, les informations d’identification du principal de service sont écrites dans le fichier ~/.azure/acsServicePrincipal.json sur la machine utilisée pour exécuter la commande.
* Si vous utilisez la commande `az aks create` pour générer automatiquement le principal de service, ce dernier peut également s’authentifier auprès d’un registre [Azure Container Registry](../container-registry/container-registry-intro.md) créé dans le même abonnement.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les principaux de service Azure Active Directory, consultez la documentation d’applications Azure AD.

> [!div class="nextstepaction"]
> [Objets application et principal du service](../active-directory/develop/active-directory-application-objects.md)
