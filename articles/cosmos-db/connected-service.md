---
title: Service connecté Visual Studio pour Azure Cosmos DB
description: Permet aux développeurs de se connecter facilement à leur compte Azure Cosmos DB et de gérer des ressources via les services connectés Visual Studio.
services: cosmos-db
documentationcenter: ''
author: jejiang
manager: DJ
+tags: cosmos-db
editor: Jenny Jiang
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jejiang
ms.openlocfilehash: 93be368d34f02e64d11abe9a04b11272ce18124d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-cosmos-db-visual-studio-connected-service-preview"></a>Azure Cosmos DB : service connecté Visual Studio (version préliminaire)

Les services connectés Visual Studio permettent aux développeurs de se connecter facilement à leur compte Azure Cosmos D et de gérer leurs ressources.

Vous pouvez également utiliser l’Explorateur de données dans le service connecté pour créer des procédures stockées, des fonctions définies par l’utilisateur et des déclencheurs afin d’exécuter la logique métier côté serveur. L’Explorateur de données affiche tous les accès aux données par programmation intégrés disponibles dans les API, mais permet d’accéder facilement à vos données.

## <a name="prerequisites"></a>Prérequis


Vérifiez que vous disposez des éléments suivants :

* Un compte Azure actif. Si vous n’en avez pas, vous pouvez vous inscrire pour bénéficier d’un [compte gratuit](https://azure.microsoft.com/free/). 
* Un compte Azure Cosmos DB. Si vous n’en avez pas encore, suivez les étapes de [création d’un compte Azure Cosmos DB](create-sql-api-dotnet.md) pour en créer un dans le portail Azure, ou consultez la rubrique [Créer un compte Azure Cosmos DB dans l’outil de service connecté](#Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool). 
* Si vous souhaitez utiliser un environnement local à des fins de développement, vous pouvez vous servir de l’[émulateur Azure Cosmos DB](local-emulator.md). L’environnement imite le service Azure Cosmos DB.
* [Visual Studio](http://www.visualstudio.com/).
* Les derniers bits du services connecté Azure Cosmos DB. Vous pouvez télécharger un service connecté Azure Cosmos DB à partir du marketplace Visual Studio, comme illustré dans la capture d’écran suivante. Ouvrez **Visual Studio** sur votre ordinateur. Dans le menu **Outils**, sélectionnez **Extensions et mises à jour...**, puis choisissez **En ligne** / **Visual Studio Marketplace**. Entrez **cosmosdb** pour rechercher les bits.

    Vous pouvez également installer le service connecté Azure Cosmos DB à partir de [Visual Studio Marketplace](https://go.microsoft.com/fwlink/?linkid=858709).

    ![Capture d’écran de téléchargement de bits pour un service connecté](./media/connected-service/connected-service-downloadbits.png) 

Une fois que vous avez téléchargé l’extension Connected Service d’Azure Cosmos DB, fermez Visual Studio pour installer l’extension.

## <a id="SetupVS"></a>Configurer votre solution Visual Studio
1. Ouvrez **Visual Studio** sur votre ordinateur.
2. Dans le menu **Fichier**, sélectionnez **Nouveau**, puis choisissez **Projet**.
3. Dans la boîte de dialogue **Nouveau projet**, sélectionnez **Visual C#** / **Application console (.NET Framework)** ou **Application WPF (.NET Framework)**, nommez votre projet, puis cliquez sur **OK**.

    ![Capture d’écran de la fenêtre Nouveau projet](./media/connected-service/connected-service-new-project.png)
    
## <a name="add-connected-service-and-add-account"></a>Ajouter un service connecté et ajouter un compte
1. Dans l’Explorateur de solutions, cliquez avec le bouton droit de la souris sur le nœud du projet, puis sélectionnez **Ajouter** / **Service connecté**. Vous pouvez aussi cliquer sur le menu **Projet**, puis sélectionner **Ajouter un service connecté**.

    ![Capture d’écran de la fenêtre Ajouter un service connecté](./media/connected-service/connected-service-add-connectedservice-rightclick.png)
2. Dans la page du service connecté, cliquez sur **Services connectés** / **Azure Cosmos DB** pour ouvrir la page **Azure Cosmos DB**.

    ![Capture d’écran de la fenêtre Azure Cosmos DB](./media/connected-service/connected-service-choose-azure-cosmosdb.png)
3. Cliquez sur la flèche bas pour vous connecter pour la première fois ou ajouter un compte. Après la connexion, tous les comptes Azure Cosmos DB sont affichés dans la zone vide. Choisissez un compte Azure Cosmos DB à ajouter à votre projet.

    ![Capture d’écran de la fenêtre de connexion et des compte de base de données](./media/connected-service/connected-service-add-db-account.png)
4. Une fois que vous avez ajouté un compte Azure Cosmos DB, un dossier de service connecté de compte Azure Cosmos DB est ajouté au projet. Vous pouvez ajouter plusieurs comptes Azure Cosmos DB en répétant les étapes 1 à 3.

    ![Capture d’écran de la fenêtre du dossier de service connecté](./media/connected-service/connected-service-add-connectedservice-folder.png)

5. Une fois que vous avez ajouté un service connecté Azure Cosmos DB, modifiez votre projet pour permettre l’accès à Azure Cosmos DB de l’une des manières suivantes :

    * Certains packages nuget requis par le client Azure Cosmos DB sont installés. Vous pouvez les voir à partir de votre fichier de configuration de packages. 

        ![Configuration des nouveaux packages Azure Cosmos DB](./media/connected-service/connected-service-packages-config.png)   
    
    * L’URI et la clé de connexion Azure Cosmos DB sont ajoutées au fichier de configuration de projet (ici, App.config). 

        ![Configuration de la nouvelle application Azure Cosmos DB](./media/connected-service/connected-service-app-config.png) 

## <a name="open-azure-cosmos-db-explorer"></a>Ouvrir l’Explorateur Azure Cosmos DB
1. Cliquez avec le bouton droit de la souris sur le nœud du projet et **ouvrez l’Explorateur Azure Cosmos DB**.

    ![Capture d’écran de la tentative d’ouverture de la fenêtre de l’Explorateur de données](./media/connected-service/connected-service-right-click-open-data-exporer.png)
2. Sur la page de **sélection de compte**, cliquez sur la liste déroulante pour sélectionner un compte Azure Cosmos DB.

    ![Capture d’écran de la fenêtre après ajout d’un service connecté de compte](./media/connected-service/connected-service-open-explorer.png)
3. Cliquez sur **Ouvrir** pour afficher la fenêtre de l’Explorateur de données.

## <a id="Create-an-Azure-Cosmo-DB-account-in-Connected-Service-tool"></a>Créer un compte Azure Cosmos DB dans l’outil de service connecté
1. Sur la page du service connecté, en bas à gauche du volet, cliquez sur **Créer un nouveau compte Cosmos DB** pour ouvrir la page de **création de compte Cosmos DB**.

    ![Capture d’écran de la fenêtre ouverte de création de compte Azure Cosmos DB](./media/connected-service/connected-service-click-new-db-account.png)
2. Sur la page de **création de compte Cosmos DB**, indiquez la configuration souhaitée pour ce compte Azure Cosmos DB.

    Remplissez les champs de la page de **création de compte Cosmos DB** en vous appuyant sur les informations de la capture d’écran suivante. 
 
    ![Nouvelle page Azure Cosmos DB](./media/connected-service/connected-service-create-new-account.png)        
3. Cliquez sur **Créer** pour créer le compte.

## <a name="use-data-explorer"></a>Utiliser l’Explorateur de données

Après l’ouverture de l’Explorateur de données, vous pouvez effectuer les opérations suivantes :
* Créer et supprimer des bases de données
* Créer et supprimer des collections
* Créer, supprimer et filtrer des documents
* Créer et supprimer des procédures stockées
* Créer et supprimer des déclencheurs et des fonctions définies par l’utilisateur pour exécuter la logique métier côté serveur 

![Nouvelle page Azure Cosmos DB](./media/connected-service/connected-service-dataexplorerui.png)

## <a name="demo"></a>Démonstration

Visionnez la vidéo suivante pour savoir comment utiliser un service connecté Azure Cosmos DB dans Visual Studio : [utiliser un service connecté Azure Cosmos DB dans Visual Studio](https://go.microsoft.com/fwlink/?linkid=858711).

## <a name="next-steps"></a>Étapes suivantes
Dans ce document, vous avez découverts les éléments suivants :

> [!div class="checklist"]
> * Création d’un compte Azure Cosmos DB
> * Ajouter un service connecté et ajouter un compte
> * Ouvrir l’Explorateur Azure Cosmos DB
> * Utiliser l’Explorateur de données

Maintenant que les services connectés sont en cours d’exécution avec votre compte Azure Cosmos DB, passez à l’un des didacticiels pour commencer à développer votre solution :

* [Azure CosmosDB: Develop with the SQL API in .NET](tutorial-develop-sql-api-dotnet.md) (Azure Cosmos DB : Développer avec l’API SQL dans .NET).
* [Azure Cosmos DB: SQL API getting started tutorial](sql-api-get-started.md) (Azure Cosmos DB : didacticiel sur la prise en main de l’API SQL).
* Vous voulez effectuer un test des performances et de la mise à l’échelle avec Azure Cosmos DB ? Consultez la page [Test des performances et de la mise à l’échelle avec Azure Cosmos DB](performance-testing.md).
* Découvrez comment [surveiller un compte Azure Cosmos DB](monitor-accounts.md).

