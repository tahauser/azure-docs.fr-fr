---
title: Principal de service de cluster Azure Kubernetes
description: Créer et gérer un principal de service Azure Active Directory pour un cluster Kubernetes dans AKS
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: a7c80b64a33f4f71c694f80bf3e68f39ecd01828
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="service-principals-with-azure-container-service-aks"></a>Principaux de service avec Azure Container Service (AKS)

Un cluster AKS nécessite un [principal de service Azure Active Directory][aad-service-principal] pour interagir avec des API Azure. Le principal de service est nécessaire pour gérer dynamiquement des ressources telles que des [itinéraires définis par l’utilisateur][user-defined-routes] et[ Azure Load Balancer de couche 4][azure-load-balancer-overview].

Cet article indique les différentes options de configuration d’un principal de service pour votre cluster Kubernetes dans AKS.

## <a name="before-you-begin"></a>Avant de commencer


Pour créer un principal de service Azure AD, vous devez disposer des autorisations suffisantes pour inscrire une application auprès de votre client Azure AD et l’affecter à un rôle dans votre abonnement. Si vous n’avez pas les privilèges nécessaires, vous devriez demander à votre administrateur Azure AD ou administrateur d’abonnement de vous attribuer les privilèges nécessaires, de ou créer un principal de service pour le cluster Kubernetes au préalable.

Vous devez également avoir installé et configuré Azure CLI version 2.0.27 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Créer un Service Pack avec cluster AKS

Lors du déploiement d’un cluster AKS avec la commande `az aks create`, vous avez la possibilité de générer automatiquement un principal de service.

Dans l’exemple suivant, un cluster AKS est créé, et puisqu’un principal de service existant n’est pas spécifié, un principal de service est créé pour le cluster. Pour effectuer cette opération, votre compte doit disposer des droits nécessaires pour la création d’un principal de service.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Utiliser un Service Pack existant

Un principal de service Azure AD existant peut être utilisé ou créé au préalable pour une utilisation avec un cluster AKS. Cela s’avère utile lors du déploiement d’un cluster à partir du portail Azure où vous devez fournir les informations du principal de service. Lorsque vous utilisez un principal de service existant, la clé secrète du client doit être configurée comme un mot de passe.

## <a name="pre-create-a-new-sp"></a>Créer un nouveau Service Pack au préalable

Pour créer le principal de service avec Azure CLI, utilisez la commande [az ad sp create-for-rbac][az-ad-sp-create].

```azurecli
az ad sp create-for-rbac --skip-assignment
```

Le résultat ressemble à ce qui suit. Notez la valeur de `appId` et `password`. Ces valeurs sont utilisées lors de la création d’un cluster AKS.

```json
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
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Si vous déployez un cluster AKS à l’aide du portail Azure, entrez la valeur `appId` dans le champ **ID client de principal de service** et la valeur `password` dans le champ **Clé secrète client de principal de service** dans le formulaire de configuration de cluster AKS.

![Image de la navigation vers Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Considérations supplémentaires

Lorsque vous travaillez avec des principaux de service AKS et Azure AD, gardez les points suivants à l’esprit.

* Le principal de service pour Kubernetes fait partie de la configuration du cluster. Toutefois, n’utilisez pas l’identité pour déployer le cluster.
* Chaque principal de service est associé à une application Azure AD. Le principal de service pour un cluster Kubernetes peut être associé à tout nom d’application Azure AD valide (par exemple : `https://www.contoso.org/example`). L’URL de l’application ne doit pas être un point de terminaison réel.
* Lorsque vous spécifiez **l’ID Client** du principal de service, vous pouvez utiliser la valeur de `appId` (comme indiqué dans cet article) ou le principal de service correspondant `name` (par exemple, `https://www.contoso.org/example`).
* Sur les machines virtuelles principales et de nœud du cluster Kubernetes, les informations d’identification du principal de service sont stockées dans le fichier `/etc/kubernetes/azure.json`.
* Si vous utilisez la commande `az aks create` pour générer automatiquement le principal de service, les informations d’identification du principal de service sont écrites dans le fichier `~/.azure/acsServicePrincipal.json` sur la machine utilisée pour exécuter la commande.
* Lors de la suppression d’un cluster AKS qui a été créé par `az aks create`, le principal du service qui a été créé automatiquement ne sera pas supprimé. Vous pouvez utiliser `az ad sp delete --id $clientID` pour le supprimer.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les principaux de service Azure Active Directory, consultez la documentation d’applications Azure AD.

> [!div class="nextstepaction"]
> [Objets application et principal du service dans Azure Active Directory (Azure AD)][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]: ../active-directory/develop/active-directory-application-objects.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]: ../active-directory/develop/active-directory-application-objects.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md