---
title: "Exemples d’intégration d’Azure Service Bus et d’Event Grid | Documents Microsoft"
description: "Exemples d’intégration de la messagerie Service Bus et d’Event Grid"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 2a4d17673340d145de9a3514f920c74f7eebf6b6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Exemples d’intégration d’Azure Service Bus et d’Azure Event Grid

Dans ce document, vous allez apprendre à configurer des fonctions Azure et une application logique pour recevoir des messages en fonction de l’événement reçu à partir d’Event Grid. L’exemple suppose qu’une rubrique Service Bus avec deux abonnements existe déjà et que vous créez l’abonnement Event Grid de façon à envoyer des événements uniquement pour un abonnement Service Bus. Ensuite, vous envoyez des messages à la rubrique Service Bus et vérifiez que l’événement est généré pour cet abonnement Service Bus. Puis, la fonction ou l’application logique reçoit les messages à partir de cet abonnement Service Bus et les marque comme terminés.

* Vérifiez au préalable que toutes les [conditions préalables](#prerequisites) sont remplies.
* Créez une [fonction Azure test simple](#test-function-setup) pour déboguer et voir le flux initial d’événements à partir d’Event Grid.  **Que vous exécutiez les étapes 3 et 4 ou non, vous devez suivre cette procédure.**
* Créez une [fonction Azure pour recevoir et traiter les messages Service Bus](#receive-messages-using-azure-function) en fonction des événements Event Grid.
* Utilisez [Logic Apps](#receive-messages-using-azure-logic-app).

## <a name="prerequisites"></a>Prérequis

### <a name="service-bus-namespace"></a>Espace de noms Service Bus

Créez un espace de noms Service Bus Premium. Créez une rubrique Service Bus avec deux abonnements.

### <a name="code-to-send-message-to-the-service-bus-topic"></a>Code pour envoyer un message à la rubrique Service Bus

Vous pouvez utiliser la méthode de votre choix pour envoyer un message vers votre rubrique Service Bus. Sinon, vous pouvez utiliser l’exemple ci-dessous. L’exemple de code suppose que vous utilisez Visual Studio 2017.

Clonez [ce référentiel GitHub](https://github.com/Azure/azure-service-bus/).

Accédez au dossier suivant :

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration et ouvrez le fichier : SBEventGridIntegration.sln.

Ensuite, accédez au projet MessageSender et ouvrez Program.cs.

![8][]

Entrez le nom de la rubrique et de la chaîne de connexion, puis exécutez l’application de console :

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>Configuration de la fonction de test

Avant d’étudier le scénario dans son intégralité, il est préférable de créer au moins une petite fonction test, que vous pouvez utiliser pour déboguer et voir quels événements sont transmis.

Dans le portail, créez une application Azure Function. Suivez ce [lien](https://docs.microsoft.com/en-us/azure/azure-functions/) pour découvrir les notions de base relatives à Azure Functions.

Dans la fonction que vous venez de créer, clique sur le petit plus pour ajouter une fonction de déclenchement :

![2][]

![3][]

Ensuite, copiez le code suivant dans la fonction :

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

Cliquez sur Enregistrer et exécuter.

## <a name="connect-function-and-namespace-via-event-grid"></a>Connecter une fonction et un espace de noms via Event Grid

Dans l’étape suivante, vous allez relier la fonction et l’espace de noms Service Bus ensemble. Pour cet exemple, utilisez le portail Azure. Consultez la page [concepts] (service-bus-to-event-grid-integration-concept.md pour savoir comment utiliser PowerShell ou Azure CLI pour obtenir le même résultat.

Pour créer un nouvel abonnement Azure Event Grid, accédez à votre espace de noms dans le portail Azure, puis sélectionnez le panneau Event Grid. Cliquez sur « + Abonnement aux événements ».

La capture d’écran suivante montre un espace de noms, qui dispose déjà de plusieurs abonnements Event Grid.

![20][]

La capture d’écran suivante montre comment s’abonner à une fonction Azure ou à un Webhook sans aucun filtrage spécifique. N’oubliez pas d’ajouter le filtre de votre abonnement Service Bus en tant que « Filtre de suffixe » :

![21][]

Envoyez un message à votre rubrique Service Bus, comme indiqué dans les conditions préalables, et vérifiez que les événements sont transmis via la fonctionnalité de surveillance d’Azure Function.

![9.][]

### <a name="receive-messages-using-azure-function"></a>Recevoir des messages à l’aide d’Azure Function

Dans la section précédente, vous avez étudié un scénario de test simple et de débogage, et vérifié que les événements sont transmis. Dans cette partie de la documentation, vous allez étudier la réception et le traitement des messages lors de la réception d’un événement.

Nous utilisons cette méthode pour ajouter une fonction Azure, car les fonctions Service Bus d’Azure Functions ne prennent pas encore en charge la nouvelle intégration Event Grid de façon native.

Dans la solution Visual Studio que vous avez ouverte conformément aux conditions préalables, sélectionnez ReceiveMessagesOnEvent.cs. Entrez votre chaîne de connexion dans le code :

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

Ensuite, accédez au portail Azure et téléchargez le profil de publication pour la fonction Azure créée au préalable dans [2. Configuration de la fonction de test](#2-test-function-setup).

![11][]

Puis, dans Visual Studio, cliquez avec le bouton droit sur SBEventGridIntegration et sélectionnez Publier. Utilisez le profil de publication téléchargé au préalable, sélectionnez Importer un profil, puis cliquez sur Publier.

![12][]

Une fois la nouvelle fonction Azure publiée, créez un nouvel abonnement Azure Event Grid pointant vers elle. Assurez-vous que vous appliquez le filtre « se termine par » approprié, normalement le nom de votre abonnement Service Bus.

Ensuite, envoyez un message à la rubrique Service Bus créée au préalable, puis vérifiez dans le journal de la fonction Azure sur le portail que les événements sont transmis et les messages sont reçus.

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Recevoir des messages à l’aide d’Azure Logic App

Les instructions suivantes montrent comment connecter Azure Logic App à Azure Service Bus et Azure Event Grid :

Tout d’abord, créez une application logique dans le portail Azure et sélectionnez Event Grid comme action de démarrage.

![13.][]

Dans le Concepteur d’applications logiques, la vue initiale doit ressembler à la capture d’écran suivante, où vous devez remplacer « Nom de la ressource » par votre propre nom d’espace de noms. Assurez-vous également de développer des options avancées et d’ajouter le filtre de suffixe pour votre abonnement :

![14][]

Ensuite, ajoutez une action de réception Service Bus à partir d’un abonnement à la rubrique. L’action finale doit ressembler à la capture d’écran suivante.

![15][]

Ensuite, ajoutez un événement complet, qui doit ressembler à ceci.

![16][]

Enregistrez l’application logique et envoyez un message à votre rubrique Service Bus, comme indiqué dans les conditions préalables. Puis, observez l’exécution des applications logiques. En cliquant sur « Présentation », puis sur « Historique des exécutions », vous pouvez obtenir plus de données sur l’exécution.

![17][]

![18][]

## <a name="next-steps"></a>étapes suivantes

* Apprenez-en plus sur [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Apprenez-en plus sur [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Apprenez-en plus sur [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/).
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
