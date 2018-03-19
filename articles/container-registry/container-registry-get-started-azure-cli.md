---
title: "Démarrage rapide - Créer un registre Docker privé dans Azure avec l’interface de ligne de commande Azure"
description: "Apprenez rapidement à créer un registre de conteneurs Docker privé avec l’interface de ligne de commande Azure."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: db1fb3deec4b70a9341753a59910aeb0e002bca0
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Créer un registre de conteneur à l’aide de l’interface de ligne de commande Azure

Azure Container Registry est un service de registre de conteneurs Docker géré utilisé pour stocker des images de conteneurs Docker privés. Ce guide détaille comment créer un registre de conteneurs Azure Container Registry avec Azure CLI, envoyer une image de conteneur dans le registre et enfin déployer le conteneur dans Azure Container Instances (ACI) à partir de votre registre.

Ce guide de démarrage rapide nécessite d’exécuter Azure CLI 2.0.27 ou ultérieur. Exécutez `az --version` pour trouver la version. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation d’Azure CLI 2.0][azure-cli].

Docker doit également être installé en local. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create][az-group-create]. Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Dans ce démarrage rapide, vous allez créer un registre *De base*. Azure Container Registry est disponible dans plusieurs références SKU, qui sont brièvement décrites dans le tableau suivant. Pour plus d’informations sur chaque référence, consultez [Références SKU de registres de conteneurs][container-registry-skus].

[!INCLUDE [container-registry-sku-matrix](../../includes/container-registry-sku-matrix.md)]

Créez une instance ACR à l’aide de la commande [az acr create][az-acr-create].

Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Dans l’exemple suivant, nous utilisons le nom *myContainerRegistry007*. Mettez à jour le nom de façon à utiliser une valeur unique.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Une fois le registre créé, la sortie ressemble à ce qui suit :

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Dans la suite du démarrage rapide, `<acrName>` est un espace réservé pour le nom de registre de conteneurs.

## <a name="log-in-to-acr"></a>Se connecter à l’ACR

Avant d’extraire et de transmettre des images conteneur, vous devez vous connecter à l’instance ACR. Pour ce faire, utilisez la commande [az acr login][az-acr-login].

```azurecli
az acr login --name <acrName>
```

Une fois l’opération terminée, la commande retourne un message `Login Succeeded`.

## <a name="push-image-to-acr"></a>Envoyer une image dans l’ACR

Pour envoyer une image dans un registre Azure Container Registry, vous devez tout d’abord disposer d’une image. Si vous n’avez pas encore toutes les images du conteneur local, exécutez la commande suivante pour tirer (pull) une image existante du hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

Avant de pousser (push) une image vers le registre, vous devez la marquer avec le nom complet de votre serveur de connexion ACR. Exécutez la commande suivante pour obtenir le nom complet du serveur de connexion de l’instance ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Marquez l’image en utilisant la commande [docker tag][docker-tag]. Remplacez `<acrLoginServer>` par le nom du serveur de connexion de votre instance ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Enfin, utilisez la commande [docker push][docker-push] pour pousser l’image vers l’instance ACR. Remplacez `<acrLoginServer>` par le nom du serveur de connexion de votre instance ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Répertorier les images conteneur

L’exemple suivant répertorie les référentiels d’un registre :

```azurecli
az acr repository list --name <acrName> --output table
```

Output:

```bash
Result
----------------
aci-helloworld
```

L’exemple suivant répertorie les étiquettes sur le référentiel **aci-helloworld**.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-helloworld --output table
```

Output:

```bash
Result
--------
v1
```

## <a name="deploy-image-to-aci"></a>Déployer l’image dans ACI

Pour déployer une instance de conteneur à partir du registre que vous avez créé, vous devez fournir les informations d’identification du registre lorsque vous le déployez. Dans des scénarios de production, vous devez utiliser un [principal de service][container-registry-auth-aci] pour un accès au registre de conteneur, mais pour simplifier ce démarrage rapide, activez l’utilisateur administrateur sur votre registre avec la commande suivante :

```azurecli
az acr update --name <acrName> --admin-enabled true
```

Une fois fait, le nom d’utilisateur est similaire à celui de votre registre et vous pouvez récupérer le mot de passe via cette commande :

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Pour déployer votre image conteneur avec un cœur à 1 processeur et 1 Go de mémoire, exécutez la commande suivante. Remplacez `<acrName>`, `<acrLoginServer>` et `<acrPassword>` par les valeurs obtenues à partir des commandes précédentes.

```azurecli
az container create --resource-group myResourceGroup --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Vous devez obtenir une réponse initiale d’Azure Resource Manager avec des détails concernant votre conteneur. Pour surveiller le statut de votre conteneur, et voir quand il s’exécute, répétez la commande [az container show][az-container-show]. L’opération doit prendre moins d’une minute.

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query instanceView.state
```

## <a name="view-the-application"></a>Afficher l’application

Une fois le déploiement vers ACI réussi, récupérez le nom de domaine complet du conteneur en exécutant la commande [az container show][az-container-show] :

```azurecli
az container show --resource-group myResourceGroup --name acr-quickstart --query ipAddress.fqdn
```

Exemple de sortie : `"aci-demo.eastus.azurecontainer.io"`

Pour voir l’application en cours d’exécution, accédez à l’adresse IP publique dans votre navigateur favori.

![Application Hello world dans le navigateur][aci-app-browser]

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources, l’instance ACR et toutes les images conteneurs.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un registre de conteneur Azure Container Registry avec Azure CLI, envoyé une image conteneur dans le registre et lancé une instance via des instances Azure Container Instances. Poursuivez avec le didacticiel sur Azure Container Instances pour approfondir vos connaissances sur ACI.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances][container-instances-tutorial-prepare-app]

<!-- IMAGES> -->
[aci-app-browser]: ../container-instances/media/container-instances-quickstart/aci-app-browser.png


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[az-container-show]: /cli/azure/container#az_container_show
[container-instances-tutorial-prepare-app]: ../container-instances/container-instances-tutorial-prepare-app.md
[container-registry-skus]: container-registry-skus.md
[container-registry-auth-aci]: container-registry-auth-aci.md