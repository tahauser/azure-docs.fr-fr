---
title: "Authentification d’Azure Service Bus avec des signatures d’accès partagé | Microsoft Docs"
description: "Vue d’ensemble de l’authentification de Service Bus à l’aide des signatures d’accès partagé, plus d’informations sur l’authentification SAP avec Azure Service Bus."
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
ms.date: 12/21/2017
ms.author: sethm
ms.openlocfilehash: cdbac0fd18ad440ece35881cbe165c3c7eff8914
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2017
---
# <a name="service-bus-authentication-with-shared-access-signatures"></a>Authentification de Service Bus avec les signatures d’accès partagé

Les *signatures d’accès partagé* (SAP) constituent le mécanisme principal de sécurité de la messagerie Service Bus. Cet article décrit les SAP, leur fonctionnement et comment les utiliser indépendamment de la plateforme.

L’authentification des SAP permet aux applications de s’authentifier auprès de Service Bus à l’aide d’une clé d’accès configurée sur l’espace de noms ou sur l’entité de messagerie (file d’attente ou rubrique) avec des droits spécifiques associés. Vous pouvez ensuite utiliser cette clé pour générer un jeton SAS que les clients peuvent ensuite utiliser pour s’authentifier auprès de Service Bus.

La prise en charge de l’authentification SAP est incluse dans le kit de développement logiciel SDK Microsoft Azure SDK version 2.0 et ultérieure.

## <a name="overview-of-sas"></a>Présentation des signatures d’accès partagé (SAS)

Les signatures d’accès partagé sont un mécanisme d’authentification basé sur des hachages sécurisés SHA-256 ou des URI. Les signatures d’accès partagé sont un mécanisme extrêmement puissant qui est utilisé par tous les services Service Bus. En fait, les SAP ont deux composants : une *stratégie d’accès partagé* et une *signature d’accès partagé* (souvent appelée *jeton*).

L’authentification SAP dans Service Bus implique la configuration d’une clé de chiffrement avec les droits associés sur une ressource Service Bus. Les clients demandent l’accès aux ressources de Service Bus à l’aide d’un jeton SAS. Ce jeton se compose de l’URL de ressource à laquelle on accède et de l’expiration signée avec la clé configurée.

