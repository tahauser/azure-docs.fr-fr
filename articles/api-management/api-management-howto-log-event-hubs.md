---
title: "Enregistrement des événements sur Azure Event Hubs dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment enregistrer des événements sur Azure Event Hubs dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 066f151aa96b3a57c86515411ba05a982c10aa5f
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Comment enregistrer des événements sur Azure Event Hubs dans Gestion des API Azure
Les concentrateurs d'événements Azure sont un service d'entrée de données hautement évolutif qui peut traiter des millions d'événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données générées par vos périphériques connectés et vos applications. Les concentrateurs d'événements fonctionnent comme la « porte d'entrée » d’un pipeline d’événements, et une fois que les données sont collectées dans un concentrateur d'événements, elles peuvent être transformées et stockées à l'aide de n'importe quel fournisseur d'analyse en temps réel ou d’adaptateurs de traitement par lot ou de stockage. Les concentrateurs d'événements dissocient la production d'un flux d'événements de la consommation de ces événements, de manière à ce que les consommateurs d'événements puissent accéder aux événements selon leur propre planification.

Cet article, qui complète la vidéo [Intégrer la gestion des API Azure avec Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) , explique comment consigner les événements Gestion des API à l’aide d’Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Création d'un hub d'événements Azure
Pour créer un hub d’événements, connectez-vous au [portail Azure Classic](https://manage.windowsazure.com) et cliquez sur **Nouveau**->**App Services**->**Service Bu**->**Hub d’événements**->**Création rapide**. Entrez un nom d’Event Hub, une région, sélectionnez un abonnement et sélectionnez un espace de noms. Si vous n’avez pas créé d’espace de noms précédemment, vous pouvez en créer un en entrant un nom dans la zone de texte **Espace de noms** . Une fois que toutes les propriétés sont configurées, cliquez sur **Créer un hub d’événements** pour créer le hub d’événements.

![Créer un event hub][create-event-hub]

Ensuite, accédez à l’onglet **Configurer** de votre nouveau hub d’événements et créez deux **stratégies d’accès partagé**. Nommez la première stratégie **Envoi** et attribuez-lui des autorisations **Envoyer**.

![Stratégie Envoi][sending-policy]

Nommez la seconde **Réception** et attribuez-lui des autorisations **Écouter**, puis cliquez sur **Enregistrer**.

![Stratégie Réception][receiving-policy]

Chaque stratégie d’accès partagé permet aux applications d’envoyer et de recevoir des événements vers et depuis l’Event Hub. Pour accéder aux chaînes de connexion pour ces stratégies, accédez à l’onglet **Tableau de bord** du hub d’événements et cliquez sur **Informations de connexion**.

![Chaîne de connexion][event-hub-dashboard]

La chaîne de connexion **Envoi** est utilisée lors de l’enregistrement des événements et la chaîne de connexion **Réception** lors du téléchargement des événements à partir du hub d’événements.

![Chaîne de connexion][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>Créer un enregistreur d’événements de gestion des API
Maintenant que vous disposez d’un hub d’événements, l’étape suivante consiste à configurer un [enregistreur d’événements](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) dans votre service Gestion des API afin qu’il puisse enregistrer des événements dans le hub d’événements.

Les enregistreurs d’événements de gestion des API peuvent être configurés à l’aide de l’ [API REST Gestion des API](http://aka.ms/smapi). Avant d’utiliser l’API REST pour la première fois, passez en revue les [conditions préalables](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#Prerequisites) et assurez-vous que vous avez [activé l’accès à l’API REST](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Pour créer un enregistreur d’événements, créez une demande HTTP PUT à l’aide du modèle d’URL suivant.

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* Remplacez `{your service}` par le nom de votre instance de service Gestion des API.
* Remplacez `{new logger name}` par le nom souhaité pour votre nouvel enregistreur d’événements. Ce nom servira de référence lors de la configuration de la stratégie [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) .

Ajoutez les en-têtes suivants à la demande.

* Type de contenu : application/json
* Autorisation : SharedAccessSignature 58...
  * Pour obtenir des instructions sur la génération de `SharedAccessSignature` , consultez [Authentification de l’API REST Gestion des API Azure](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Spécifiez le corps de la demande en utilisant le modèle suivant.

```json
{
  "loggertype" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggertype` doit être défini sur `AzureEventHub`.
* `description` fournit une description facultative de l’enregistreur d’événements et peut être une chaîne vide si vous le souhaitez.
* `credentials` contient les valeurs `name` et `connectionString` de votre hub d’événements Azure.

Lorsque vous créez la demande, si l’enregistreur d’événements est créé, un code d’état `201 Created` est renvoyé.

> [!NOTE]
> Pour connaître les autres codes de retour possibles et leurs raisons, consultez [Créer un enregistreur d’événements](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT). Pour voir comment effectuer d’autres opérations, notamment la création de listes, la mise à jour et la suppression, consultez la documentation de l’entité [Enregistreur d’événements](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) .
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Configurer des stratégies Enregistrer sur event hub
Une fois que votre journal est configuré dans Gestion des API, vous pouvez configurer vos stratégies Enregistrer sur event hubs pour enregistrer les événements de votre choix. La stratégie Enregistrer sur event hubs peut être utilisée dans la section de la stratégie entrante ou dans la section de la stratégie sortante.

Pour configurer des stratégies, connectez-vous au [portail Azure](https://portal.azure.com), accédez à votre service de gestion des API et cliquez sur **portail de publication** pour accéder au portail de publication.

![Portail des éditeurs][publisher-portal]

Cliquez sur **Stratégies** ans le menu Gestion des API situé à gauche, sélectionnez le produit et l’API de votre choix, puis cliquez sur **Ajouter une stratégie**. Dans cet exemple, nous ajoutons une stratégie à l’**API Echo** dans le produit **Illimité**.

![Add policy][add-policy]

Placez votre curseur dans la section de la stratégie `inbound` et cliquez sur la stratégie **Log to EventHub`log-to-eventhub` pour insérer le modèle de déclaration de stratégie** .

![Policy editor][event-hub-policy]

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```

Remplacez `logger-id` par le nom de l’enregistreur d’événements Gestion des API que vous avez configuré à l’étape précédente.

Vous pouvez utiliser toute expression qui renvoie une chaîne en tant que valeur pour l’élément `log-to-eventhub` . Dans cet exemple, une chaîne contenant la date et l’heure, le nom du service, l’ID de la demande, l’adresse IP de la demande et le nom de l’opération est enregistrée.

Cliquez sur **Enregistrer** pour enregistrer la configuration de la stratégie mise à jour. Dès qu’elle est enregistrée, la stratégie est active et les événements sont enregistrés dans l’Event Hub désigné.

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur Azure Event Hubs
  * [Prise en main avec Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Réception de messages avec EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Guide de programmation Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* En savoir plus sur l’intégration de Gestion des API et Event Hubs
  * [Référence d’entité d’enregistreur](https://docs.microsoft.com/rest/api/apimanagement/loggers)
  * [Référence de stratégie log-to-eventhub](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Surveiller vos API avec gestion des API Azure, les hubs d’événements et Runscope](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>Regarder une procédure pas à pas en vidéo
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
>
>

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
