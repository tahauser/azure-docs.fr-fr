---
title: "Utilisation de l’infrastructure Mongoose avec Azure Cosmos DB | Microsoft Docs"
description: "Apprenez à connecter une application Mongoose Node.js à Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: romitgirdhar
manager: jhubbard
editor: 
ms.assetid: de5eea58-ee7c-4609-b1c9-4af3e61a5883
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 01/08/2018
ms.author: rogirdh
ms.openlocfilehash: fb6db6555171b65767a715c6b4c8ff37f42c94ef
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-cosmos-db-using-the-mongoose-framework-with-azure-cosmos-db"></a>Azure Cosmos DB : Utilisation de l’infrastructure Mongoose avec Azure Cosmos DB

Ce didacticiel montre comment utiliser l’[infrastructure Mongoose](http://mongoosejs.com/) lors du stockage de données dans Azure Cosmos DB. Dans cette procédure pas à pas, nous utilisons l’API MongoDB pour Azure Cosmos DB. Pour ceux d’entre vous qui ne connaissent pas Mongoose, il s’agit d’une infrastructure de modélisation d’objet pour MongoDB dans Node.js qui offre une solution simple basée sur un schéma pour modéliser vos données d’application.

Azure Cosmos DB est le service de base de données multi-modèle de Microsoft distribué à l’échelle mondiale. Rapidement, vous avez la possibilité de créer et d’interroger des documents, des paires clé/valeur, et des bases de données orientées graphe, profitant tous de la distribution à l’échelle mondiale et des capacités de mise à l’échelle horizontale au cœur d’Azure Cosmos DB.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) version v0.10.29 ou supérieure.

## <a name="create-an-azure-cosmos-db-account"></a>Création d’un compte Azure Cosmos DB

