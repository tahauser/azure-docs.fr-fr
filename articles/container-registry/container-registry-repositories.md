---
title: "Les référentiels Azure Container Registry dans le portail Azure"
description: "Procédure d’affichage des référentiels Azure Container Registry dans le portail Azure."
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 01/05/2018
ms.author: cristyg
ms.openlocfilehash: 593972e972207a27d1232fcb0c1bf220ac3a8def
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Affichage des référentiels Container Registry dans le portail Azure

Un référentiel Azure Container Registry vous permet de stocker des images de conteneur Docker. En stockant les images dans des référentiels, vous pouvez stocker des groupes d’images (ou une version de ces images) au sein d’environnements isolés. Vous pouvez spécifier ces référentiels lorsque vous envoyez des images à votre Registre via une transmission de type push, et afficher leur contenu dans le portail Azure.

## <a name="prerequisites"></a>Conditions préalables

* **Registre de conteneurs** : créez un Registre de conteneurs dans votre abonnement Azure. Par exemple, utilisez le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI** : Installez [Docker][docker-install] sur votre ordinateur local, ce qui vous fournit l’interface de ligne de commande Docker.
* **Image de conteneur** : distribuez une image dans Container Registry. Pour savoir comment envoyer des images via une transmission de type push et en extraire, voir [Envoyer et extraire une image](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Affichage des référentiels dans le portail Azure

Vous pouvez voir une liste des référentiels hébergeant vos images, ainsi que les balises d’image dans le portail Azure.

Si vous avez suivi les étapes de [Envoyer et extraire une image](container-registry-get-started-docker-cli.md) (et n’avez pas supprimé l’image par la suite), vous devez voir apparaître une image Nginx dans le Registre de conteneur. Les instructions de l’article spécifient que vous balisez l’image avec un espace de noms, les « exemples » dans `/samples/nginx`. Pour rappel, la commande [docker push][docker-push] spécifiée dans cet article était :

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Azure Container Registry prenant en charge les espaces de noms à plusieurs niveaux, vous pouvez étendre des collections d’images liées à une application spécifique, ou une collection d’applications à des équipes opérationnelles ou de développement différentes. Pour en savoir plus sur les référentiels des Registres de conteneur, voir [Présentation des registres de conteneurs Docker privés](container-registry-intro.md).

Pour afficher un référentiel :

1. Connectez-vous au [portail Azure][portal]
1. Sélectionnez l’instance **Azure Container Registry** sur laquelle vous avez poussé l’image Nginx
1. Sélectionnez **Référentiels** pour afficher la liste des espaces de stockage qui contiennent les images dans le registre
1. Sélectionnez un référentiel pour consulter les balises d’image qui s’y trouvent

Par exemple, si vous avez poussé l’image Nginx, comme indiqué dans [Envoyer et extraire une image](container-registry-get-started-docker-cli.md), vous devriez voir quelque chose de similaire à :

![Affichage de référentiels dans le portail](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les principes fondamentaux de l’affichage et de l’utilisation des référentiels dans le portail, essayez d’utiliser Azure Container Registry avec un cluster [Azure Container Service](../aks/tutorial-kubernetes-prepare-app.md).

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
