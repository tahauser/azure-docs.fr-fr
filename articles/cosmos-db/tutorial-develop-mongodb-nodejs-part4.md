---
title: Didacticiels MongoDB, Angular et Node pour Azure - Partie 4 | Microsoft Docs
description: "Il s’agit de la partie 4 de cette série de didacticiels sur la création d’une application MongoDB avec Angular et Node sur Azure Cosmos DB à l’aide des mêmes API que celles utilisées pour MongoDB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: e1d96fe8cb06be08863bbcd1885080aaca8fbd61
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-4-create-an-azure-cosmos-db-account-using-the-azure-cli"></a>Créer une application MongoDB avec Angular et Azure Cosmos DB - Partie 4 : Créer un compte Azure Cosmos DB à l’aide de la CLI Azure

Ce didacticiel en plusieurs parties montre comment créer une application [API MongoDB](mongodb-introduction.md) écrite en Node.js avec Express, Angular et votre base de données Azure Cosmos DB.

La partie 4 de ce didacticiel est basée sur la [partie 3](tutorial-develop-mongodb-nodejs-part3.md) et aborde les tâches suivantes :

> [!div class="checklist"]
> * Créer un groupe de ressources Azure à l’aide d’Azure CLI
> * Créer un compte Azure Cosmos DB à l’aide de la CLI Azure

## <a name="video-walkthrough"></a>Vidéo de procédure pas à pas

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Prérequis

Avant de commencer cette partie du didacticiel, assurez-vous d’avoir effectué les étapes de la [partie 3](tutorial-develop-mongodb-nodejs-part3.md) du didacticiel. 

Dans cette section du didacticiel, vous pouvez utiliser Azure Cloud Shell (dans votre navigateur internet) ou [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) installé en local. Si vous utilisez Azure CLI en local, veillez à exécuter Azure CLI 2.0 ou une version plus récente. Exécutez la commande `az --version` à l’invite de commandes pour vérifier la version que vous utilisez. 

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Ce didacticiel vous guide à travers les étapes pour générer l’application pas à pas. Si vous souhaitez télécharger le projet terminé, vous pouvez obtenir l’application complète à partir du [référentiel cosmosdb-angular](https://github.com/Azure-Samples/angular-cosmosdb) sur GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

Créez un compte Azure Cosmos DB en exécutant la commande [`az cosmosdb create`](/cli/azure/cosmosdb#az_cosmosdb_create).

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Dans `<cosmosdb-name>`, utilisez le nom de votre compte Azure Cosmos DB. Il doit être unique parmi tous les noms de compte Azure Cosmos DB présents dans Azure.
* Le paramètre `--kind MongoDB` active les connexions MongoDB pour Azure Cosmos DB.

L’exécution de la commande peut durer quelques minutes. Une fois la commande terminée, la fenêtre du terminal affiche les informations de la nouvelle base de données. 

Après avoir créé le compte Azure Cosmos DB :
1. Ouvrez une nouvelle fenêtre dans votre navigateur et accédez à [https://portal.azure.com](https://portal.azure.com)
1. Cliquez sur le logo Azure Cosmos DB ![dans le portail Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) dans la barre de gauche pour afficher toutes les bases de données Azure Cosmos DB que vous possédez.
1. Cliquez sur le compte Azure Cosmos DB que vous venez de créer, sélectionnez l’onglet **Vue d’ensemble** et faites défiler vers le bas pour voir où se trouve la base de données. 

    ![Nouveau compte Azure Cosmos DB dans le portail Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. Faites défiler vers le bas dans la barre de navigation située à gauche, puis cliquez sur l’onglet **Répliquer les données mondialement** pour afficher un mappage indiquant les différentes zones dans lesquelles vous pouvez répliquer des données. Par exemple, vous pouvez cliquer sur Sud-Est de l’Australie ou Est de l’Australie pour répliquer vos données en Australie. Pour en savoir plus sur la réplication mondiale, consultez [Distribution mondiale des données avec Azure Cosmos DB](distribute-data-globally.md). Pour le moment, conservons cette instance et lorsque nous voudrons répliquer, nous saurons comment procéder.

    ![Nouveau compte Azure Cosmos DB dans le portail Azure](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Étapes suivantes

Dans cette partie du didacticiel, vous avez :

> [!div class="checklist"]
> * Créé un groupe de ressources Azure à l’aide d’Azure CLI
> * Créé un compte Azure Cosmos DB à l’aide d’Azure CLI

Vous pouvez maintenant passer à la partie suivante du didacticiel afin de connecter Azure Cosmos DB et votre application à l’aide de Mongoose.

> [!div class="nextstepaction"]
> [Utiliser Mongoose pour se connecter à Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)
