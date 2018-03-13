---
title: "Vue d’ensemble du modèle de sécurité et d’authentification Azure Event Hubs| Microsoft Docs"
description: "Présentation du modèle de sécurité et de l'authentification Event Hubs."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: sethm;clemensv
ms.openlocfilehash: bfe7b95236c1a5336c1bb3a93d0eb5ca880adabf
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Présentation du modèle de sécurité et de l'authentification Event Hubs

Le modèle de sécurité Azure Event Hubs remplit les conditions suivantes :

* Seuls les clients qui présentent des informations d’identification valides peuvent envoyer des données à un hub d’événements.
* Un client ne peut pas emprunter l’identité d’un autre client.
* Il est possible d’empêcher un client non autorisé d’envoyer des données à un hub d’événements.

## <a name="client-authentication"></a>Authentification du client

Le modèle de sécurité du hub d’événements est basé sur une combinaison de jetons de [signature d’accès partagé (SAS)](../service-bus-messaging/service-bus-sas.md) et d’*éditeurs d’événements*. Un éditeur d’événements définit un point de terminaison virtuel pour un hub d’événements. L’éditeur ne peut être utilisé que pour envoyer des messages à un hub d’événements. Il n'est pas possible de recevoir des messages à partir de l'éditeur.

En règle générale, un hub d’événements utilise un seul éditeur par client. Tous les messages qui sont envoyés à l’un des éditeurs d’un hub d’événements sont empilés dans celui-ci. Les éditeurs permettent un contrôle d’accès précis et une limitation.

Un jeton unique, qui est téléchargé sur le client, est affecté à chaque hub d’événement. Les jetons sont produits de façon à ce que chaque jeton unique donne accès à un éditeur unique différent. Un client qui possède un jeton ne peut envoyer qu’à un seul éditeur, et à aucun autre. Si plusieurs clients partagent le même jeton, alors ils partagent un éditeur.

Bien que cela soit déconseillé, il est possible d’équiper les appareils de jetons qui donnent un accès direct à un hub d’événements. N’importe quel appareil qui détient ce jeton peut envoyer des messages directement dans ce hub d’événements. Un appareil de ce type ne sera pas soumis à la limitation. En outre, il est impossible d’empêcher l’appareil d’effectuer des envois à ce hub d’événements.

Tous les jetons sont signés avec une clé SAS. En règle générale, tous les jetons sont signés avec la même clé. Les clients ne sont pas conscients de la clé. Cela empêche que d’autres clients fabriquent des jetons.

### <a name="create-the-sas-key"></a>Créer la clé SAP

Lorsque vous créez un espace de noms Event Hubs, le service génère automatiquement une clé SAS de 256 bits nommée **RootManageSharedAccessKey**. Cette règle contient une paire associée clé primaire/clé secondaire qui accorde des autorisations d’envoi, d’écoute et de gestion à l’espace de noms. Vous pouvez aussi créer des clés supplémentaires. Nous vous recommandons de produire une clé qui accorde les droits d’envoi au hub d’événements spécifique. Pour le reste de cette rubrique, supposons que vous avez nommé cette clé **EventHubSendKey**.

L’exemple suivant crée une clé exclusivement pour l’envoi, lors de la création du hub d’événements :

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Générer des jetons

Vous pouvez générer des jetons à l'aide de la clé SAS. Vous ne devez produire qu’un seul jeton par client. Les jetons peuvent ensuite être générés à l'aide de la méthode suivante. Tous les jetons sont générés à l'aide de la clé **EventHubSendKey** . Une URI unique est affectée à chaque jeton.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Lors de l'appel de cette méthode, l'URI doit être spécifiée en tant que `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Pour tous les jetons, l'URI est identique, à l'exception de `PUBLISHER_NAME`, qui doit être différent pour chaque jeton. Dans l’idéal, `PUBLISHER_NAME` représente l’ID du client qui reçoit ce jeton.

Cette méthode génère un jeton avec la structure suivante :

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

L'heure d'expiration du jeton est exprimée en secondes à partir du 1er janvier 1970. Vous trouverez ci-dessous un exemple de jeton :

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

En règle générale, les jetons ont une durée de vie équivalente ou supérieure à la durée de vie du client. Si le client a la possibilité d’obtenir un nouveau jeton, il est possible d’utiliser des jetons avec une durée de vie plus courte.

### <a name="sending-data"></a>Envoi de données

Une fois les jetons créés, chaque client est configuré avec son propre jeton unique.

Lorsque le client envoie des données dans un hub d’événements, il balise sa requête d’envoi avec le jeton. Pour empêcher un intrus de procéder à des écoutes clandestines et de voler le jeton, la communication entre le client et le hub d’événements doit avoir lieu sur un canal chiffré.

### <a name="blacklisting-clients"></a>Inscription des clients sur liste noire

Si un jeton est volé par un intrus, celui-ci peut emprunter l’identité du client à qui le jeton a été volé. L’inscription d’un client sur liste noire le rend inutilisable, jusqu’à ce qu’il reçoive un nouveau jeton qui utilise un éditeur différent.

## <a name="authentication-of-back-end-applications"></a>Authentification des applications principales

Pour authentifier des applications principales qui consomment les données générées par les clients Event Hubs, Event Hubs utilise un modèle de sécurité qui est similaire au modèle utilisé pour les rubriques Service Bus. Un groupe de consommateurs Event Hubs est équivalent à un abonnement à une rubrique Service Bus. Un client peut créer un groupe de consommateurs si la requête de création du groupe de consommateurs est accompagnée d’un jeton qui accorde des droits de gestion pour le hub d’événements ou pour l’espace de noms auxquels appartient le hub d’événements. Un client est autorisé à consommer des données à partir d’un groupe de consommateurs si la requête de réception est accompagnée d’un jeton qui accorde des droits de réception pour ce groupe de consommateurs, le hub d’événements ou l’espace de noms auxquels appartient le hub d’événements.

La version actuelle de Service Bus ne prend pas en charge les règles SAS pour les abonnements individuels. Il en va de même pour les groupes de consommateurs de hubs d'événements. La prise en charge SAS sera ajoutée ultérieurement pour ces deux fonctionnalités.

En l'absence d'authentification SAS pour les groupes de consommateurs individuels, vous pouvez utiliser des clés SAS pour sécuriser tous les groupes de consommateurs avec une clé commune. Cette approche permet à une application de consommer des données à partir de n’importe quel groupe de consommateurs d’un hub d’événements.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Event Hubs, consultez les rubriques suivantes :

* [Vue d’ensemble des hubs d’événements]
* [Présentation des signatures d’accès partagé]
* [Exemples d’application complets qui utilisent des concentrateurs d’événements]

[Vue d’ensemble des hubs d’événements]: event-hubs-what-is-event-hubs.md
[Exemples d’application complets qui utilisent des concentrateurs d’événements]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Présentation des signatures d’accès partagé]: ../service-bus-messaging/service-bus-sas.md

