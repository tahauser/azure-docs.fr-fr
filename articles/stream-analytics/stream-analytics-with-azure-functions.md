---
title: "Exécuter Azure Functions avec des travaux Azure Stream Analytics | Microsoft Docs"
description: "Apprenez à configurer Azure Functions comme récepteur de sortie pour les travaux Stream Analytics."
keywords: "sortie de données, diffusion en continu de données, Azure Functions"
documentationcenter: 
services: stream-analytics
author: SnehaGunda
manager: kfile
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/19/2017
ms.author: sngun
ms.openlocfilehash: ab095827dc9dbfee19284abfbac353b16d3239a7
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2018
---
# <a name="run-azure-functions-with-azure-stream-analytics-jobs"></a>Exécuter Azure Functions avec des travaux Azure Stream Analytics 
 
> [!IMPORTANT]
> Cette fonctionnalité n’existe qu’en version préliminaire.

Vous pouvez exécuter Azure Functions avec Azure Stream Analytics en configurant Functions comme l’un des récepteurs de sortie du travail Stream Analytics. Functions offre une expérience de calcul à la demande basée sur des événements, qui vous permet d’implémenter le code qui est déclenché par les événements qui se produisent dans Azure ou dans des services tiers. Comme ce logiciel peut répondre à des déclencheurs, il constitue l’outil de sortie logique pour des travaux Stream Analytics.

Stream Analytics appelle Functions via des déclencheurs HTTP. L’adaptateur de sortie de Functions permet aux utilisateurs de connecter Functions à Stream Analytics, de telle sorte que les événements puissent être déclenchés en fonction des requêtes Stream Analytics. 

Ce didacticiel montre comment connecter Stream Analytics au [cache Redis Azure](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md) à l’aide de [Azure Functions](../azure-functions/functions-overview.md). 

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Configurer un travail Stream Analytics pour exécuter une fonction 

Cette section explique comment configurer un travail Stream Analytics pour exécuter une fonction qui écrit des données dans le cache Redis Azure. Le travail Stream Analytics lit les événements d’Azure Event Hubs et exécute une requête qui appelle la fonction. Cette fonction lit les données à partir du travail Stream Analytics et les écrit dans le cache Redis Azure.

![Schéma montrant les relations entre les services Azure](./media/stream-analytics-with-azure-functions/image1.png)

