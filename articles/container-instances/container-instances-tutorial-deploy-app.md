---
title: "Didacticiel Azure Container Instances - Déployer des applications"
description: "Didacticiel Azure Container Instances (3/3) – Déployer une application"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 0532d255b271b2155ae3115f8f96c4cbb53916e4
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Déployer un conteneur sur Azure Container Instances

Il s’agit du didacticiel final d’une série en trois parties. Dans les deux premières parties, [nous avons créé une image conteneur](container-instances-tutorial-prepare-app.md) et [nous l’avons envoyée (push) vers un conteneur Azure Container Registry](container-instances-tutorial-prepare-acr.md). Cet article termine la série de didacticiels en déployant le conteneur sur Azure Container Instances.

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * déployer le conteneur à partir d’Azure Container Registry à l’aide d’Azure CLI ;
> * afficher l’application dans le navigateur ;
> * afficher les journaux du conteneur.

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel nécessite l’exécution d’Azure CLI 2.0.27 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez procéder à une installation ou une mise à niveau, consultez [Installation d’Azure CLI 2.0][azure-cli-install].

Pour terminer ce didacticiel, il vous faut un environnement de développement Docker installé localement. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

Azure Cloud Shell n’inclut pas les composants Docker requis pour effectuer chaque étape de ce didacticiel. Vous devez installer Azure CLI et l’environnement de développement Docker sur votre ordinateur local pour pouvoir réaliser les étapes de ce didacticiel.

## <a name="deploy-the-container-using-the-azure-cli"></a>Déployer le conteneur à l’aide de l’interface CLI Azure

L’interface CLI Azure permet de déployer un conteneur sur Azure Container Instances en une seule commande. L’image conteneur étant hébergée dans l’Azure Container Registry privé, vous devez inclure les informations d’identification requises pour y accéder. Récupérez les informations d’identification avec les commandes Azure CLI suivantes.

Serveur de connexion au Registre de conteneurs (mettez-le à jour avec le nom de votre Registre) :

```azurecli
az acr show --name <acrName> --query loginServer
```

Mot de passe du Registre de conteneurs :

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Votre application devra avoir été [préparée à l’avance][prepare-app], pour déployer votre image conteneur à partir du registre de conteneurs avec une requête de ressource de 1 noyau de processeur et 1 Go de mémoire, exécutez la commande [az container create][az-container-create] qui suit. Remplacez `<acrLoginServer>` et `<acrPassword>` par les valeurs obtenues à partir des deux commandes précédentes. Remplacez `<acrName>` par le nom de votre registre de conteneur ; vous pouvez également remplacer `aci-tutorial-app` par le nom que vous souhaitez donner à la nouvelle application.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-username <acrName> --registry-password <acrPassword> --dns-name-label aci-demo --ports 80
```

Après quelques secondes, vous devriez recevoir une réponse initiale de la part d’Azure Resource Manager. La valeur `--dns-name-label` doit être unique au sein de la région Azure dans laquelle vous créez l’instance de conteneur. Mettez à jour la valeur de l’exemple précédent si vous recevez un message d’erreur d’**Étiquette du nom DNS** lorsque vous exécutez la commande.

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

Exemple de sortie : `"aci-demo.eastus.azurecontainer.io"`

Pour afficher l’application en cours d’exécution, accédez au nom DNS affiché dans votre navigateur favori :

![Application Hello world dans le navigateur][aci-app-browser]

Vous pouvez également afficher la sortie du journal du conteneur :

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Output:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources que vous avez créées dans cette série de didacticiels, vous pouvez exécuter la commande [az group delete][az-group-delete] pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Cette commande supprime le registre de conteneurs que vous avez créé, ainsi que le conteneur en cours d’exécution et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez terminé le processus de déploiement de vos conteneurs sur Azure Container Instances. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * déployer le conteneur à partir d’Azure Container Registry à l’aide d’Azure CLI ;
> * afficher l’application dans le navigateur ;
> * afficher les journaux du conteneur.

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
