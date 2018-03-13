---
title: "Contrôle d’accès Azure Service Bus avec des signatures d’accès partagé | Microsoft Docs"
description: "Vue d’ensemble du contrôle d’accès Service Bus avec des signatures d’accès partagé, et informations sur l’autorisation SAP avec Azure Service Bus."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: sethm;clemensv
ms.openlocfilehash: f6bb77ad6df09e36419b24b24924dac7ecd79065
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Contrôle d’accès Service Bus avec des signatures d’accès partagé

Les *signatures d’accès partagé* (SAP) constituent le mécanisme principal de sécurité de la messagerie Service Bus. Cet article décrit les SAP, leur fonctionnement et comment les utiliser indépendamment de la plateforme.

SAP protège l’accès à Service Bus en fonction de règles d’autorisation. Celles-ci sont configurées sur un espace de noms, ou sur une entité de messagerie (relais, file d’attente ou rubrique). Une règle d’autorisation a un nom, est associée à des droits spécifiques et comporte une paire de clés de chiffrement. Vous utilisez le nom et la clé de la règle via le Kit de développement logiciel (SDK) Service Bus ou dans votre propre code pour générer un jeton SAP. Un client peut ensuite passer le jeton à Service Bus pour prouver l’autorisation pour l’opération demandée.

## <a name="overview-of-sas"></a>Présentation des signatures d’accès partagé (SAS)

Les signatures d’accès partagé sont un mécanisme d’autorisation reposant sur des revendications à l’aide de simples jetons. À l’aide d’une SAP, les clés ne sont jamais transmises simultanément. Les clés sont utilisées pour signer par chiffrement des informations qui peuvent être vérifiées ultérieurement par le service. Une SAP peut être utilisée de façon similaire à un schéma de nom d’utilisateur et de mot de passe où le client est en possession immédiate d’un nom de règle d’autorisation et d’une clé correspondante. Une SAP peut également être utilisée comme un modèle de sécurité fédéré, où le client reçoit un jeton d’accès signé et limité dans le temps d’un service d’émission de jeton de sécurité sans jamais être en possession de la clé de signature.

