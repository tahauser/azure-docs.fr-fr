---
title: "Didacticiel Azure Container Instances - Déployer des applications"
description: "Didacticiel Azure Container Instances - Déployer des applications"
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: tutorial
ms.date: 11/20/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: a6b36349c7fae09e70178ae7e7c2b6c15c0c26d4
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-a-container-to-azure-container-instances"></a>Déployer un conteneur sur Azure Container Instances

Il s’agit de la dernière partie d’un didacticiel en trois parties. Dans les sections précédentes, [nous avons créé une image conteneur](container-instances-tutorial-prepare-app.md) et [nous l’avons transmise vers un Azure Container Registry](container-instances-tutorial-prepare-acr.md). Cette section termine le didacticiel en déployant le conteneur sur Azure Container Instances. Les étapes terminées sont les suivantes :

> [!div class="checklist"]
> * Déploiement du conteneur à partir d’Azure Container Registry à l’aide de l’interface CLI Azure
> * Affichage de l’application dans le navigateur
> * Affichage des journaux de conteneurs

## <a name="before-you-begin"></a>Avant de commencer

Ce didacticiel nécessite que vous exécutiez Azure CLI version 2.0.21 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli).

Pour terminer ce didacticiel, il vous faut un environnement de développement Docker. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell n’inclut pas les composants Docker requis pour effectuer chaque étape de ce didacticiel. Par conséquent, nous recommandons une installation locale de l’interface Azure CLI et de l’environnement de développement Docker.

## <a name="deploy-the-container-using-the-azure-cli"></a>Déployer le conteneur à l’aide de l’interface CLI Azure

L’interface CLI Azure permet de déployer un conteneur sur Azure Container Instances en une seule commande. L’image conteneur étant hébergée dans l’Azure Container Registry privé, vous devez inclure les informations d’identification requises pour y accéder. Si nécessaire, vous pouvez les interroger comme indiqué ci-dessous.

Serveur de connexion au Registre de conteneurs (mettez-le à jour avec le nom de votre Registre) :

```azurecli
az acr show --name <acrName> --query loginServer
```

Mot de passe du Registre de conteneurs :

```azurecli
az acr credential show --name <acrName> --query "passwords[0].value"
```

Pour déployer votre image conteneur à partir du registre de conteneurs avec une requête de ressource de 1 noyau de processeur et 1 Go de mémoire, exécutez la commande qui suit. Remplacez `<acrLoginServer>` et `<acrPassword>` par les valeurs obtenues à partir des deux commandes précédentes.

```azurecli
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public --ports 80 -g myResourceGroup
```

Après quelques secondes, vous devriez recevoir une réponse initiale de la part d’Azure Resource Manager. Pour afficher l’état du déploiement, utilisez la commande [az container show](/cli/azure/container#az_container_show) :

```azurecli
az container show --name aci-tutorial-app --resource-group myResourceGroup --query instanceView.state
```

Répétez la commande `az container show` jusqu’à ce que l’état passe de *En attente* à *En cours d’exécution*, ce qui doit prendre moins d’une minute. Lorsque le conteneur est *En cours d’exécution*, passez à l’étape suivante.

## <a name="view-the-application-and-container-logs"></a>Afficher les journaux d’applications et de conteneurs

Une fois le déploiement réussi, affichez l’adresse IP publique du conteneur à l’aide de la commande [az container show](/cli/azure/container#az_container_show) :

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

Exemple de sortie : `"13.88.176.27"`

Pour voir l’application en cours d’exécution, accédez à l’adresse IP publique dans votre navigateur favori.

![Application Hello world dans le navigateur][aci-app-browser]

Vous pouvez également afficher la sortie du journal du conteneur :

```azurecli
az container logs --name aci-tutorial-app -g myResourceGroup
```

Output:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous n’avez plus besoin des ressources que vous avez créées dans cette série de didacticiels, vous pouvez exécuter la commande [az group delete](/cli/azure/group#delete) pour supprimer le groupe de ressources et toutes les ressources qu’il contient. Cette commande supprime le registre de conteneurs que vous avez créé, ainsi que le conteneur en cours d’exécution et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez terminé le processus de déploiement de vos conteneurs sur Azure Container Instances. Les étapes suivantes ont été effectuées :

> [!div class="checklist"]
> * Déploiement du conteneur à partir d’Azure Container Registry à l’aide de l’interface CLI Azure
> * Affichage de l’application dans le navigateur
> * Affichage des journaux de conteneurs

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
