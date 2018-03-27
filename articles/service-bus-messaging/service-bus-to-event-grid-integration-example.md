---
title: Exemples d’intégration d’Azure Service Bus et d’Event Grid | Documents Microsoft
description: Cet article fournit des exemples d’intégration de la messagerie Service Bus et d’Event Grid.
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: fd30a8eb5149647a24ff04e099bf5c3e187459ef
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-examples"></a>Exemples d’intégration d’Azure Service Bus et d’Azure Event Grid

Dans cet article, vous allez apprendre à configurer une fonction Azure et une application logique pour recevoir des messages en fonction de l’événement reçu à partir d’Azure Event Grid. Vous allez effectuer les opérations suivantes :
 
* Créer une [fonction Azure test](#test-function-setup) simple pour déboguer et voir le flux initial d’événements à partir d’Event Grid. Effectuez cette étape indépendamment des autres.
* Créer une [fonction Azure pour recevoir et traiter les messages Azure Service Bus](#receive-messages-using-azure-function) en fonction des événements Event Grid.
* Utiliser la [fonctionnalité Logic Apps d’Azure App Service](#receive-messages-using-azure-logic-app).

L’exemple que vous créez suppose que la rubrique Service Bus a deux abonnements. L’exemple part également du principe que l’abonnement Event Grid a été créé pour envoyer des événements à un seul abonnement Service Bus. 

Dans l’exemple, vous envoyez des messages à la rubrique Service Bus et vérifiez que l’événement a été généré pour cet abonnement Service Bus. L’application logique ou la fonction reçoit les messages de l’abonnement Service Bus, puis le renseigne.

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que vous avez effectué les étapes décrites dans les deux sections suivantes.

### <a name="create-a-service-bus-namespace"></a>Création d’un espace de noms Service Bus

Créez un espace de noms Service Bus Premium et créez une rubrique Service Bus qui a deux abonnements.

### <a name="send-a-message-to-the-service-bus-topic"></a>Envoyer un message à la rubrique Service Bus

Vous pouvez utiliser la méthode de votre choix pour envoyer un message à votre rubrique Service Bus. L’exemple de code figurant à la fin de cette procédure suppose que vous utilisez Visual Studio 2017.

1. Clonez [le référentiel azure-service-bus GitHub](https://github.com/Azure/azure-service-bus/).

2. Dans Visual Studio, accédez au dossier *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration*, puis ouvrez le fichier *SBEventGridIntegration.sln*.

3. Accédez au projet **MessageSender**, puis sélectionnez **Program.cs**.

   ![8][]

4. Entrez le nom de la rubrique et la chaîne de connexion, puis exécutez le code de l’application de console suivant :

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```

## <a name="set-up-a-test-function"></a>Configurer une fonction de test

Avant d’étudier le scénario dans son intégralité, configurez au moins une petite fonction test, que vous pouvez utiliser pour déboguer et voir quels événements sont transmis.

1. Dans le portail Azure, créez une application Azure Functions. Pour apprendre les notions de base d’Azure Functions, consultez la [documentation Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/).

2. Dans la fonction que vous venez de créer, sélectionnez le signe plus (+) pour ajouter une fonction de déclencheur HTTP :

    ![2][]
    
    La fenêtre **Commencez rapidement avec une fonction prédéfinie** s’ouvre.

    ![3][]

3. Sélectionnez le bouton **Webhook + API**, puis **CSharp**, et enfin **Créer cette fonction**.
 
4. Dans la fonction, collez le code suivant :

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
    IEnumerable<string> headerValues;
    if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
    {
    var validationHeaderValue = headerValues.FirstOrDefault();
    if(validationHeaderValue == "SubscriptionValidation")
    {
    var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
         var code = events[0].Data["validationCode"];
         return req.CreateResponse(HttpStatusCode.OK,
         new { validationResponse = code });
    }
    }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```

5. Sélectionnez **Enregistrer et exécuter**.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Connecter la fonction et l’espace de noms via Event Grid

Dans cette section, vous allez relier la fonction et l’espace de noms Service Bus. Pour cet exemple, utilisez le portail Azure. Pour comprendre comment utiliser PowerShell ou Azure CLI pour effectuer cette procédure, consultez [Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid](service-bus-to-event-grid-integration-concept.md).

Pour créer un abonnement Azure Event Grid, procédez comme suit :
1. Dans le portail Azure, accédez à votre espace de noms et puis, dans le volet gauche, sélectionnez **Event Grid**.  
    La fenêtre de votre espace de noms s’ouvre, avec deux abonnements Event Grid affichés dans le volet droit.

    ![20][]

2. Sélectionnez **Abonnement aux événements**.  
    La fenêtre **Abonnement aux événements** s’ouvre. L’image suivante affiche un formulaire pour s’abonner à une fonction Azure ou à un webhook sans appliquer de filtres.

    ![21][]

3. Remplissez le formulaire comme indiqué et, dans la zone **Filtre de suffixe**, veillez à entrer le filtre approprié.

4. Sélectionnez **Créer**.

5. Envoyez un message à votre rubrique Service Bus, comme indiqué dans la section sur les conditions préalables, et vérifiez que les événements sont transmis via la fonctionnalité de surveillance d’Azure Functions.

L’étape suivante consiste à relier la fonction et l’espace de noms Service Bus. Pour cet exemple, utilisez le portail Azure. Pour comprendre comment utiliser PowerShell ou Azure CLI pour effectuer cette procédure, consultez [Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid](service-bus-to-event-grid-integration-concept.md).

![9.][]

### <a name="receive-messages-by-using-azure-functions"></a>Recevoir des messages à l’aide d’Azure Functions

Dans la section précédente, vous avez étudié un scénario de test et de débogage simple, et vérifié que les événements sont transmis. 

Dans cette section, vous allez apprendre comment recevoir et traiter des messages après avoir reçu un événement.

Vous allez ajouter une fonction Azure comme indiqué dans l’exemple suivant, car les fonctions Service Bus d’Azure Functions ne prennent pas encore en charge la nouvelle intégration Event Grid de façon native.

1. Dans la même solution Visual Studio que vous avez ouverte dans la section sur les conditions préalables, sélectionnez **ReceiveMessagesOnEvent.cs**. 

    ![10][]

2. Entrez votre chaîne de connexion dans le code suivant :

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```

3. Dans le portail Azure, téléchargez le profil de publication pour la fonction Azure que vous avez créée dans la section « Configurer une fonction de test ».

    ![11][]

4. Dans Visual Studio, cliquez avec le bouton droit sur **SBEventGridIntegration** et sélectionnez **Publier**. 

5. Dans le volet **Publier** du profil de publication que vous avez téléchargé précédemment, sélectionnez **Importer le profil**, puis sélectionnez **Publier**.

    ![12][]

6. Une fois la nouvelle fonction Azure publiée, créez un abonnement Azure Event Grid pointant vers celle-ci.  
    Dans la zone **Se termine par**, assurez-vous que vous appliquez le filtre approprié, normalement le nom de votre abonnement Service Bus.

7. Envoyez un message à la rubrique Azure Service Bus créée au préalable, puis vérifiez dans le journal Azure Functions sur le portail Azure que les événements sont transmis et que les messages sont reçus.

    ![12-1][]

### <a name="receive-messages-by-using-logic-apps"></a>Recevoir des messages à l’aide de Logic Apps

Connectez une application logique avec Azure Service Bus et Azure Event Grid en procédant comme suit :

1. Créez une application logique dans le portail Azure et sélectionnez **Event Grid** comme action de démarrage.

    ![13.][]

    La fenêtre du concepteur Logic Apps s’ouvre.

    ![14][]

2. Ajoutez vos informations en procédant comme suit :

    a. Dans la zone **Nom de la ressource**, entrez votre propre nom d’espace de noms. 

    b. Sous **Options avancées**, dans la zone **Filtre de suffixe**, entrez le filtre de votre abonnement.

3. Aoutez une action de réception Service Bus pour recevoir des messages d’un abonnement à la rubrique.  
    L’action finale est indiquée dans l’image suivante :

    ![15][]

4. Ajoutez un événement terminé, comme indiqué dans l’image suivante :

    ![16][]

5. Enregistrez l’application logique et envoyez un message à votre rubrique Service Bus, comme indiqué dans la section sur les conditions préalables.  
    Observez l’exécution de l’application logique. Pour afficher plus de données pour l’exécution, sélectionnez **Vue d’ensemble**, puis affichez les données sous **Historique des exécutions**.

    ![17][]

    ![18][]

## <a name="next-steps"></a>Étapes suivantes

* Apprenez-en plus sur [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Apprenez-en plus sur [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Apprenez-en plus sur [la fonctionnalité Logic Apps d’Azure App Service](https://docs.microsoft.com/azure/logic-apps/).
* En savoir plus sur [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/).


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