Les étapes suivantes sont nécessaires pour exécuter cette tâche :
* [Créer un travail Stream Analytics en utilisant Event Hubs comme entrée](#create-stream-analytics-job-with-event-hub-as-input)  
* [Créer une instance de cache Redis Azure](#create-an-azure-redis-cache)  
* [Créer une fonction dans Azure Functions qui permet d’écrire des données dans le cache Redis Azure](#create-an-azure-function-that-can-write-data-to-the-redis-cache)    
* [Mettre à jour le travail Stream Analytics en utilisant la fonction comme sortie](#update-the-stream-analytic-job-with-azure-function-as-output)  
* [Vérifier les résultats dans le cache Redis Azure](#check-redis-cache-for-results)  

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Créer un travail Stream Analytics en utilisant Event Hubs comme entrée

Suivez le didacticiel [Détection des fraudes en temps réel](stream-analytics-real-time-fraud-detection.md) pour créer un concentrateur d’événements, démarrer l’application de générateur d’événements et créer un travail Stream Analytics. (Ignorez les étapes de création de la requête et de la sortie. Consultez plutôt les sections suivantes pour configurer la sortie Functions.)

## <a name="create-an-azure-redis-cache-instance"></a>Créer une instance de cache Redis Azure

1. Pour créer un cache dans le Cache Redis Azure, suivez les étapes décrites dans la section [Création d’un cache](../redis-cache/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).  

2. Une fois le cache créé, sous **Paramètres**, sélectionnez **Clés d’accès**. Notez la **chaîne de connexion primaire**.

   ![Capture d’écran de la chaîne de connexion du cache Redis Azure](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-redis-cache"></a>Créer une fonction dans Azure Functions qui permet d’écrire des données dans le cache Redis Azure

1. Consultez la section [Créer une application de fonction](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) de la documentation Functions. Cette section vous explique comment créer une application de fonction et une [fonction déclenchée par HTTP dans Azure Functions](../azure-functions/functions-create-first-azure-function.md#create-function) en utilisant le langage CSharp.  

2. Accédez à la fonction **run.csx**. Mettez-la à jour avec le code suivant. (Veillez à remplacer « \<your redis cache connection string goes here\> » par la chaîne de connexion primaire du cache Redis Azure que vous avez récupérée dans la section précédente.)  

   ```c#
   using System;
   using System.Net;
   using System.Threading.Tasks;
   using StackExchange.Redis;
   using Newtonsoft.Json;
   using System.Configuration;

   public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
   {
      log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
      // Get the request body
      dynamic dataArray = await req.Content.ReadAsAsync<object>();

      // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

      if (dataArray.ToString().Length > 262144)
      {        
         return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
      var connection = ConnectionMultiplexer.Connect("<your redis cache connection string goes here>");
      log.Info($"Connection string.. {connection}");
    
      // Connection refers to a property that returns a ConnectionMultiplexer
      IDatabase db = connection.GetDatabase();
      log.Info($"Created database {db}");
    
      log.Info($"Message Count {dataArray.Count}");

      // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
      for (var i = 0; i < dataArray.Count; i++)
      {
        string time = dataArray[i].time;
        string callingnum1 = dataArray[i].callingnum1;
        string key = time + " - " + callingnum1;
        db.StringSet(key, dataArray[i].ToString());
        log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
      // Simple get of data types from the cache
      string value = db.StringGet(key);
      log.Info($"Database got: {value}");
      }

      return req.CreateResponse(HttpStatusCode.OK, "Got");
    }    

   ```

   Lorsque Stream Analytics reçoit une erreur « HTTP Request Entity Too Large » de la part de la fonction, il réduit la taille des lots envoyés à Functions. Dans votre fonction, utilisez le code suivant pour vérifier que Stream Analytics n’envoie pas de lots de taille excessive. Vérifiez également que les valeurs de taille et de nombre de lots maximum utilisées dans la fonction correspondent à celles qui ont été saisies dans le portail Stream Analytics.

   ```c#
   if (dataArray.ToString().Length > 262144)
      {        
        return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
      }
   ```

3. Dans un éditeur de texte de votre choix, créez un fichier JSON nommé **project.json**. Utilisez le code suivant et enregistrez-le sur votre ordinateur local. Ce fichier contient les dépendances du package NuGet dont a besoin la fonction C#.  
   
   ```json
       {
         "frameworks": {
             "net46": {
                 "dependencies": {
                     "StackExchange.Redis":"1.1.603",
                     "Newtonsoft.Json": "9.0.1"
                 }
             }
         }
     }

   ```
 
4. Revenez au portail Azure. Sous l’onglet **Fonctionnalités de la plateforme**, accédez à votre fonction. Sous **Outils de développement**, sélectionnez **Éditeur App Service**. 
 
   ![Capture d’écran de l’Éditeur App Service](./media/stream-analytics-with-azure-functions/image3.png)

5. Dans l’éditeur App Service, cliquez avec le bouton droit sur votre répertoire racine et téléchargez le fichier **project.json**. Une fois le téléchargement terminé, actualisez la page. Vous devriez maintenant voir un fichier généré automatiquement appelé **project.lock.json**. Ce fichier généré automatiquement contient des références aux fichiers .dll spécifiés dans le fichier project.json.  

   ![Capture d’écran de l’Éditeur App Service](./media/stream-analytics-with-azure-functions/image4.png)

 

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Mettre à jour le travail Stream Analytics en utilisant la fonction comme sortie

1. Dans le portail Azure, ouvrez votre travail Stream Analytics.  

2. Accédez à votre fonction et sélectionnez **Vue d’ensemble** > **Sorties** > **Ajouter**. Pour ajouter une nouvelle sortie, sélectionnez **Azure Function** pour l’option de récepteur. Le nouvel adaptateur de sortie Functions est disponible, avec les propriétés suivantes :  

   |**Nom de la propriété**|**Description**|
   |---|---|
   |Alias de sortie| Nom convivial que vous utilisez dans la requête du travail pour faire référence à la sortie. |
   |Option d’importation| Vous pouvez utiliser la fonction de l’abonnement actuel ou renseigner manuellement les paramètres si la fonction se trouve dans un autre abonnement. |
   |Function App| Nom de votre application Functions. |
   |Fonction| Nom de la fonction dans votre application Functions (nom de votre fonction run.csx).|
   |Taille de lot maximale|Définit la taille maximale de chaque lot de sortie envoyé à votre fonction. Par défaut, cette valeur est définie sur 256 Ko.|
   |Nombre maximal de lots|Spécifie le nombre maximal d’événements dans chaque lot envoyé à la fonction. La valeur par défaut est 100. Cette propriété est facultative.|
   |Clé|Vous permet d’utiliser une fonction d’un autre abonnement. Indiquez la valeur de la clé pour accéder à votre fonction. Cette propriété est facultative.|

3. Indiquez un nom pour l’alias de sortie. Dans ce didacticiel, nous allons le nommer **saop1** (vous pouvez utiliser n’importe quel nom de votre choix). Renseignez les autres détails.  

4. Ouvrez votre travail Stream Analytics et mettez à jour la requête comme suit. (Veillez à remplacer le texte « saop1 » si vous avez donné un autre nom au récepteur de sortie.)  

   ```sql
    SELECT 
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1, 
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Démarrez l’application telcodatagen.exe en exécutant la commande suivante dans la ligne de commande (utilisez le format `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`) :  
   
   **telcodatagen.exe 1000 .2 2**
    
6.  Démarrez le travail Stream Analytics.

## <a name="check-azure-redis-cache-for-results"></a>Vérifier les résultats dans le cache Redis Azure

1. Accédez au portail Azure et recherchez votre cache Redis Azure. Sélectionnez **Console**.  

2. Utilisez les [commandes du cache Redis](https://redis.io/commands) pour vérifier que vos données se trouvent bien dans le cache Redis. (La commande prend le format Get {clé}.) Par exemple : 

   **Get "12/19/2017 21:32:24 - 123414732"**

   Cette commande doit produire la valeur de la clé spécifiée :

   ![Capture d’écran de la sortie du cache Redis Azure](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="known-issues"></a>Problèmes connus

Dans le portail Azure, lorsque vous essayez de rétablir les valeurs maximales de taille et de nombre de lots à des valeurs vides (par défaut), la valeur bascule sur la valeur précédemment saisie au moment de la sauvegarde. Dans ce cas, entrez manuellement les valeurs par défaut pour ces champs.