L’authentification SAP dans Service Bus est configurée avec des [règles d’autorisation d’accès partagé](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) nommées ayant les droits d’accès associés, ainsi qu’une paire de clés de chiffrement primaire et secondaire. Les clés sont des valeurs de 256 bits dans une représentation Base64. Vous pouvez configurer des règles au niveau de l’espace de noms, sur les [relais](service-bus-fundamentals-hybrid-solutions.md#relays), [files d’attente](service-bus-fundamentals-hybrid-solutions.md#queues) et [rubriques](service-bus-fundamentals-hybrid-solutions.md#topics) Service Bus.

Le jeton de [signature d’accès partagé](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) contient le nom de la règle d’autorisation choisie, l’URI de la ressource à laquelle accéder, un délai d’expiration et une signature de chiffrement HMAC-SHA256 calculé sur ces champs à l’aide de la clé de chiffrement primaire ou secondaire de la règle d’autorisation choisie.

## <a name="shared-access-authorization-policies"></a>Stratégies d’autorisation d’accès partagé

Chaque espace de noms Service Bus et chaque entité Service Bus ont une stratégie d’autorisation d’accès partagé constituée de règles. La stratégie au niveau de l’espace de noms s’applique à toutes les entités à l’intérieur de l’espace de noms, quelle que soit leur configuration de stratégie individuelle.

Pour chaque règle de stratégie d’autorisation, vous choisissez trois éléments d’information : le **nom**, **l’étendue** et les **autorisations**. Le **nom** est un nom unique au sein de cette étendue. L’étendue est l’URI de la ressource en question. Pour un espace de noms Service Bus, l’étendue est le nom de domaine complet (FQDN), tel que `https://<yournamespace>.servicebus.windows.net/`.

Les droits qui sont attribués par la règle de stratégie peuvent être une combinaison de :

* « Envoyer » - donne le droit d’envoyer des messages à l’entité.
* « Écouter » - donne le droit d’écouter (relais) ou de recevoir (file d’attente, abonnements et toutes les opérations de traitement de messages associées).
* « Gérer » - donne le droit de gérer la topologie de l’espace de noms, y compris la création et la suppression des entités.

Le droit « Gérer » inclut les droits « Envoyer » et « Recevoir ».

Une stratégie d’entité et d’espace de noms peut contenir 12 règles d’autorisation d’accès partagé maximum, ce qui laisse de l’espace à trois ensembles de règles, chacun couvrant les droits basiques et la combinaison des droits « Envoyer » et « Écouter ». Cette limite souligne que le magasin de stratégies SAP n’est pas destiné à être magasin de comptes de service ou d’utilisateur. Si votre application doit accorder l’accès aux identités de service ou d’utilisateur basées sur Service Bus, elle doit implémenter un service d’émission de jeton de sécurité qui émet des jetons SAP après une authentification et une vérification de l’accès.

Une *clé primaire* et une *clé secondaire* sont attribuées à une règle d’autorisation. Il s’agit de clés de chiffrement fortes. Ne les perdez pas et ne les diffusez pas ; elles seront toujours disponibles sur le [portail Azure][Azure portal]. Vous pouvez utiliser n’importe laquelle des clés générées et vous pouvez les régénérer à tout moment. Si vous régénérez ou modifiez une clé dans la stratégie, tous les jetons précédemment émis en fonction de cette clé deviennent instantanément non valides. Toutefois, les connexions en cours créées en fonction de ces jetons continuent de fonctionner jusqu’à ce que le jeton expire.

Lorsque vous créez un espace de noms Service Bus, une règle de stratégie nommée **RootManageSharedAccessKey** est automatiquement créée pour l’espace de noms. Cette stratégie dispose d’autorisations Gérer pour l’espace de noms complet. Il est recommandé de traiter cette règle comme un compte **racine** d’administration et de ne pas l’utiliser dans votre application. Vous pouvez créer des règles de stratégies supplémentaires sous l’onglet **Configurer** pour l’espace de noms dans le portail via Powershell ou Azure CLI.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuration de l’authentification de signature d’accès partagé

Vous pouvez configurer la règle [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) sur les espaces de noms, les files d’attente ou rubriques Service Bus. La configuration d’un abonnement [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) sur un abonnement Service Bus n’est pas pris en charge pour l’instant, mais vous pouvez utiliser les règles configurées sur un espace de noms ou une rubrique permettant de sécuriser l’accès aux abonnements. Pour obtenir un exemple fonctionnel qui illustre cette procédure, consultez l’exemple [Utilisation de l’authentification de signature d’accès partagé (SAS) avec les abonnements Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Dans cette figure, les règles d’autorisation *manageRuleNS*, *sendRuleNS* et *listenRuleNS* s’appliquent à la file d’attente Q1 et à la rubrique T1, tandis que *listenRuleQ* et *sendRuleQ* s’appliquent uniquement à la file d’attente Q1, et *sendRuleT* uniquement à la rubrique T1.

## <a name="generate-a-shared-access-signature-token"></a>Générer un jeton de signature d’accès partagé

Les clients qui ont accès au nom d’une règle d’autorisation et à celui de ses clés de signature peuvent générer un jeton SAP. Le jeton est généré suite à l’élaboration d’une chaîne au format suivant :

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** - Délai d’expiration du jeton. Entier reflétant les secondes depuis l’époque `00:00:00 UTC` du 1er janvier 1970 (époque UNIX) lorsque le jeton expire.
* **`skn`** - Nom de la règle d’autorisation.
* **`sr`** - URI de la ressource faisant l’objet de l’accès.
* **`sig`** - Signature.

La `signature-string` est le hachage SHA-256 calculé sur l’URI de ressource (**étendue** comme décrit dans la section précédente) et la représentation de chaîne du délai d’expiration du jeton, séparée par CRLF.

Le calcul de hachage est similaire au code de pseudo suivant et retourne une valeur de hachage de 256 bits/32 octets.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Le jeton contient les valeurs non hachées afin que le destinataire puisse recalculer le hachage avec les mêmes paramètres, en vérifiant que l’émetteur est en possession d’une clé de signature valide. 

L’URI de ressource est l’URI complet de la ressource Service Bus à laquelle vous souhaitez accéder. Par exemple, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>` ; qui est, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. L’URI doit être [encodée en pourcentage](https://msdn.microsoft.com/library/4fkewx0t.aspx).

La règle de l’autorisation d’accès partagé utilisée pour la signature doit être configurée sur l’entité spécifiée par cette URI, ou par un de ses parents hiérarchiques. Par exemple, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` dans l’exemple précédent.

Un jeton SAP est valable pour toutes les ressources avec le préfixe `<resourceURI>` utilisé dans `signature-string`.

## <a name="regenerating-keys"></a>Régénération des clés

Il est recommandé de régénérer régulièrement les clés utilisées dans l’objet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . Les emplacements des clés primaires et secondaires existent afin que vous puissiez permuter progressivement les clés. Si votre application utilise généralement la clé primaire, vous pouvez copier la clé primaire dans l’emplacement de la clé secondaire et alors seulement régénérer la clé primaire. La nouvelle valeur de clé primaire peut ensuite être configurée dans les applications clientes, qui bénéficient d’un accès continu à l’aide de l’ancienne clé primaire dans l’emplacement secondaire. Une fois que tous les clients sont mis à jour, vous pouvez régénérer la clé secondaire pour enfin mettre hors service l’ancienne clé primaire.

Si vous avez connaissance ou suspectez qu’une clé est compromise et si vous devez révoquer les clés, vous pouvez régénérer les éléments [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) et [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) d’une règle [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), et les remplacer par de nouvelles clés. Cette procédure annule tous les jetons signés avec les anciennes clés.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Authentification par signature d’accès partagé avec Service Bus

Les scénarios décrits ci-après incluent la configuration des règles d’autorisation, la génération de jetons SAP et l’autorisation de client.

Pour visionner un exemple fonctionnel d’application Service Bus qui illustre la configuration et l’autorisation SAP, consultez [Authentification de la Signature d’accès partagé avec Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Un exemple d’utilisation de règles d’autorisation SAP configuré sur les espaces de noms ou des rubriques pour sécuriser les abonnements Service Bus est disponible ici : [Utilisation de l’authentification de signature d’accès partagé (SAP) avec les abonnements Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Accès aux règles d’autorisation d’accès partagé sur une entité

Avec les bibliothèques .NET Framework Service Bus, vous pouvez accéder à un objet [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configuré sur une file d’attente ou une rubrique Service Bus, par le biais de la collection [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) dans les objets [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) correspondants.

Le code suivant montre comment ajouter des règles d’autorisation à une file d’attente.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Utilisation de l’autorisation de la signature d’accès partagé (SAP)

Les applications utilisant le kit de développement logiciel (SDK) avec les bibliothèques.NET Service BUS peuvent utiliser les autorisations SAP via la classe [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) classe. Le code suivant montre comment utiliser le fournisseur de jeton pour envoyer des messages à une file d’attente Service Bus. Comme alternative à l’utilisation présentée ici, vous pouvez également transmettre un jeton préalablement émis à la méthode de fabrique du fournisseur de jetons.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Vous pouvez aussi utiliser le fournisseur de jetons directement pour l’émission de jetons à transmettre à d’autres clients. 

Les chaînes de connexion peuvent inclure un nom de règle (*SharedAccessKeyName*) et la clé de la règle (*SharedAccessKey*) ou un jeton émis précédemment (*SharedAccessSignature*). Lorsque ceux-ci sont présents dans la chaîne de connexion transmise à un constructeur ou une méthode de fabrique acceptant une chaîne de connexion, le fournisseur de jetons SAP est automatiquement créé et renseigné.

Pour utiliser une autorisation SAS avec les relais Service Bus, vous pouvez utiliser des clés SAS configurées sur un espace de noms Service Bus. Si vous créez explicitement un relais sur l’espace de noms ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) avec un objet [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)), vous pouvez définir les règles SAP pour ce relais. Pour utiliser une autorisation SAS avec des abonnements Service Bus, vous pouvez utiliser des clés SAS configurées sur un espace de noms Service Bus ou sur une rubrique.

## <a name="use-the-shared-access-signature-at-http-level"></a>Utilisation de la signature d’accès partagé (au niveau de HTTP)

Maintenant que vous savez comment créer des signatures d’accès partagé pour les entités dans Service Bus, vous êtes prêt à effectuer une requête HTTP POST :

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

N’oubliez pas que cela fonctionne pour tout. Vous pouvez créer une signature d’accès partagé pour une file d’attente, une rubrique ou un abonnement.

Si vous donnez un jeton SAS à un expéditeur ou un client, celui-ci ne dispose pas directement de la clé et il ne peut pas inverser le hachage pour l’obtenir. Ainsi, vous contrôlez le contenu auquel il peut accéder et pour quelle durée. Il est important de ne pas oublier que si vous modifiez la clé primaire dans la stratégie, les signatures d’accès partagé créées à partir de celle-ci ne sont plus valides.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Utilisation de la signature d’accès partagé (au niveau d’AMQP)

Dans la section précédente, vous avez vu comment utiliser le jeton SAS avec une requête HTTP POST pour envoyer des données au Service Bus. Comme vous le savez, vous pouvez accéder au Service Bus à l’aide du protocole AMQP (Advanced Message Queuing Protocol) qui est le protocole privilégié pour des raisons de performances dans de nombreux scénarios. L’utilisation de jetons SAP avec AMQP est décrite dans le document [AMQP Claim-Based Security Version 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc). Bien qu’au stade d’ébauche depuis 2013, elle est bien prise en charge par Azure aujourd’hui.

Avant de commencer à envoyer des données vers Service Bus, le serveur de publication doit envoyer le jeton SAP dans un message AMQP à un nœud AMQP bien défini nommé **$cbs** (il s’agit d’une sorte de file d’attente « spéciale » que le service utilise pour acquérir et valider tous les jetons SAP). Le serveur de publication doit spécifier le champ **ReplyTo** dans le message AMQP. Il s’agit du nœud sur lequel le service répond au serveur de publication avec le résultat de la validation du jeton (système de demande/réponse simple entre le serveur de publication et le service). Ce nœud de réponse est créé « à la volée » en ce qui concerne la « création dynamique du nœud à distance », comme le décrit la spécification AMQP 1.0.  Après avoir vérifié que le jeton SAS est valide, le serveur de publication peut continuer et commencer à envoyer des données au service.

Les étapes suivantes montrent comment envoyer le jeton SAP avec le protocole AMQP à l'aide de la bibliothèque [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) . C’est utile si vous ne pouvez pas utiliser le Kit de développement logiciel (SDK) Service Bus officiel (par exemple sur WinRT, .NET Compact Framework, .NET Micro Framework et Mono) pour le développement en C\#. Bien évidemment, cette bibliothèque est utile pour comprendre comment la sécurité basée sur les revendications fonctionne au niveau AMQP, tout comme vous avez pu voir comment cela fonctionne au niveau HTTP (avec une demande HTTP POST et le jeton SAP envoyé dans l’en-tête « Autorisation »). Si vous n’avez pas besoin de ces connaissances aussi approfondies concernant AMQP, vous pouvez utiliser le Kit de développement logiciel (SDK) Service Bus officiel avec des applications .NET Framework qui s’en occuperont pour vous.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

La méthode `PutCbsToken()` reçoit la *connexion* (instance de classe de connexion AMQP telle que fournie par la [bibliothèque AMQP .NET Lite](https://github.com/Azure/amqpnetlite)) qui représente la connexion TCP au service et le paramètre *sasToken* correspondant au jeton SAP à envoyer. 

> [!NOTE]
> Il est important que la connexion soit créée avec le **mécanisme d’authentification SASL défini sur ANONYMOUS** (et non sur le paramètre par défaut PLAIN avec le nom d’utilisateur et le mot de passe utilisés lorsque vous n’avez pas besoin d’envoyer le jeton SAP).
> 
> 

Le serveur de publication crée ensuite deux liens AMQP pour envoyer le jeton SAP et recevoir la réponse (résultat de validation du jeton) depuis le service.

Le message AMQP inclut un ensemble de propriétés et plus d’informations qu’un simple message. Le jeton SAP est le corps du message (à l’aide de son constructeur). La propriété **« ReplyTo »** est définie sur le nom du nœud permettant de recevoir le résultat de validation sur le lien du récepteur (vous pouvez modifier son nom si vous le souhaitez, il sera créé dynamiquement par le service). Les trois dernières propriétés personnalisées / application sont utilisées par le service pour indiquer le type d’opération à exécuter. Comme l’indique le projet de spécification CBS, il doit s’agir du **nom de l’opération** (« put-token »), du **type de jeton** (dans ce cas, `servicebus.windows.net:sastoken`) et du **« nom » de l’audience** à laquelle le jeton s’applique (entité entière).

Après avoir envoyé le jeton SAP sur le lien de l’expéditeur, le serveur de publication doit lire la réponse sur le lien du récepteur. La réponse est un simple message AMQP avec une propriété d’application nommée **« status-code »** qui peut contenir les mêmes valeurs qu’un code d’état HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Droits requis pour les opérations Service Bus

Le tableau suivant affiche les droits d’accès requis pour effectuer diverses opérations sur les ressources Service Bus.

| Opération | Revendication obligatoire | Étendue de la revendication |
| --- | --- | --- |
| **Espace de noms** | | |
| Configure une règle d’autorisation dans un espace de noms |gérer |N’importe quelle adresse d’espace de noms |
| **Registre de service** | | |
| Énumération des stratégies privées |gérer |N’importe quelle adresse d’espace de noms |
| Commencer à écouter sur un espace de noms |Écouter |N’importe quelle adresse d’espace de noms |
| Envoyer des messages à un écouteur sur un espace de noms |Envoyer |N’importe quelle adresse d’espace de noms |
| **File d'attente** | | |
| Créer une file d’attente |gérer |N’importe quelle adresse d’espace de noms |
| Suppression d'une file d'attente |gérer |N’importe quelle adresse de file d’attente valide |
| Énumérer les files d’attente |gérer |/$Resources/Queues |
| Obtenir la description de file d’attente |gérer |N’importe quelle adresse de file d’attente valide |
| Configure une règle d’autorisation pour une file d’attente |gérer |N’importe quelle adresse de file d’attente valide |
| Envoyer dans la file d’attente |Envoyer |N’importe quelle adresse de file d’attente valide |
| Réception des messages d'une file d'attente |Écouter |N’importe quelle adresse de file d’attente valide |
| Abandonner ou terminer des messages après la réception du message en mode de verrouillage |Écouter |N’importe quelle adresse de file d’attente valide |
| Différer un message pour une récupération ultérieure |Écouter |N’importe quelle adresse de file d’attente valide |
| Mettre un message au rebut |Écouter |N’importe quelle adresse de file d’attente valide |
| Obtenir l’état associé à une session de file d’attente |Écouter |N’importe quelle adresse de file d’attente valide |
| Obtenir l’état associé à une session de file d’attente de message |Écouter |N’importe quelle adresse de file d’attente valide |
| **Rubrique** | | |
| Création d'une rubrique |gérer |N’importe quelle adresse d’espace de noms |
| Supprimer une rubrique |gérer |N’importe quelle adresse de rubrique valide |
| Énumérer les rubriques |gérer |/$Resources/Topics |
| Obtenir la description de la rubrique |gérer |N’importe quelle adresse de rubrique valide |
| Configure une règle d’autorisation pour une rubrique |gérer |N’importe quelle adresse de rubrique valide |
| Envoyer à la rubrique |Envoyer |N’importe quelle adresse de rubrique valide |
| **Abonnement** | | |
| Création d’un abonnement |gérer |N’importe quelle adresse d’espace de noms |
| Supprimer l’abonnement |gérer |../myTopic/Subscriptions/mySubscription |
| Énumérer les abonnements |gérer |../myTopic/Subscriptions |
| Obtenir la description de l’abonnement |gérer |../myTopic/Subscriptions/mySubscription |
| Abandonner ou terminer des messages après la réception du message en mode de verrouillage |Écouter |../myTopic/Subscriptions/mySubscription |
| Différer un message pour une récupération ultérieure |Écouter |../myTopic/Subscriptions/mySubscription |
| Mettre un message au rebut |Écouter |../myTopic/Subscriptions/mySubscription |
| Obtenir l’état associé à une session de rubrique |Écouter |../myTopic/Subscriptions/mySubscription |
| Définir l’état associé à une session de rubrique |Écouter |../myTopic/Subscriptions/mySubscription |
| **Règles** | | |
| Créer une règle |gérer |../myTopic/Subscriptions/mySubscription |
| Supprimer une règle |gérer |../myTopic/Subscriptions/mySubscription |
| Énumérer des règles |Gérer ou écouter |.. /myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la messagerie Service Bus, voir les rubriques suivantes.

* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Utilisation des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com