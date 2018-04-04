---
title: Didacticiel Azure Container Instances - Déployer des applications
description: Didacticiel Azure Container Instances (3/3) – Déployer une application
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 29d7114f288f7387d0c7cd5c6afe2eaaa7a8c560
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-deploy-a-container-to-azure-container-instances"></a>Didacticiel : Déployer un conteneur sur Azure Container Instances

Il s’agit du didacticiel final d’une série en trois parties. Dans les deux premières parties, [nous avons créé une image conteneur](container-instances-tutorial-prepare-app.md) et [nous l’avons envoyée à Azure Container Registry](container-instances-tutorial-prepare-acr.md). Cet article termine la série en déployant le conteneur sur Azure Container Instances.

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer le conteneur à partir d’Azure Container Registry vers Azure Container Instances
> * Afficher l’application en cours d’exécution dans le navigateur
> * Afficher les journaux du conteneur

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Déployer le conteneur à l’aide de l’interface CLI Azure

Dans cette section, vous utilisez l’interface Azure CLI pour déployer l’image générée dans le [premier didacticiel](container-instances-tutorial-prepare-app.md) et envoyée à Azure Container Registry dans le [deuxième didacticiel](container-instances-tutorial-prepare-acr.md). Assurez-vous d’avoir terminé ces didacticiels avant de continuer.

### <a name="get-registry-credentials"></a>Obtenir les informations d’identification du registre

Lorsque vous déployez une image qui est hébergée dans un registre de conteneurs privé, tel que celui créé dans le [deuxième didacticiel](container-instances-tutorial-prepare-acr.md), vous devez fournir les informations d’identification du registre.

D’abord, obtenez le nom complet du serveur de connexion du registre de conteneurs (remplacez `<acrName>` par le nom de votre registre) :

```azurecli
az acr show --name <acrName> --query loginServer
```

Ensuite, obtenez le mot de passe du registre de conteneurs :

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

### <a name="deploy-container"></a>Déployer le conteneur

Maintenant, utilisez la commande [az container create][az-container-create] pour déployer le conteneur. Remplacez `<acrLoginServer>` et `<acrPassword>` par les valeurs obtenues à partir des deux commandes précédentes. Remplacez `<acrName>` par le nom de votre registre de conteneurs.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Après quelques secondes, vous devriez recevoir une réponse initiale d’Azure. La valeur `--dns-name-label` doit être unique au sein de la région Azure dans laquelle vous créez l’instance de conteneur. Modifiez la valeur de la commande précédente si vous recevez un message d’erreur **Étiquette de nom DNS** lorsque vous exécutez la commande.

### <a name="verify-deployment-progress"></a>Vérifier la progression du déploiement

Pour afficher l’état du déploiement, utilisez la commande [az container show][az-container-show] :

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Répétez la commande [az container show][az-container-show] jusqu’à ce que l’état passe de *En attente* à *En cours d’exécution*, ce qui prend normalement moins d’une minute. Lorsque le conteneur est *En cours d’exécution*, passez à l’étape suivante.

## <a name="view-the-application-and-container-logs"></a>Afficher les journaux d’applications et de conteneurs

Une fois le déploiement réussi, affichez le nom de domaine complet du conteneur avec la commande [az container show][az-container-show] :

```bash
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Par exemple : 
```console
$ az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
"aci-demo.eastus.azurecontainer.io"
```

Pour afficher l’application en cours d’exécution, accédez au nom DNS affiché dans votre navigateur favori :

![Application Hello world dans le navigateur][aci-app-browser]

Vous pouvez également afficher la sortie du journal du conteneur :

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Exemple de sortie :

```bash
$ az container logs --resource-group myResourceGroup --name aci-tutorial-app
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources que vous avez créées dans cette série de didacticiels, vous pouvez exécuter la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Cette commande supprime le registre de conteneurs que vous avez créé, ainsi que le conteneur en cours d’exécution et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez terminé le processus de déploiement de votre conteneur sur Azure Container Instances. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déployer le conteneur à partir d’Azure Container Registry à l’aide d’Azure CLI
> * afficher l’application dans le navigateur ;
> * afficher les journaux du conteneur.

Maintenant que vous avez les bases, continuez pour en savoir plus sur Azure Container Instances, par exemple sur le fonctionnement des groupes de conteneurs :

> [!div class="nextstepaction"]
> [Groupes de conteneurs dans Azure Container Instances](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
