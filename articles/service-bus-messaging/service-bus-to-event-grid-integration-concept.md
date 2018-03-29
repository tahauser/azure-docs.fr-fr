---
title: Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid | Documents Microsoft
description: Description de l’intégration de la messagerie Service Bus et d’Event Grid
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
ms.openlocfilehash: e0c32510ee49b95bc3606ea1efff7e2a6f72799b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Vue d’ensemble de l’intégration d’Azure Service Bus et Event Grid

Azure Service Bus a lancé une nouvelle intégration avec Azure Event Grid. Cette fonctionnalité peut être utilisée lorsque les abonnements ou les files d’attente Service Bus ayant un faible volume de messages n’ont pas besoin d’un récepteur qui interroge les messages en continu. 

Service Bus peut maintenant émettre des événements vers Event Grid lorsque la file d’attente ou l’abonnement contient des messages et qu’aucun récepteur n’est présent. Vous pouvez créer des abonnements Event Grid à vos espaces de noms Service Bus, écouter ces événements et y réagir en démarrant un récepteur. Avec cette fonctionnalité, vous pouvez utiliser Service Bus dans des modèles de programmation réactive.

Pour activer la fonctionnalité, vous avez besoin des éléments suivants :

* Un espace de noms Service Bus Premium avec au moins une file d’attente Service Bus ou une rubrique Service Bus avec au moins un abonnement.
* Un accès Contributeur à l’espace de noms Service Bus.
* En outre, vous avez besoin d’un abonnement Event Grid pour l’espace de noms Service Bus. Cet abonnement reçoit une notification de la part d’Event Grid lui indiquant qu’il y a des messages à relever. Les abonnés classiques peuvent être la fonctionnalité Logic Apps d’Azure App Service, Azure Functions ou un webhook contactant une application web. L’abonné traite ensuite les messages. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Vérifier que vous avez accès Collaborateur

Accédez à votre espace de noms Service Bus et sélectionnez **Contrôle d’accès (IAM)** comme illustré ici :

![1][]

### <a name="events-and-event-schemas"></a>Événements et schémas d’événement

Aujourd’hui, Service Bus envoie des événements pour deux scénarios :

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

En outre, Service Bus utilise les [mécanismes d’authentification](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication) et la sécurité Event Grid standard.

Pour plus d’informations, consultez [Schéma d’événement Azure Event Grid](https://docs.microsoft.com/en-us/azure/event-grid/event-schema).

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

#### <a name="dead-letter-messages-available-event"></a>Événement Messages lettres mortes disponibles

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

### <a name="how-many-events-are-emitted-and-how-often"></a>À quelle fréquence et en quelle quantité sont émis les événements ?

Si plusieurs files d’attente et rubriques ou abonnements sont présents dans l’espace de noms, vous obtenez au moins un événement par file d’attente et un par abonnement. Les événements sont émis immédiatement s’il n’y a aucun message dans l’entité Service Bus et un nouveau message arrive. Ou les événements sont émis toutes les deux minutes, sauf si Service Bus détecte un récepteur actif. Le parcours des messages n’interrompt pas les événements.

Par défaut, Service Bus émet des événements pour toutes les entités dans l’espace de noms. Si vous souhaitez obtenir des événements uniquement pour des entités spécifiques, consultez la section suivante.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Utilisation de filtres pour limiter l’emplacement des événements que vous obtenez

Si vous souhaitez obtenir les événements d’une seule file d’attente ou d’un seul abonnement dans votre espace de noms, vous pouvez utiliser les filtres *Commence par* ou *Se termine par* fournis par Event Grid. Dans certaines interfaces, les filtres sont appelés des filtres de *Préfixe* et de *Suffixe*. Si vous souhaitez obtenir les événements de plusieurs files d’attente et abonnements, mais pas tous, vous pouvez créer plusieurs abonnements Event Grid et fournir un filtre pour chacun.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Créer des abonnements Event Grid pour les espaces de noms Service Bus

Il existe trois méthodes pour créer des abonnements Event Grid pour les espaces de noms Service Bus :

* Dans le [portail Azure](#portal-instructions) :
* Dans [Azure CLI](#azure-cli-instructions)
* Dans [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Instructions pour le portail Azure

Pour créer un abonnement Event Grid, procédez comme suit :
1. Accédez à votre espace de noms dans le portail Azure.
2. Dans le volet gauche, sélectionnez **Event Grid**. 
3. Sélectionnez **Abonnement aux événements**.  

   L’image suivante affiche un espace de noms qui a quelques abonnements Event Grid :

   ![20][]

   L’image suivante montre comment s’abonner à une fonction ou à un webhook sans aucun filtrage spécifique :

   ![21][]

## <a name="azure-cli-instructions"></a>Instructions Azure CLI

Tout d’abord, assurez-vous que vous avez Azure CLI version 2.0 ou ultérieure installée. [Téléchargez le programme d’installation](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Sélectionnez **Windows + X**, puis ouvrez une nouvelle console PowerShell avec des autorisations administrateur. Vous pouvez également utiliser un interpréteur de commandes dans le portail Azure.

Exécutez le code suivant :

 ```azurecli-interactive
az login

az account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>Instructions PowerShell

Vérifiez qu’Azure PowerShell est installé. [Téléchargez le programme d’installation](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.4.0). Sélectionnez **Windows + X**, puis ouvrez une nouvelle console PowerShell avec des autorisations administrateur. Vous pouvez également utiliser un interpréteur de commandes dans le portail Azure.

```PowerShell-interactive
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

* Obtenir des [exemples](service-bus-to-event-grid-integration-example.md) Service Bus et Event Grid.
* En savoir plus sur [Event Grid](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Apprenez-en plus sur [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Découvrez plus en détail les [applications logiques](https://docs.microsoft.com/en-us/azure/logic-apps/).
* En savoir plus sur [Service Bus](https://docs.microsoft.com/en-us/azure/azure-functions/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