Vous pouvez configurer des règles d’autorisation de signature d’accès partagé sur des [relais](service-bus-fundamentals-hybrid-solutions.md#relays), des [files d’attente](service-bus-fundamentals-hybrid-solutions.md#queues) et des [rubriques](service-bus-fundamentals-hybrid-solutions.md#topics) Service Bus.

L’authentification SAP utilise les éléments suivants :

* [Règle d’autorisation d’accès partagée](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) : clé de chiffrement principale cryptée en Base64 à 256 bits, une clé secondaire facultative et un nom de clé et des droits associés (une collection de droits *écouter*, *envoyer* ou *gérer*).
* [signature d’accès partagé](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) : généré à l’aide de la HMAC-SHA256 d’une chaîne de ressource, constitué de l’URI de la ressource accessible et à échéance, avec la clé de chiffrement. La signature et les autres éléments décrits dans les sections suivantes sont formatés en une chaîne pour constituer le jeton SAP.

## <a name="shared-access-policy"></a>Stratégie d’accès partagé

Concernant les signatures d’accès partagé, il est important de comprendre que tout commence par une stratégie. Pour chaque stratégie, vous choisissez trois éléments d’information : le **nom**, l’**étendue** et les **autorisations**. Le **nom** est un nom unique au sein de cette étendue. L’étendue est l’URI de la ressource en question. Pour un espace de noms Service Bus, l’étendue est le nom de domaine complet (FQDN), tel que `https://<yournamespace>.servicebus.windows.net/`.

Les autorisations disponibles pour une stratégie sont relativement explicites :

* Envoyer
* Écouter
* gérer

Une fois la stratégie créée, une *clé primaire* et une *clé secondaire* lui sont affectées. Il s’agit de clés de chiffrement fortes. Ne les perdez pas et ne les diffusez pas ; elles seront toujours disponibles sur le [portail Azure][Azure portal]. Vous pouvez utiliser n’importe laquelle des clés générées et vous pouvez les régénérer à tout moment. Toutefois, si vous régénérez ou modifiez la clé primaire dans la stratégie, les signatures d’accès partagé créées à partir de celle-ci ne seront plus valides.

Lorsque vous créez un espace de noms Service Bus, une stratégie nommée **RootManageSharedAccessKey** est automatiquement créée pour l’espace de noms entier, qui dispose de toutes les autorisations. Étant donné que vous ne vous connectez pas en tant que **racine**, n’utilisez cette stratégie que si vous avez une très bonne raison de le faire. Vous pouvez créer des stratégies supplémentaires sous l’onglet **Configurer** pour l’espace de noms dans le portail. Il est important de noter qu’un seul niveau d’arborescence de Service Bus (espace de noms, file d’attente, etc.) peut avoir jusqu’à 12 stratégies.

## <a name="configuration-for-shared-access-signature-authentication"></a>Configuration de l’authentification de signature d’accès partagé
Vous pouvez configurer la règle [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) sur les espaces de noms, les files d’attente ou rubriques Service Bus. La configuration d’un abonnement [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) sur un abonnement Service Bus n’est pas pris en charge pour l’instant, mais vous pouvez utiliser les règles configurées sur un espace de noms ou une rubrique permettant de sécuriser l’accès aux abonnements. Pour obtenir un exemple fonctionnel qui illustre cette procédure, consultez l’exemple [Utilisation de l’authentification de signature d’accès partagé (SAS) avec les abonnements Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

Un maximum de 12 règles de ce type peut être configuré sur un espace de noms, une file d’attente ou une rubrique Service Bus. Les règles qui sont configurées sur un espace de noms Service Bus s’appliquent à toutes les entités appartenant à cet espace de noms.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Dans cette figure, les règles d’autorisation *manageRuleNS*, *sendRuleNS* et *listenRuleNS* s’appliquent à la file d’attente Q1 et à la rubrique T1, tandis que *listenRuleQ* et *sendRuleQ* s’appliquent uniquement à la file d’attente Q1, et *sendRuleT* uniquement à la rubrique T1.

Les paramètres clés de [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) sont les suivantes :

| Paramètre | DESCRIPTION |
| --- | --- |
| *Nom de clé* |Chaîne qui décrit la règle d’autorisation. |
| *Clé primaire* |Clé principale cryptée en Base64 sur 256 bits pour signer et valider le jeton SAS. |
| *Clé secondaire* |Clé secondaire cryptée en Base64 codée sur 256 bits pour signer et valider le jeton SAS. |
| *Droits d’accès* |Liste des droits d’accès accordés par la règle d’autorisation. Ces droits peuvent être n’importe quel ensemble contenant les droits d’écoute, d’envoi et de gestion. |

Quand un espace de noms Service Bus est configuré, un objet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) avec [KeyName](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) défini sur **RootManageSharedAccessKey** est créé par défaut.

## <a name="generate-a-shared-access-signature-token"></a>Création d’une signature d’accès partagé (jeton)

La stratégie elle-même n’est pas le jeton d’accès pour Service Bus. C’est l’objet à partir duquel le jeton d’accès est généré, à l’aide de la clé primaire ou de la clé secondaire. Tout client a accès aux clés de connexion spécifiées dans la règle d’autorisation d’accès partagée et peut générer un jetons SAP. Le jeton est généré suite à l’élaboration soigneuse d’une chaîne au format suivant :

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

Où `signature-string` correspond au hachage SHA-256 de l’étendue du jeton (**étendue** telle que décrite dans la section précédente) avec un CRLF ajouté et un délai d’expiration (en secondes depuis le début de l’époque : `00:00:00 UTC` le 1er janvier 1970). 

> [!NOTE]
> Pour éviter une durée d’expiration courte du jeton, il est recommandé d’encoder la valeur de durée d’expiration comme un entier non signé 32 bits ou, mieux, un entier long (64 bits).  
> 
> 

Le hachage est similaire au pseudo-code suivant et retourne 32 octets.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Les valeurs non hachées se trouvent dans la chaîne **SharedAccessSignature** afin que le destinataire puisse calculer le hachage avec les mêmes paramètres, pour s’assurer qu’il renvoie le même résultat. L’URI spécifie l’étendue et le nom de la clé identifie la stratégie à utiliser pour calculer le hachage. Ceci est important du point de vue de la sécurité. Si la signature ne correspond pas à celle calculée par le destinataire (Service Bus), l’accès est refusé. À ce stade, vous pouvez être sûr que l’expéditeur avait accès à la clé et doit bénéficier des droits spécifiés dans la stratégie.

Notez que vous devez utiliser l’URI de ressource codé pour cette opération. L’URI de ressource est l’URI complet de la ressource Service Bus à laquelle vous souhaitez accéder. Par exemple, `http://<namespace>.servicebus.windows.net/<entityPath>` ou `sb://<namespace>.servicebus.windows.net/<entityPath>` ; qui est, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`.

La règle de l’autorisation d’accès partagé utilisée pour la signature doit être configurée sur l’entité spécifiée par cette URI, ou par un de ses parents hiérarchiques. Par exemple, `http://contoso.servicebus.windows.net/contosoTopics/T1` ou `http://contoso.servicebus.windows.net` dans l’exemple précédent.

Un jeton SAP est valable pour toutes les ressources de `<resourceURI>` utilisées dans la `signature-string`.

Le [Nom de clé](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_KeyName) présent dans le jeton SAS fait référence à la règle d’autorisation partagée **Nom de clé** utilisée pour générer le jeton.

L’ *URL-encoded-resourceURI* doit être identique à l’URI utilisée dans la chaîne de signature lors du traitement de la signature. Elle doit être [encodée en pourcentage](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Il est recommandé de régénérer régulièrement les clés utilisées dans l’objet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . Les applications doivent généralement utiliser la [Clé primaire](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) pour générer un jeton SAS. Lors de la régénération des clés, vous devez remplacer la [clé secondaire](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) par l’ancienne clé primaire, et générer une nouvelle clé en tant que nouvelle clé primaire. Cela vous permet de continuer à utiliser des jetons d’autorisation n’ayant pas encore expiré émis avec l’ancienne clé primaire.

Si une clé est compromise et que vous devez révoquer les clés, vous pouvez régénérer la [clé primaire](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) et la [clé secondaire](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) d’une règle [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), et les remplacer par de nouvelles clés. Cette procédure annule tous les jetons signés avec les anciennes clés.

## <a name="how-to-use-shared-access-signature-authentication-with-service-bus"></a>Comment utiliser l’authentification par signature d’accès partagé avec Service Bus

Les scénarios suivants incluent la configuration des règles d’autorisation, la génération de jetons SAP et l’autorisation de client.

Pour visionner un exemple fonctionnel d’application Service Bus qui illustre la configuration et l’autorisation SAP, consultez [Authentification de la Signature d’accès partagé avec Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Un exemple d’utilisation de règles d’autorisation SAP configuré sur les espaces de noms ou des rubriques pour sécuriser les abonnements Service Bus est disponible ici : [Utilisation de l’authentification de signature d’accès partagé (SAP) avec les abonnements Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-a-namespace"></a>Accès aux règles d’autorisation d’accès partagé sur un espace des noms

Les opérations sur la racine de la racine d’espace de noms Service Bus requièrent l’authentification par certificat. Vous devez charger un certificat de gestion pour votre abonnement Azure. Pour télécharger un certificat de gestion, suivez les étapes décrites [ici](../cloud-services/cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate), en utilisant le [portail Azure][Azure portal]. Pour plus d’informations sur les certificats de gestion Azure, consultez la rubrique [Vue d’ensemble des certificats Azure](../cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Le point de terminaison pour l’accès aux règles d’autorisation de l’accès partagé dans un espace de noms Service Bus est la suivante :

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/
```

Pour créer un objet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) dans un espace de noms Service Bus, exécutez une opération POST sur ce point de terminaison avec les informations de règle sérialisée JSON ou XML. Par exemple : 

```csharp
// Base address for accessing authorization rules on a namespace
string baseAddress = @"https://management.core.windows.net/<subscriptionId>/services/ServiceBus/namespaces/<namespace>/AuthorizationRules/";

// Configure authorization rule with base64-encoded 256-bit key and Send rights
var sendRule = new SharedAccessAuthorizationRule("contosoSendAll",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send });

// Operations on the Service Bus namespace root require certificate authentication.
WebRequestHandler handler = new WebRequestHandler
{
    ClientCertificateOptions = ClientCertificateOption.Manual
};
// Access the management certificate by subject name
handler.ClientCertificates.Add(GetCertificate(<certificateSN>));

HttpClient httpClient = new HttpClient(handler)
{
    BaseAddress = new Uri(baseAddress)
};
httpClient.DefaultRequestHeaders.Accept.Add(
    new MediaTypeWithQualityHeaderValue("application/json"));
httpClient.DefaultRequestHeaders.Add("x-ms-version", "2015-01-01");

// Execute a POST operation on the baseAddress above to create an auth rule
var postResult = httpClient.PostAsJsonAsync("", sendRule).Result;
```

De même, utilisez une opération GET sur le point de terminaison pour lire les règles d’autorisation configurées sur l’espace de noms.

Pour mettre à jour ou supprimer une règle d’autorisation spécifique, utilisez le point de terminaison suivant :

```http
https://management.core.windows.net/{subscriptionId}/services/ServiceBus/namespaces/{namespace}/AuthorizationRules/{KeyName}
```

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Accès aux règles d’autorisation d’accès partagé sur une entité

Vous pouvez accéder à un objet [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configuré sur une file d’attente ou une rubrique Service Bus, par le biais de la collection [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) dans les objets [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) ou [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription) correspondants.

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

Les applications utilisant le kit de développement logiciel (SDK) avec les bibliothèques.NET Service BUS peuvent utiliser les autorisations SAP via la classe [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) classe. Le code suivant montre comment utiliser le fournisseur de jeton pour envoyer des messages à une file d’attente Service Bus.

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

Les applications peuvent également utiliser des associations de sécurité pour l’authentification en utilisant une chaîne de connexion dans les méthodes qui acceptent des chaînes de connexion.

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

Si vous donnez un jeton SAS à un expéditeur ou un client, celui-ci ne dispose pas directement de la clé et il ne peut pas inverser le hachage pour l’obtenir. Ainsi, vous contrôlez le contenu auquel il peut accéder et pour quelle durée. Il est important de ne pas oublier que si vous modifiez la clé primaire dans la stratégie, les signatures d’accès partagé créées à partir de celle-ci ne seront plus valides.

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
> Il est important que la connexion soit créée avec le **mécanisme d’authentification SASL défini sur EXTERNAL** (et non sur le paramètre par défaut PLAIN avec nom d’utilisateur et mot de passe utilisé lorsque vous n’avez pas besoin d’envoyer le jeton SAP).
> 
> 

Le serveur de publication crée ensuite deux liens AMQP pour envoyer le jeton SAP et recevoir la réponse (résultat de validation du jeton) depuis le service.

Le message AMQP inclut un ensemble de propriétés et plus d’informations qu’un simple message. Le jeton SAP est le corps du message (à l’aide de son constructeur). La propriété **« ReplyTo »** est définie sur le nom du nœud permettant de recevoir le résultat de validation sur le lien du récepteur (vous pouvez modifier son nom si vous le souhaitez, il sera créé dynamiquement par le service). Les trois dernières propriétés personnalisées / application sont utilisées par le service pour indiquer le type d’opération à exécuter. Comme l’indique le projet de spécification CBS, il doit s’agir du **nom de l’opération** (« put-token »), du **type de jeton** (dans ce cas, « servicebus.windows.net:sastoken ») et du **« nom » de l’audience** à laquelle le jeton s’applique (entité entière).

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

## <a name="next-steps"></a>étapes suivantes

Pour en savoir plus sur la messagerie Service Bus, voir les rubriques suivantes.

* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Utilisation des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com