Commençons par créer un compte Azure Cosmos DB. Si vous avez déjà un compte que vous souhaitez utiliser, vous pouvez passer directement à l’étape [Configurer votre application Node.js](#SetupNode). Si vous utilisez l’émulateur Azure Cosmos DB, suivez les étapes de la section [Émulateur Azure Cosmos DB](local-emulator.md) pour le configurer, puis passez directement à l’étape [Configurer votre application Node.js](#SetupNode).

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="set-up-your-nodejs-application"></a>Configurer votre application Node.js

>[!Note]
> Si vous souhaitez simplement parcourir l’exemple de code sans configurer l’application elle-même, clonez l’[exemple](https://github.com/Azure-Samples/Mongoose_CosmosDB) utilisé pour ce didacticiel et créez votre application Mongoose Node.js dans Azure Cosmos DB.

1. Pour créer une application Node.js dans le dossier de votre choix, exécutez la commande suivante dans une invite de commandes Node.

    ```npm init```

    Répondez aux questions et votre projet est prêt à être utilisé.

1. Ajoutez un nouveau fichier dans le dossier et nommez-le ```index.js```.
1. Installez les packages nécessaires à l’aide de l’une des options ```npm install``` :
    * Mongoose : ```npm install mongoose --save```
    * Dotenv (si vous voulez charger vos secrets à partir d’un fichier .env) : ```npm install dotenv --save```

    >[!Note]
    > L’indicateur ```--save``` ajoute la dépendance au fichier package.json.

1. Importez les dépendances dans votre fichier index.js.
    ```JavaScript
    var mongoose = require('mongoose');
    var env = require('dotenv').load();    //Use the .env file to load the variables
    ```

1. Ajoutez la chaîne de connexion Cosmos DB et le nom Cosmos DB au fichier ```.env```.

    ```JavaScript
    COSMOSDB_CONNSTR={Your MongoDB Connection String Here}
    COSMOSDB_DBNAME={Your DB Name Here}
    ```

1. Connectez-vous à Azure Cosmos DB à l’aide de l’infrastructure Mongoose en ajoutant le code suivant à la fin du fichier index.js.
    ```JavaScript
    mongoose.connect(process.env.COSMOSDB_CONNSTR+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb"); //Creates a new DB, if it doesn't already exist

    var db = mongoose.connection;
    db.on('error', console.error.bind(console, 'connection error:'));
    db.once('open', function() {
    console.log("Connected to DB");
    });
    ```
    >[!Note]
    > Ici, les variables d’environnement sont chargées à l’aide de process.env.{nomVariable} en utilisant le package npm « dotenv ».

    Une fois connecté à Azure Cosmos DB, vous pouvez commencer à configurer des modèles d’objet dans Mongoose.

## <a name="caveats-to-using-mongoose-with-azure-cosmos-db"></a>Avertissements concernant l’utilisation de Mongoose avec Azure Cosmos DB

Pour chaque modèle que vous créez, Mongoose crée une collection MongoDB en arrière-plan. Toutefois, compte tenu du modèle de facturation par collection d’Azure Cosmos DB, cette solution n’est peut-être pas la plus rentable si vous disposez de nombreux objets faiblement remplis.

Cette procédure pas à pas traite des deux modèles. Nous aborderons tout d’abord la procédure relative au stockage d’un type de données par collection. Il s’agit du comportement de facto pour Mongoose.

Mongoose propose également un concept appelé [Discriminateurs](http://mongoosejs.com/docs/discriminators.html). Il s’agit d’un mécanisme d’héritage de schéma. Les discriminateurs vous permettent d’avoir plusieurs modèles avec des schémas superposés sur la même collection MongoDB sous-jacente.

Vous pouvez stocker les différents modèles de données dans la même collection, puis utiliser une clause de filtre au moment de la requête pour extraire uniquement les données nécessaires.

### <a name="one-collection-per-object-model"></a>Une collection par modèle d’objet

Le comportement par défaut de Mongoose consiste à créer une collection MongoDB chaque fois que vous créez un modèle d’objet. Cette section explique comment y parvenir avec MongoDB pour Azure Cosmos DB. Cette méthode est recommandée avec Azure Cosmos DB lorsque vos modèles d’objet contiennent de grandes quantités de données. Il s’agit du modèle de fonctionnement par défaut pour Mongoose, que vous devez déjà connaître si vous connaissez Mongoose.

1. Ouvrez de nouveau votre ```index.js```.

1. Créez la définition de schéma pour « Family ».

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Créez un objet pour « Family ».

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Pour finir, nous allons enregistrer l’objet dans Azure Cosmos DB. Cette opération crée une collection en arrière-plan.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Nous allons ensuite créer un autre schéma et un objet. Nous allons le consacrer aux destinations de vacances (« Vacation Destinations ») susceptibles d’intéresser les familles.
    1. Comme la dernière fois, nous allons créer le schéma.
    ```JavaScript
    const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
        name: String,
        country: String
    }));
    ```

    1. Créez un exemple d’objet (vous pouvez en ajouter plusieurs à ce schéma) et enregistrez-le.
    ```JavaScript
    const vacaySpot = new VacationDestinations({
        name: "Honolulu",
        country: "USA"
    });

    vacaySpot.save((err, saveVacay) => {
        console.log(JSON.stringify(saveVacay));
    });
    ```

1. Dans le portail Azure, vous remarquez que deux collections ont été créées dans Azure Cosmos DB.

    ![Didacticiel Node.js - Capture d’écran du portail Azure montrant un compte Azure Cosmos DB avec plusieurs noms de la collection en surbrillance (Node database)][mutiple-coll]

1. Pour finir, nous allons lire les données à partir d’Azure Cosmos DB. Étant donné que nous utilisons le modèle de fonctionnement par défaut de Mongoose, la lecture est identique à n’importe quelle autre lecture avec Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Utiliser les discriminateurs Mongoose pour stocker des données dans une seule collection

Dans cette méthode, nous utilisons les [discriminateurs Mongoose](http://mongoosejs.com/docs/discriminators.html) pour optimiser les coûts de chaque collection Azure Cosmos DB. Les discriminateurs vous permettent de définir une « clé » de différenciation destinée à stocker, à différencier et à filtrer les différents modèles d’objet.

Nous créons ici un modèle d’objet de base, définissons une clé de différenciation et ajoutons « Family » et « VacationDestinations » comme extension au modèle de base.

1. Commençons par définir la configuration de base et la clé du discriminateur.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Nous allons ensuite définir le modèle d’objet commun

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Nous définissons maintenant le modèle « Family ». Notez que nous utilisons ```commonModel.discriminator``` au lieu de ```mongoose.model```. Nous ajoutons également la configuration de base au schéma Mongoose. L’élément discriminatorKey est donc ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. De la même manière, nous allons ajouter un autre schéma, cette fois-ci pour « VacationDestinations ». Ici, l’élément discriminatorKey est ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Pour finir, nous allons créer des objets pour le modèle, puis l’enregistrer.
    1. Commençons par ajouter un ou plusieurs objets au modèle « Family ».
    ```JavaScript
    const family_common = new Family_common({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Blackie" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });

    family_common.save((err, saveFamily) => {
        console.log("Saved: " + JSON.stringify(saveFamily));
    });
    ```

    1. Nous ajoutons ensuite un ou plusieurs objets au modèle « VacationDestinations », avant de l’enregistrer.
    ```JavaScript
    const vacay_common = new Vacation_common({
        name: "Honolulu",
        country: "USA"
    });

    vacay_common.save((err, saveVacay) => {
        console.log("Saved: " + JSON.stringify(saveVacay));
    });
    ```

1. De retour dans le portail Azure, vous remarquez qu’il n’existe qu’une seule collection appelée ```alldata``` contenant les données de « Family » et de « VacationDestinations ».

    ![Didacticiel Node.js - Capture d’écran du portail Azure montrant un compte Azure Cosmos DB avec le nom de la collection en surbrillance (Node database)][alldata]

1. Vous remarquez également que chaque objet possède un autre attribut nommé ```__type```, qui vous permet de différencier les deux modèles d’objet.

1. Pour finir, nous allons lire les données stockées dans Azure Cosmos DB. Mongoose s’occupe de filtrer les données en fonction du modèle. Vous n’avez donc rien d’autre à faire lors de la lecture des données. Il vous suffit de spécifier votre modèle (dans cet exemple, ```Family_common```) et Mongoose se charge d’appliquer un filtrage en fonction de l’élément « DiscriminatorKey ».

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Comme vous pouvez le constater, il est très simple d’utiliser des discriminateurs Mongoose. Si votre application utilise l’infrastructure Mongoose, ce didacticiel vous permet de préparer votre application sur l’API MongoDB dans Azure Cosmos DB sans nécessiter trop de modifications.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>étapes suivantes

Pour en savoir plus sur les opérations MongoDB, les opérateurs, les étapes, les commandes et les options prises en charge par l’API MongoDB Azure Cosmos DB, consultez [Prise en charge de l’API MongoDB pour la syntaxe et les fonctionnalités de MongoDB](mongodb-feature-support.md).

[alldata]: ./media/mongodb-mongoose/mongo-collections-alldata.png
[mutiple-coll]: ./media/mongodb-mongoose/mongo-mutliple-collections.png