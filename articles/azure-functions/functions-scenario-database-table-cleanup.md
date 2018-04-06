---
title: Utiliser Azure Functions pour effectuer une tâche de nettoyage de base de données | Microsoft Docs
description: Utilisez Azure Functions pour planifier une tâche qui se connecte à Azure SQL Database pour nettoyer des lignes périodiquement.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 2947fc6da0c4559e81cf97255b8375b020e0b657
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Utiliser Azure Functions pour se connecter à une base de données Azure SQL Database
Cette rubrique vous montre comment utiliser Azure Functions pour créer une tâche planifiée qui nettoie des lignes dans une table d’une base de données Azure SQL Database. La nouvelle fonction de script C# est créée selon un modèle de déclencheur du minuteur prédéfini dans le Portail Azure. Pour prendre en charge ce scénario, vous devez également définir une chaîne de connexion de base de données comme paramètre d’application dans l’application de fonction. Ce scénario utilise une opération en bloc sur la base de données. 

Pour que votre fonction traite des opérations de création, de lecture, de mise à jour et de suppression individuelles dans une table Mobile Apps, utilisez à la place des [liaisons Mobile Apps](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Prérequis


+ Cette rubrique crée une fonction déclenchée par un minuteur. Suivez les étapes de la rubrique [Créer une fonction dans Azure, qui est déclenchée par un minuteur](functions-create-scheduled-function.md) pour créer une version C# de cette fonction.   

+ Cette rubrique montre une commande Transact-SQL qui exécute une opération de nettoyage en bloc dans la table nommée **SalesOrderHeader** de l’exemple de base de données AdventureWorksLT. Pour créer l’exemple de base de données AdventureWorksLT, effectuez les étapes de la rubrique [Création d’une base de données SQL Azure dans le portail Azure](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Obtenir des informations de connexion

Vous devez obtenir la chaîne de connexion pour la base de données que vous avez créée quand vous avez effectué les étapes de [Création d’une base de données SQL Azure dans le portail Azure](../sql-database/sql-database-get-started-portal.md).

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
 
3. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis sélectionnez votre base de données dans la page **Bases de données SQL**.

4. Sélectionnez **Afficher les chaînes de connexion de la base de données** et copiez la chaîne de connexion **ADO.NET** complète. 

    ![Copiez la chaîne de connexion ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Définir la chaîne de connexion 

Une Function App héberge l’exécution de vos fonctions dans Azure. Il est recommandé de stocker les chaînes de connexion et autres secrets dans vos paramètres de conteneur de fonctions. L’utilisation de paramètres d’application empêche la divulgation accidentelle de la chaîne de connexion avec votre code. 

1. Accédez à l’application de fonction que vous avez créée dans [Créer une fonction dans Azure, qui est déclenchée par un minuteur](functions-create-scheduled-function.md).

2. Sélectionnez **Fonctionnalités de la plateforme** > **Paramètres de l’application**.
   
    ![Paramètres de l’application pour l’application de fonction.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Faites défiler jusqu’à **Chaînes de connexion** et ajoutez une chaîne de connexion en utilisant les paramètres spécifiés dans le tableau.
   
    ![Ajoutez une chaîne de connexion aux paramètres de l’application de fonction.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Paramètre       | Valeur suggérée | Description             | 
    | ------------ | ------------------ | --------------------- | 
    | **Name**  |  sqldb_connection  | Utilisé pour accéder à la chaîne de connexion stockée dans le code de votre fonction.    |
    | **Valeur** | Chaîne copiée  | Collez la chaîne de connexion que vous avez copiée dans la section précédente et remplacez les espaces réservés `{your_username}` et `{your_password}` par des valeurs réelles. |
    | **Type** | Base de données SQL | Utilisez la connexion SQL Database par défaut. |   

3. Cliquez sur **Enregistrer**.

À présent, vous pouvez ajouter le code de fonction C# qui se connecte à votre base de données SQL.

## <a name="update-your-function-code"></a>Mettre à jour le code de votre fonction

1. Dans votre application de fonction du portail, sélectionnez la fonction déclenchée par minuteur.
 
3. Ajoutez les références d’assembly suivantes en haut du code de la fonction de script C# existante :

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >Le code dans ces exemples est le script C# à partir du portail. Lorsque vous développez une fonction C# précompilée localement, vous devez à la place ajouter des références à ces assemblys dans votre projet local.  

3. Ajoutez les instructions `using` suivantes à la fonction :
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Remplacez la fonction `Run` existante par le code suivant :
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Cet exemple de commande met à jour la colonne `Status` en fonction de la date d’envoi. Il doit mettre à jour 32 lignes de données.

5. Cliquez sur **Enregistrer**, surveillez l’exécution suivante de la fonction dans les fenêtres **Journaux**, puis notez le nombre de lignes mises à jour dans la table **SalesOrderHeader**.

    ![Consultez les journaux des fonctions.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez ensuite comment utiliser des fonctions avec Logic Apps pour s’intégrer à d’autres services.

> [!div class="nextstepaction"] 
> [Créer une fonction qui s’intègre avec Logic Apps](functions-twitter-email.md)

Pour plus d’informations sur Functions, consultez les rubriques suivantes :

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)  
  Référence du programmeur pour le codage de fonctions et la définition de déclencheurs et de liaisons.
* [Test d’Azure Functions](functions-test-a-function.md)  
  décrit plusieurs outils et techniques permettant de tester vos fonctions.  
