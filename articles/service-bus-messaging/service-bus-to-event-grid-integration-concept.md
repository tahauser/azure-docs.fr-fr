---
title: "Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid | Documents Microsoft"
description: "Description de l’intégration de la messagerie Service Bus et d’Event Grid"
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
ms.openlocfilehash: bf771428505081cb60ca4417f87a4f6c2afbd25d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-overview"></a>Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid

Azure Service Bus a lancé une nouvelle intégration avec Azure Event Grid. Cette fonctionnalité peut être utilisée lorsque les abonnements ou les files d’attente Service Bus ayant un faible volume de messages n’ont pas de récepteur pour interroger les messages à tout moment. Service Bus peut maintenant émettre des événements vers Azure Event Grid lorsque la file d’attente ou l’abonnement contient des messages et qu’aucun récepteur n’est présent. Vous pouvez créer des abonnements Azure Event Grid à vos espaces de noms Service Bus, écouter ces événements et y réagir en démarrant un récepteur. Avec cette fonctionnalité, vous pouvez utiliser Service Bus dans des modèles de programmation réactive.

Pour activer la fonctionnalité, vous avez besoin des éléments suivants :

* Un espace de noms Azure Service Bus Premium avec au moins une file d’attente Service Bus ou une rubrique Service Bus avec au moins un abonnement.
* Un accès Collaborateur à l’espace de noms Azure Service Bus.
* En outre, vous avez besoin d’un abonnement Azure Event Grid pour l’espace de noms Service Bus. Cet abonnement reçoit une notification de la part d’Azure Event Grid lui indiquant qu’il y a des messages à relever. Les abonnés classiques peuvent être Logic Apps, Azure Functions ou un Webhook contactant une application web, qui traite ensuite les messages. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Vérifier que vous avez accès Collaborateur

Accédez à votre espace de noms Service Bus et sélectionnez « Contrôle d’accès (IAM) » comme illustré ci-dessous :

![1][]

### <a name="events-and-event-schemas"></a>Événements et schémas d’événement

Aujourd’hui, Azure Service Bus envoie des événements pour deux scénarios.

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

En outre, il utilise les [mécanismes d’authentification](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication) et la sécurité Azure Event Grid standard.

Pour plus d’informations sur les schémas d’événement Event Grid, suivez [ce](https://docs.microsoft.com/en-us/azure/event-grid/event-schema) lien.

#### <a name="active-messages-available-event"></a>Événement Messages actifs disponibles

Cet événement est généré si des messages actifs sont présents dans une file d’attente ou un abonnement et qu’aucun récepteur n’est à l’écoute.

Le schéma de cet événement est le suivant :

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-lettered-messages-available-event"></a>Événement Messages lettres mortes disponibles

Vous obtenez au moins un événement par file d’attente de lettres mortes, comportant des messages et aucun récepteur actif.

Le schéma de cet événement est le suivant :

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-often-and-how-many-events-are-emitted"></a>À quelle fréquence et en quelle quantité sont émis les événements ?

Si plusieurs files d’attente et rubriques/abonnements sont présents dans l’espace de noms, vous obtenez au moins un événement par file d’attente et un par abonnement. Les événements sont émis immédiatement s’il n’y a aucun message dans l’entité Service Bus et qu’un nouveau message arrive. Sinon, ils sont émis toutes les deux minutes, sauf si Azure Service Bus détecte un récepteur actif. Le parcours des messages n’interrompt pas les événements.

Par défaut, Azure Service Bus émet des événements pour toutes les entités dans l’espace de noms. Si vous souhaitez obtenir des événements uniquement pour des entités spécifiques, consultez la section de filtrage suivante.

### <a name="filtering-limiting-from-where-you-get-events"></a>Filtrage, limitation des événements reçus

Si vous souhaitez obtenir les événements d’une seule file d’attente ou d’un seul abonnement dans votre espace de noms, par exemple, vous pouvez utiliser les filtres « Commence par » ou « Se termine par » fournis par Azure Event Grid. Dans certaines interfaces, les filtres sont appelés des filtres de « Préfixe » et de « Suffixe ». Si vous souhaitez obtenir les événements de plusieurs files d’attente et abonnements, mais pas tous, vous pouvez créer plusieurs abonnements Azure Event Grid différents et fournir un filtre pour chacun.

## <a name="how-to-create-azure-event-grid-subscriptions-for-service-bus-namespaces"></a>Création d’abonnements Azure Event Grid pour les espaces de noms Service Bus

Il existe trois méthodes pour créer des abonnements Event Grid pour les espaces de noms Service Bus.

* [Le portail Azure](#portal-instructions)
* [interface de ligne de commande Azure](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="portal-instructions"></a>Instructions pour le portail

Pour créer un nouvel abonnement Azure Event Grid, accédez à votre espace de noms dans le portail Azure, puis sélectionnez le panneau Event Grid. La capture d’écran « + Abonnement aux événements » suivante montre un espace de noms, qui dispose déjà de plusieurs abonnements Event Grid.

![20][]

La capture d’écran suivante montre un exemple d’abonnement à une fonction Azure ou à un Webhook sans aucun filtrage spécifique :

![21][]

## <a name="azure-cli-instructions"></a>Instructions Azure CLI

Tout d’abord, assurez-vous qu’Azure CLI version 2.0 au minimum est installé. Vous pouvez télécharger le programme d’installation ici. Appuyez sur « Windows + X », puis ouvrez une nouvelle console PowerShell avec des autorisations Administrateur. Vous pouvez également utiliser un interpréteur de commandes dans le portail Azure.

Exécutez le code suivant :

```PowerShell
Az login

Aa account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>Instructions PowerShell

Vérifiez qu’Azure PowerShell est installé. Vous pouvez le trouver ici. Appuyez sur « Windows + X », puis ouvrez une nouvelle console PowerShell avec des autorisations Administrateur. Vous pouvez également utiliser un interpréteur de commandes dans le portail Azure.

```PowerShell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

À ce stade, vous pouvez explorer les autres options d’installation ou [vérifier que les événements sont transmis](#test-that-events-are-flowing).

## <a name="next-steps"></a>Étapes suivantes

* [Exemples](service-bus-to-event-grid-integration-example.md) Service Bus et Event Grid.
* Apprenez-en plus sur [Azure Event Grid](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Apprenez-en plus sur [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Apprenez-en plus sur [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/).
* En savoir plus sur [Azure Service Bus](https://docs.microsoft.com/en-us/azure/azure-functions/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png