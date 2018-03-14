---
title: "Créer une fonction déclenchée par Azure Cosmos DB | Microsoft Docs"
description: "Utilisez Azure Functions pour créer une fonction sans serveur qui est appelée quand des données sont ajoutées à une base de données dans Azure Cosmos DB."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/07/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 9ee3eddda53052c47f2cecff80f971f5eadac414
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Créer une fonction déclenchée par Azure Cosmos DB

Découvrez comment créer une fonction qui est déclenchée quand des données sont ajoutées à Azure Cosmos DB ou y sont changées. Pour découvrir plus en détail Azure Cosmos DB, consultez [Azure Cosmos DB : traitement de base de données sans serveur à l’aide d’Azure Functions](..\cosmos-db\serverless-computing-database.md).

![Affichage du message dans les journaux.](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>configuration requise

Pour suivre ce didacticiel :

+ Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Création d’une application Azure Function

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Créez ensuite une fonction dans la nouvelle Function App.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Créer le déclencheur Azure Cosmos DB

1. Développez votre Function App, puis cliquez sur le bouton **+** en regard de **Fonctions**. S’il s’agit de la première fonction de votre Function App, sélectionnez **Fonction personnalisée**. Cela affiche l’ensemble complet des modèles de fonction.

    ![Page de démarrage rapide des fonctions sur le portail Azure](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

2. Dans la zone de recherche, saisissez `cosmos`, puis sélectionnez la langue souhaitée pour le modèle déclencheur d’Azure Cosmos DB.

    ![Sélectionner le déclencheur d’Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/select-cosmos-db-trigger-portal.png)

3. Configurez le nouveau déclencheur avec les paramètres comme spécifié dans le tableau situé sous l’image.

    ![Créer la fonction déclenchée par Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)
    
    | Paramètre      | Valeur suggérée  | Description                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Name** | Default | Utilisez le nom de fonction par défaut suggéré par le modèle. |
    | **Nom de la collection** | Éléments | Nom de la collection à surveiller. |
    | **Créer la collection de baux si elle n’existe pas** | Activé | La collection n’existe pas, vous devez la créer. |
    | **Nom de la base de données** | Tâches | Nom de la base de données avec la collection à surveiller. |

4. Sélectionnez **Nouveau** en regard du libellé **Connexion au compte Azure Cosmos DB**, puis choisissez un compte Cosmos DB existant ou cliquez sur **+ Créer**. 
 
    ![Configurer la connexion au compte Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-create-CosmosDB.png)

6. Lorsque vous créez un compte Cosmos DB, utilisez les paramètres du **nouveau compte** comme indiqué dans la table.

    | Paramètre      | Valeur suggérée  | Description                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Identifiant** | Nom de base de données | ID unique de la base de données Azure Cosmos DB  |
    | **API** | SQL (DocumentDB) | Cette rubrique utilise l’API de base de données de document.  |
    | **Abonnement** | Abonnement Azure | Abonnement sous lequel est créé ce nouveau compte Cosmos DB.  |
    | **Groupe de ressources** | myResourceGroup |  Utilisez le groupe de ressources existant qui contient votre application de fonction. |
    | **Lieu**  | WestEurope | Sélectionnez un emplacement près de votre application de fonction ou d’autres applications qui utilisent les documents stockés.  |

6. Cliquez sur **OK** pour créer la base de données. La création de la base de données peut prendre quelques minutes. Une fois la base de données créée, la chaîne de connexion de base de données est stockée en tant que paramètre d’application de fonction. Le nom de ce paramètre d’application est inséré dans **Connexion au compte Azure Cosmos DB**. 

7. Cliquez sur **Créer** pour créer votre fonction déclenchée par Azure Cosmos DB. Une fois la fonction créée, le code de fonction basé sur le modèle s’affiche.  

    ![Modèle de fonction Cosmos DB en C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Ce modèle de fonction écrit le nombre de documents et le premier ID de document dans les journaux. 

Ensuite, vous vous connectez à votre compte Azure Cosmos DB et créez la collection **Tasks** dans la base de données. 

## <a name="create-the-items-collection"></a>Créer la collection d’éléments

1. Ouvrez une deuxième instance du [portail Azure](https://portal.azure.com) sous un nouvel onglet dans le navigateur. 

2. Sur le côté gauche du portail, développez la barre d’icônes, tapez `cosmos` dans le champ de recherche, puis sélectionnez **Azure Cosmos DB**.

    ![Rechercher le service Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

2. Sélectionnez votre compte Azure Cosmos DB, puis sélectionnez **Explorateur de données**. 
 
3. Dans **Collections**, choisissez **taskDatabase**, puis sélectionnez **Nouvelle collection**.

    ![Création d'une collection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

4. Dans **Ajouter une collection**, utilisez les paramètres présentés dans le tableau situé sous l’image. 
 
    ![Définir la collection taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)
 
    | Paramètre|Valeur suggérée|Description |
    | ---|---|--- |
    | **ID de base de données** | Tâches |Nom de votre nouvelle base de données. Ce nom doit correspondre au nom défini dans votre liaison de fonction. |
    | **ID de la collection** | Éléments | Nom de la nouvelle collection. Ce nom doit correspondre au nom défini dans votre liaison de fonction.  |
    | **Capacité de stockage** | Fixe (10 Go)|Utilisez la valeur par défaut. Cette valeur correspond à la capacité de stockage de la base de données. |
    | **Débit** |400 RU| Utilisez la valeur par défaut. Si vous souhaitez réduire la latence, vous pourrez augmenter le débit par la suite. |
    | **[Clé de partition](../cosmos-db/partition-data.md#design-for-partitioning)** | /category|Clé de partition qui distribue les données uniformément à chaque partition. Il est important de sélectionner la clé de partition correcte pour obtenir une collection performante. | 

1. Cliquez sur **OK** pour créer la collection **Tasks**. La création de la collection peut prendre un peu de temps.

Une fois créée la collection spécifiée dans la liaison de fonction, vous pouvez tester la fonction en ajoutant des documents à cette nouvelle collection.

## <a name="test-the-function"></a>Tester la fonction

1. Développez la nouvelle collection **taskCollection** dans l’Explorateur de données, choisissez **Documents**, puis sélectionnez **Nouveau document**.

    ![Créer un document dans taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

2. Remplacez le contenu du nouveau document par le contenu suivant, puis choisissez **Enregistrer**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. Passez au premier onglet de navigateur qui contient votre fonction dans le portail. Développez les journaux de la fonction et vérifiez que le nouveau document a déclenché la fonction. La valeur d’ID de document `task1` doit être écrite dans les journaux. 

    ![Affichage du message dans les journaux.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

4. (Facultatif) Accédez à votre document, apportez une modification, puis cliquez sur **Mettre à jour**. Ensuite, revenez aux journaux de la fonction, puis vérifiez que la mise à jour a également déclenché la fonction.

## <a name="clean-up-resources"></a>Supprimer des ressources

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez créé une fonction qui s’exécute quand un document est ajouté ou modifié dans votre base de données Azure Cosmos DB.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Pour plus d’informations sur les déclencheurs Azure Cosmos DB, consultez [Liaisons Azure Cosmos DB pour Azure Functions](functions-bindings-cosmosdb.md).
