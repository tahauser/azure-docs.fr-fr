---
title: "Connecter une application MongoDB à Azure Cosmos DB à l’aide de Node.js | Microsoft Docs"
description: "Apprenez à connecter une application MongoDB Node.js existante à Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: quick start connect, mvc, devcenter
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/19/2017
ms.author: mimig
ms.openlocfilehash: e6e8e2c1c3a65aa88dae75fcb8a7d670d6f4c278
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-cosmos-db-migrate-an-existing-nodejs-mongodb-web-app"></a>Azure Cosmos DB : migrer une application web MongoDB Node.js existante 

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB. 

Ce démarrage rapide montre comment utiliser une application [MongoDB](mongodb-introduction.md) existante écrite en Node.js et comment la connecter à votre base de données Azure Cosmos DB, qui prend en charge les connexions clientes MongoDB. En d’autres termes, votre application Node.js sait uniquement qu’elle se connecte à une base de données qui utilise des API MongoDB. L’application sait que les données sont stockées dans Azure Cosmos DB.

Une fois que vous avez terminé, vous avez une application MEAN (MongoDB, Express, Angular et Node.js) exécutée dans [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

![Application MEAN.js exécutée dans Azure App Service](./media/create-mongodb-nodejs/meanjs-in-azure.png)


[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="prerequisites"></a>Prérequis 
Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

En plus d’Azure CLI, vous devez avoir installé [Node.js](https://nodejs.org/) et [Git](http://www.git-scm.com/downloads) localement pour exécuter les commandes `npm` et `git`.

Vous devriez avoir une bonne connaissance de Node.js. Ce démarrage rapide n’est pas destiné à vous aider à développer des applications Node.js en général.

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.  

Exécutez les commandes suivantes pour cloner l’exemple de référentiel. Cet exemple de référentiel contient l’application [MEAN.js](http://meanjs.org/) par défaut. 

```bash
git clone https://github.com/prashanthmadi/mean
```

## <a name="run-the-application"></a>Exécution de l'application

Installez les packages requis et démarrez l’application.

```bash
cd mean
npm install
npm start
```
L’application tente de se connecter à une source MongoDB et échoue. Continuez et quittez l’application lorsque la sortie renvoie le message « [MongoError: connect ECONNREFUSED 127.0.0.1:27017] ».

## <a name="log-in-to-azure"></a>Connexion à Azure

Si vous utilisez une interface de ligne de commande Azure installée, connectez-vous à votre abonnement Azure avec la commande [az login](/cli/azure/#az_login) et suivez les instructions à l’écran. Vous pouvez ignorer cette étape si vous utilisez Azure Cloud Shell.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Ajouter le module Azure Cosmos DB

Si vous utilisez une interface de ligne de commande Azure installée, vérifiez si le composant `cosmosdb` est déjà installé en exécutant la commande `az`. Si `cosmosdb` figure dans la liste des commandes de base, passez à la commande suivante. Vous pouvez ignorer cette étape si vous utilisez Azure Cloud Shell.

Si `cosmosdb` n’apparaît pas dans la liste des commandes de base, réinstallez [Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un [groupe de ressources](../azure-resource-manager/resource-group-overview.md) avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure comme les applications web, les bases de données et les comptes de stockage sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources dans la région Europe de l’Ouest. Choisissez un nom unique pour le groupe de ressources.

Si vous utilisez Azure Cloud Shell, cliquez sur **Essayer**, suivez les invites à l’écran pour vous connecter, puis copiez la commande dans l’invite de commandes.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

Créez un compte Azure Cosmos DB à l’aide de la commande [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create).

Dans la commande suivante, indiquez le nom unique de votre compte Azure Cosmos DB là où se trouve l’espace réservé `<cosmosdb-name>`. Ce nom unique sera utilisé en tant que point de terminaison Azure Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`). Pour cette raison, le nom doit être unique sur l’ensemble des comptes Azure Cosmos DB dans Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Le paramètre `--kind MongoDB` prend en charge les connexions clientes MongoDB.

Une fois le compte Azure Cosmos DB créé, Azure CLI affiche des informations similaires à celles de l’exemple suivant. 

> [!NOTE]
> Cet exemple utilise JSON comme format de sortie de l’interface de ligne de commande Azure, qui constitue le format par défaut. Pour utiliser un autre format de sortie, consultez l’article [Formats de sortie pour les commandes Azure CLI 2.0](https://docs.microsoft.com/cli/azure/format-output-azure-cli).

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

## <a name="connect-your-nodejs-application-to-the-database"></a>Connecter votre application Node.js à la base de données

Pendant cette étape, vous connectez votre exemple d’application MEAN.js à la base de données Azure Cosmos DB que vous venez de créer, en utilisant une chaîne de connexion MongoDB. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configurer la chaîne de connexion dans votre application Node.js

Dans votre référentiel MEAN.js, ouvrez `config/env/local-development.js`.

Remplacez le contenu de ce fichier par le code suivant. Veillez également à remplacer les deux espaces réservés `<cosmosdb-name>` avec le nom de votre compte Azure Cosmos DB.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Récupérer la clé

Pour se connecter à une base de données Azure Cosmos DB, vous avez besoin de la clé de la base de données. Utilisez la commande [az documentdb list-keys](/cli/azure/cosmosdb#list-keys) pour récupérer la clé primaire.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI génère des informations semblables à ce qui suit. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Copiez la valeur de `primaryMasterKey`. Collez celle-ci sur `<primary_master_key>` dans `local-development.js`.

Enregistrez vos modifications.

### <a name="run-the-application-again"></a>Exécutez de nouveau l'application.

Exécutez de nouveau `npm start`. 

```bash
npm start
```

Un message de console doit maintenant vous indiquer que l’environnement de développement est fonctionnel et en cours d’exécution. 

Dans un navigateur, accédez à `http://localhost:3000`. Cliquez sur **S’inscrire** dans le menu supérieur et essayez de créer deux utilisateurs fictifs. 

L’exemple d’application MEAN.js stocke les données utilisateur dans la base de données. Si vous réussissez et si MEAN.js se connecte automatiquement à l’utilisateur créé, cela signifie que votre connexion à Azure Cosmos DB fonctionne. 

![MEAN.js se connecte correctement à MongoDB](./media/create-mongodb-nodejs/mongodb-connect-success.png)

## <a name="view-data-in-data-explorer"></a>Afficher les données dans l’Explorateur de données

Les données stockées par Azure Cosmos DB peuvent être affichées, interrogées et exécutées selon une logique métier dans le portail Azure.

Pour afficher, interroger et manipuler les données utilisateur créées à l’étape précédente, connectez-vous au [portail Azure](https://portal.azure.com) dans votre navigateur web.

Dans la zone de recherche en haut de la page, tapez Azure Cosmos DB. Lorsque le panneau de votre compte Cosmos DB s’ouvre, sélectionnez votre compte Cosmos DB. Dans le volet de navigation gauche, cliquez sur Explorateur de données. Développez votre collection dans le volet Collections, pour pouvoir afficher les documents de la collection, interroger les données et même créer et exécuter des procédures stockées, des déclencheurs et des fonctions définies par l’utilisateur. 

![Explorateur de données dans le portail Azure](./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png)


## <a name="deploy-the-nodejs-application-to-azure"></a>Déployer l’application Node.js dans Azure

Dans cette étape, vous allez déployer dans Azure Cosmos DB votre application Node.js connectée à MongoDB.

Vous avez peut-être remarqué que le fichier de configuration que vous avez modifié précédemment est destiné à l’environnement de développement (`/config/env/local-development.js`). Lorsque vous déployez votre application dans App Service, elle s’exécute dans l’environnement de production par défaut. Maintenant, vous devez apporter la même modification au fichier de configuration correspondant.

Dans votre référentiel MEAN.js, ouvrez `config/env/production.js`.

Dans l’objet `db`, remplacez la valeur de `uri` comme indiqué dans l’exemple suivant. Veillez à remplacer les espaces réservés comme précédemment.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> L’option `ssl=true` est importante, car [Azure Cosmos DB nécessite SSL](connect-mongodb-account.md#connection-string-requirements). 
>
>

Dans le terminal, validez toutes vos modifications dans Git. Vous pouvez copier les deux commandes pour les exécuter ensemble.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée. 
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB et à créer une collection MongoDB à l’aide de l’Explorateur de données. Vous pouvez désormais migrer vos données MongoDB dans Azure Cosmos DB.  

> [!div class="nextstepaction"]
> [Importer des données MongoDB dans Azure Cosmos DB](mongodb-migrate.md)
