---
title: 'Azure Cosmos DB : Générer une application web Flask avec Python et l’API MongoDB Azure Cosmos DB | Microsoft Docs'
description: Cet article présente un exemple de code Python Flask que vous pouvez utiliser pour vous connecter à l’API MongoDB d’Azure Cosmos DB, et pour l’interroger.
services: cosmos-db
documentationcenter: ''
author: hshapiro
manager: scicoria
editor: ''
ms.assetid: ''
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/2/2017
ms.author: hshapiro
ms.openlocfilehash: ea6920c179bfee22c0aa545ccc3d0386940205e4
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="azure-cosmos-db-build-a-flask-app-with-the-mongodb-api"></a>Azure Cosmos DB : Générer une application Flask avec l’API MongoDB

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB.

Ce guide de démarrage rapide utilise [l’exemple Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) suivant et illustre comment créer une application de liste de tâches Flask simple avec [l’émulateur Azure Cosmos DB](/local-emulator.md) et l’[API MongoDB](mongodb-introduction.md) Azure Cosmos DB plutôt que MongoDB.

## <a name="prerequisites"></a>Prérequis


- Téléchargez [l’émulateur Azure Cosmos DB](/local-emulator.md). L’émulateur est actuellement pris en charge uniquement sur Windows. L’exemple montre comment utiliser une clé de production d’Azure, ce qui peut être effectué sur n’importe quelle plateforme.

- Si vous n’avez pas encore installé Visual Studio Code, vous pouvez rapidement installer [VS Code](https://code.visualstudio.com/Download) pour votre plateforme (Windows, Mac, Linux).

- Veillez à ajouter la prise en charge du langage Python en installant l’une des extensions Python populaires.
    1. Sélectionnez une extension.
    2. Installez l’extension en tapant `ext install` dans la Palette de commandes `Ctrl+Shift+P`.

    Les exemples dans ce document utilisent [l’extension Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) populaire et aux fonctionnalités complètes développée par Don Jayamanne.

## <a name="clone-the-sample-application"></a>Clonage de l’exemple d’application

À présent, nous allons cloner une application API Flask-MongoDB à partir de GitHub, configurer la chaîne de connexion, puis l’exécuter. Vous pouvez constater à quel point il est facile de travailler par programmation avec des données.

1. Ouvrez une fenêtre de terminal git, comme git bash, et accédez à un répertoire de travail à l’aide de la commande `cd`.
2. Exécutez la commande suivante pour cloner l’exemple de référentiel :

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Exécutez la commande suivante pour installer les modules Python.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Ouvrez le dossier dans Visual Studio Code.

## <a name="review-the-code"></a>Vérifier le code

Passons rapidement en revue ce qui se passe dans l’application. Ouvrez le fichier **app.py** dans le répertoire racine, et vous découvrirez que ces lignes de code créent la connexion Azure Cosmos DB. Le code suivant utilise la chaîne de connexion pour l’émulateur Azure Cosmos DB local. Le mot de passe doit être fractionné comme indiqué ci-dessous afin de prendre en charge les barres obliques, qui sinon ne pourraient pas être analysées.

* Initialisez le client MongoDB, récupérez la base de données et authentifiez-vous.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Récupérez la collection, ou créez-la si elle n’existe pas.

    ```python
    todos = db.todo #Select the collection
    ```

* Création de l'application

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Exécuter l’application web

1. Vérifiez que l’émulateur Azure Cosmos DB est en cours d’exécution.

2. Ouvrez une fenêtre de terminal et accédez avec la commande `cd` au répertoire dans lequel l’application est enregistrée.

3. Ensuite, définissez la variable d’environnement pour l’application Flask avec `set FLASK_APP=app.py` ou `export FLASK_APP=app.py` si vous utilisez un Mac.

4. Exécutez l’application avec `flask run` et accédez à [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Ajoutez et supprimez des tâches, et observez l’ajout ou la modification correspondante dans la collection.

## <a name="create-a-database-account"></a>Création d'un compte de base de données

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Mise à jour de votre chaîne de connexion

Si vous souhaitez tester le code par rapport à un compte Azure Cosmos DB actif, accédez au portail Azure pour créer un compte et obtenir vos informations de chaîne de connexion. Ensuite, copiez-les dans l’application.

1. Dans le [portail Azure](http://portal.azure.com/), dans votre compte Azure Cosmos DB, dans le volet de navigation de gauche, cliquez sur **Chaîne de connexion**, puis sur **Clés en lecture-écriture**. Vous utiliserez le bouton Copier sur le côté droit de l’écran pour copier le nom d’utilisateur, le mot de passe et l’hôte dans le fichier Dal.cs à l’étape suivante.

2. Ouvrez le fichier **app.py** dans le répertoire racine.

3. Copiez votre valeur de **nom d’utilisateur** à partir du portail (à l’aide du bouton Copier) et définissez-la comme valeur de **name** dans le fichier **app.py**.

4. Ensuite, copiez votre valeur de **chaîne de connexion** à partir du portail et affectez-la comme valeur de MongoClient dans votre fichier **app.py**.

5. Pour finir, copiez votre valeur de **mot de passe** à partir du portail et affectez-la comme valeur de **password** dans votre fichier **app.py**.

Vous venez de mettre à jour votre application avec toutes les informations nécessaires pour communiquer avec Azure Cosmos DB. Vous pouvez l’exécuter de la même manière qu’avant.

## <a name="deploy-to-azure"></a>Déployer dans Azure

Pour déployer cette application, vous pouvez créer une application web dans Azure et activer le déploiement continu avec une duplication (fork) de ce dépôt github. Suivez ce [didacticiel](https://docs.microsoft.com/azure/app-service-web/app-service-continuous-deployment) pour configurer le déploiement continu avec Github dans Azure.

Lors du déploiement sur Azure, vous devez supprimer vos clés d’application et vérifier que la section ci-dessous n’est pas commentée :

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Vous devez ensuite ajouter vos valeurs MONGOURL, MONGO_PASSWORD et MONGO_USERNAME aux paramètres d’application. Vous pouvez suivre ce [didacticiel](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings) pour en savoir plus sur les paramètres d’application dans Azure Web Apps.

Si vous ne souhaitez pas créer de duplication de ce dépôt, vous pouvez également cliquer sur le bouton Déployer sur Azure ci-dessous. Vous devez ensuite aller dans Azure et configurer les paramètres d’application avec vos informations de compte Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="http://azuredeploy.net/deploybutton.png"/>
</a>

> [!NOTE]
> Si vous envisagez de stocker votre code dans Github ou d’autres options de contrôle de code source, n’oubliez pas de supprimer vos chaînes de connexion du code. Elles peuvent être définies avec des paramètres d’application pour l’application web à la place.

## <a name="review-slas-in-the-azure-portal"></a>Vérification des contrats SLA dans le portail Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous ne pensez pas continuer à utiliser cette application, supprimez toutes les ressources créées durant ce guide de démarrage rapide dans le Portail Azure en procédant de la façon suivante :

1. Dans le menu de gauche du portail Azure, cliquez sur **Groupes de ressources**, puis sur le nom de la ressource que vous avez créée.
2. Sur la page de votre groupe de ressources, cliquez sur **Supprimer**, tapez le nom de la ressource à supprimer dans la zone de texte, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à créer un compte Azure Cosmos DB et à exécuter une application Flask à l’aide de l’API de MongoDB. Vous pouvez maintenant importer des données supplémentaires dans votre compte Cosmos DB.

> [!div class="nextstepaction"]
> [Importer des données dans Azure Cosmos DB pour l’API MongoDB](mongodb-migrate.md)
