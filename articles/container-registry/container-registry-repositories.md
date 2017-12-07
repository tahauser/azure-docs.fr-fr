---
title: "Référentiels Azure Container Registry"
description: "Utiliser des référentiels Azure Container Registry pour gérer des images Docker"
services: container-registry
author: cristy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 03/24/2017
ms.author: cristyg
ms.openlocfilehash: 3321dd1d8bbd1b8232c26491edd8c374df16b813
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-repositories"></a>Référentiels Azure Container Registry

Un référentiel Azure Container Registry vous permet de stocker des images de conteneur. En stockant les images dans des référentiels, vous pouvez créer des groupes d’images (ou une version de ces images) au sein d’environnements isolés. Vous pouvez spécifier ces référentiels lorsque vous envoyez des images à votre Registre via une transmission de type push.


## <a name="prerequisites"></a>Composants requis
* **Azure Container Registry** : créez un Registre de conteneur dans votre abonnement Azure. Par exemple, utilisez le [portail Azure](container-registry-get-started-portal.md) ou [Azure CLI 2.0](container-registry-get-started-azure-cli.md).
* **Interface CLI Docker** : configurez votre ordinateur local comme un hôte Docker et accédez aux commandes de l’interface CLI Docker, installez le [moteur Docker](https://docs.docker.com/engine/installation/).
* **Extraire une image** : extrayez une image à partir du Registre Docker Hub public, marquez-la et envoyez-la à votre Registre via une transmission de type push. Pour savoir comment envoyer des images via une transmission de type push et en extraire, voir [Transmission de votre première image vers un Registre de conteneur Docker privé à l’aide de l’interface de ligne de commande (CLI) Docker](container-registry-get-started-docker-cli.md).


## <a name="viewing-repositories-in-the-portal"></a>Affichage de référentiels dans le portail

Une fois que vous avez envoyé des images à votre Registre de conteneur via une transmission de type push, vous pouvez voir une liste des référentiels hébergeant les images dans le portail Azure.

Si vous avez suivi les étapes de l’article [Transmission de votre première image vers un Registre de conteneur Docker privé à l’aide de l’interface de ligne de commande (CLI) Docker](container-registry-get-started-docker-cli.md), vous devez maintenant voir apparaître une image Nginx dans le Registre de conteneur. Dans le cadre des instructions, vous avez dû spécifier un espace de noms pour l’image. Dans l’exemple ci-dessous, la commande envoie l’image Nginx via une transmission de type push, vers le référentiel « Samples » :

```
docker push myregistry.azurecr.io/samples/nginx
```
 Azure Container Registry prend en charge les espaces de noms de référentiel à plusieurs niveaux. Cette fonctionnalité vous permet de regrouper des collections d’images liées à une application spécifique, ou une collection d’applications à des équipes opérationnelles ou de développement spécifiques. Pour en savoir plus sur les référentiels des Registres de conteneur, voir [Présentation des registres de conteneurs Docker privés](container-registry-intro.md).

Pour afficher les référentiels Azure Container Registry, procédez comme suit :

1. Connectez-vous au portail Azure.
2. Sur le panneau **Azure Container Registry**, sélectionnez le Registre que vous souhaitez analyser.
3. Sur le panneau de ce Registre, cliquez sur **Référentiels** pour afficher la liste de tous les référentiels et de leurs images.
4. (Facultatif) Sélectionnez une image spécifique pour afficher les balises.

![Affichage de référentiels dans le portail](./media/container-registry-repositories/container-registry-repositories.png)


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous connaissez les principes de base, vous êtes prêt à utiliser votre Registre. Par exemple, commencez à déployer les images du conteneur vers un cluster [Azure Container Service](https://azure.microsoft.com/documentation/services/container-service/).
