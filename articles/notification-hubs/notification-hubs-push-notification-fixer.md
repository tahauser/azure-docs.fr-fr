---
title: "Diagnostic des problèmes liés à la perte des notifications dans Azure Notification Hubs"
description: "Découvrez comment diagnostiquer les problèmes courants liés à la perte des notifications dans Azure Notification Hubs."
services: notification-hubs
documentationcenter: Mobile
author: jwhitedev
manager: kpiteira
editor: 
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: 3925208fe56bcd9513ec4c0f21aa1e2dd8fbf9c5
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="diagnose-dropped-notifications-in-notification-hubs"></a>Diagnostiquer les problèmes de perte des notifications dans Notification Hubs

L’une des questions les plus couramment posées par les clients d’Azure Notification Hubs concerne les notifications qui sont envoyées à partir d’une application et qui n’apparaissent pas sur leurs appareils. Ils souhaitent savoir où se trouvent ces notifications, pourquoi elles se sont perdues et comment résoudre ce problème. Cet article explique les raisons pour lesquelles les notifications peuvent être perdues ou ne pas apparaître sur les appareils. Découvrez comment analyser le problème et déterminer la cause racine. 

En premier lieu, il est essentiel de comprendre la manière dont Notification Hubs envoie (push) les notifications vers un appareil.

![Architecture de Notification Hubs][0]

Dans un flux de notification d’envoi type, le message est envoyé par le *serveur d’applications backend* à Notification Hubs. Notification Hubs procède à un traitement de toutes les inscriptions. Le traitement prend en compte les balises configurées et les expressions de balise pour déterminer les cibles. Les cibles correspondent à tous les enregistrements qui doivent recevoir la notification Push. Ces inscriptions peuvent inclure toutes les plateformes prises en charge : iOS, Google, Windows, Windows Phone, Kindle et Baidu pour Android en Chine.

Une fois les cibles établies, Notification Hubs envoie (push) des notifications au *service de notifications Push* correspondant à la plateforme de l’appareil. Il peut s’agir, par exemple, d’Apple Push Notification Service (APNs) pour Apple ou de Firebase Cloud Messaging (FCM) pour Google. Notification Hubs envoie les notifications regroupées en plusieurs lots d’inscriptions. Notification Hubs s’authentifie auprès du service de notifications Push correspondant, en fonction des informations d’identification que vous avez configurées dans le portail Azure, sous **Configure Notification Hub** (Configurer Notification Hubs). Le service de notifications Push transmet alors les notifications aux *appareils clients* correspondants. 

Notez que le dernier tronçon de remise des notifications s’effectue entre le service de notifications Push de la plateforme et l’appareil. La perte des notifications peut être causée par l’un des quatre composants principaux du processus de notification Push, à savoir le client, le serveur d’applications backend, Notification Hubs et le service de notifications Push de la plateforme. Pour plus d’informations sur l’architecture de Notification Hubs, consultez [Présentation de Notification Hubs].

L’échec de la remise des notifications peut se produire lors de la phase de test ou de préproduction. À ce stade, la perte des notifications peut indiquer un problème de configuration. Si l’échec de remise des notifications se produit lors de la phase de production, il se peut que certaines ou que l’ensemble des notifications soient perdues. Dans ce cas, cela indique un problème plus complexe lié à l’application ou au type de messagerie. 

La section suivante présente les scénarios dans lesquels les notifications peuvent être perdues, des plus courants aux plus rares.

## <a name="notification-hubs-misconfiguration"></a>Configuration incorrecte de Notification Hubs
Pour envoyer des notifications au service de notifications Push adapté, Notification Hubs doit s’authentifier dans le contexte de l’application du développeur. Pour ce faire, le développeur doit créer un compte de développeur avec la plateforme correspondante (Google, Apple, Windows, etc.). Ensuite, il doit inscrire son application auprès de la plateforme qui lui fournit des informations d’identification. 

Vous devez ajouter ces informations d’identification de plateforme dans le portail Azure. Si aucune notification n’arrive sur l’appareil, la première chose à faire est de vérifier que les bonnes informations d’identification sont configurées dans Notification Hubs. Les informations d’identification doivent être celles de l’application qui a été créée sous un compte de développeur spécifique à la plateforme. 

Pour obtenir des instructions pas à pas pour ce processus, consultez [Bien démarrer avec Azure Notification Hubs].

Voici certaines erreurs de configuration courantes :

* **Généralités**
   
    * Vérifiez que le nom de votre hub de notification (sans fautes de frappe) est le même dans chacun de ces emplacements :

        * Là où vous vous êtes inscrit auprès du client
        * Là où vous envoyez des notifications à partir du backend
        * Là où vous avez configuré les informations d’identification du service de notifications Push.
    
    * Vérifiez que vous utilisez les mêmes chaînes de configuration de signature d’accès partagé sur le client et sur le serveur d’applications backend. En règle générale, vous devez utiliser **DefaultListenSharedAccessSignature** sur le client et **DefaultFullSharedAccessSignature** sur le serveur d’applications backend (ce qui autorise l’envoi de notifications à Notification Hubs)

* **Configuration d’Apple Push Notification Service**
   
    Vous devez disposer de deux hubs : un pour la production et l’autre pour vos tests. Le certificat que vous chargez dans l’environnement de bac à sable (sandbox) doit être différent de celui que vous chargerez dans l’environnement de production. N’essayez pas de charger plusieurs types de certificats dans un même hub. Cela peut entraîner l’échec de la remise des notifications. 
    
    Si vous chargez par inadvertance plusieurs types de certificats dans un même hub, nous vous recommandons de supprimer ce hub et d’en créer un nouveau. Si vous ne pouvez pas supprimer le hub pour une raison quelconque, vous devez au moins supprimer toutes les inscriptions effectuées auprès de ce hub. 

* **Configuration de Firebase Cloud Messaging** 
   
    1. Vérifiez que la *clé serveur* que vous avez obtenue de Firebase correspond à celle que vous avez inscrite dans le portail Azure.
   
    ![Clé serveur Firebase][3]
   
    2. Vérifiez que vous avez configuré **l’ID de projet** dans le client. La valeur de **l’ID de projet** se trouve dans le tableau de bord Firebase.
   
    ![ID de projet Firebase][1]

## <a name="application-issues"></a>Problèmes de l’application
* **Balises et expressions de balise**

    Si vous utilisez des balises ou des expressions de balise pour segmenter votre public, il est possible que lorsque vous envoyez la notification, aucune cible ne soit trouvée, selon les balises/expressions de balise que vous spécifiez dans votre appel d’envoi. 
    
    Lorsque vous envoyez une notification, vérifiez que les balises de vos inscriptions correspondent. Ensuite, vérifiez l’accusé de réception uniquement des clients disposant de ces inscriptions. 
    
    Par exemple, si toutes vos inscriptions auprès de Notification Hubs ont été effectuées à l’aide de la balise « Politique » et que vous envoyez une notification avec la balise « Sport », la notification n’est envoyée à aucun appareil. Un cas complexe peut impliquer des expressions de balise auxquelles vous vous êtes inscrit à l’aide de « Balise A » OU « Balise B », alors que vous ciblez « Balise A ET Balise B » lors de l’envoi des notifications. La section de conseils d’autodiagnostic plus loin dans cet article vous explique comment vérifier vos inscriptions et leurs balises. 

* **Problèmes liés aux modèles**

    Si vous utilisez des modèles, suivez bien les instructions décrites dans la section [Modèles]. 

* **Inscriptions non valides**

    Si le hub de notification a été configuré correctement, et si les balises ou les expressions de balise ont été correctement utilisées, des cibles valides sont trouvées. Les notifications doivent être envoyées à ces cibles. Notification Hubs déclenche ensuite plusieurs traitements en parallèle. Chaque lot envoie des messages à un ensemble d’inscriptions. 

    > [!NOTE]
    > Étant donné que plusieurs traitements sont effectués en parallèle, l’ordre dans lequel les notifications sont remises n’est pas garanti. 

    Notification Hubs est optimisé pour le modèle de remise de messages de type « une fois maximum ». Nous tentons une déduplication de sorte qu’aucune notification ne soit remise plus d’une fois au même périphérique. Pour nous en assurer, nous examinons les inscriptions et nous vérifions qu’un seul message est envoyé par identificateur d’appareil avant son envoi au service de notifications Push. 

    Comme chaque lot est envoyé au service de notifications Push, qui à son tour accepte et valide les inscriptions, il est possible que le service de notifications Push détecte une erreur dans une ou plusieurs inscriptions d’un lot. Dans ce cas, le service de notifications Push retourne une erreur à Notification Hubs, et le processus s’arrête. Le service de notifications Push abandonne alors l’intégralité du lot. Ceci est particulièrement vrai pour Apple Push Notification Service qui utilise un protocole de flux TCP. 

    Le service est optimisé pour la remise de type « une fois maximum ». Toutefois, dans ce cas, l’inscription défaillante est supprimée de la base de données. Ensuite, la remise des notifications est retentée pour les appareils restants du lot.

    Pour plus d’informations sur les messages d’erreur concernant l’échec d’une tentative de remise pour une inscription, vous pouvez utiliser les API REST de Notification Hubs [Per Message Telemetry: Get Notification Message Telemetry](https://msdn.microsoft.com/library/azure/mt608135.aspx) (Télémétrie par message : télémétrie d’obtention de messages de notification) et [PNS feedback](https://msdn.microsoft.com/library/azure/mt705560.aspx) (Commentaires du service de notifications Push). Pour obtenir un exemple de code, consultez [l’exemple d’API REST d’envoi](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/SendRestExample).

## <a name="push-notification-service-issues"></a>Problèmes liés au service de notifications Push
Une fois que le message de notification a été reçu par le service de notifications Push de la plateforme, le service de notifications Push est chargé de remettre la notification à l’appareil. À ce stade, Notification Hubs n’intervient pas et n’a aucun contrôle sur le moment de livraison ou la livraison en elle-même à l’appareil. 

Les services de notifications de plateforme étant très performants, les notifications envoyées par le service de notifications Push arrivent généralement sur les appareils en quelques secondes. Si le service de notifications Push subit une limitation, Notification Hubs applique une stratégie d’interruption exponentielle. Si le service de notifications Push reste inaccessible pendant 30 minutes, l’une de nos stratégies consiste à faire expirer puis à supprimer définitivement ces messages. 

Si un service de notifications Push tente de remettre une notification alors que l’appareil est hors connexion, la notification est stockée par le service pendant une période limitée. La notification est envoyée à l’appareil lorsque celui-ci est de nouveau disponible. 

Pour chaque application, seule une notification récente est stockée. Si plusieurs notifications sont envoyées lorsque l’appareil est hors connexion, chaque nouvelle notification provoque la suppression de la précédente. Ce comportement consistant à ne conserver que la dernière notification est appelé *fusion des notifications* dans Apple Push Notification Service et *réduction* dans Firebase Cloud Messaging (qui utilise une clé de réduction). Si l’appareil reste hors connexion pendant une longue période, les notifications qui ont été stockées pour l’appareil sont supprimées. Pour plus d’informations, consultez [Présentation d’Apple Push Notification Service] et [À propos des messages Firebase Cloud Messaging].

Avec Azure Notification Hubs, vous pouvez passer une clé de fusion via un en-tête HTTP à l’aide de l’API générique SendNotification. Par exemple, pour le SDK .NET, vous devez utiliser **SendNotificationAsync**. L’API SendNotification accepte également les en-têtes HTTP qui sont passés tels quels au service de notifications Push correspondant. 

## <a name="self-diagnosis-tips"></a>Conseils d’autodiagnostic
Voici des méthodes permettant de déterminer la cause racine des pertes de notifications dans Notification Hubs :

### <a name="verify-credentials"></a>Vérification des informations d’identification
* **Service de notifications Push - Portail des développeurs**
   
    Vérifiez les informations d’identification dans le portail des développeurs du service de notifications Push correspondant (Apple Push Notification Service, Firebase Cloud Messaging, service de notification Windows, etc.). Pour plus d’informations, consultez [Bien démarrer avec Azure Notification Hubs].

* **Portail Azure**
   
    Pour vérifier et comparer les informations d’identification avec celles que vous avez obtenues via le portail des développeurs du service de notifications Push, dans le portail Azure, accédez à l’onglet **Stratégies d’accès**. 
   
    ![Portail Azure - Stratégies d’accès][4]

### <a name="verify-registrations"></a>Vérification des inscriptions
* **Visual Studio**
   
    Si vous utilisez Visual Studio à des fins de développement, vous pouvez vous connecter à Azure via l’Explorateur de serveurs pour afficher et gérer un ensemble de services Azure, y compris Notifications Hubs. Cela est particulièrement utile pour votre environnement de développement et de test. 
   
    ![Explorateur de serveurs Visual Studio.][9]
   
    Vous pouvez afficher et gérer toutes les inscriptions de votre hub, et les classer en fonction de leur plateforme, leur type (native ou basée sur un modèle), leurs balises, leur identificateur de service de notifications Push, leur ID d’inscription et leur date d’expiration. Dans cette page, vous pouvez également modifier une inscription. Ceci est particulièrement utile pour la modification des balises. 
   
    ![Inscriptions des appareils dans Visual Studio][8]
   
   > [!NOTE]
   > Utilisez Visual Studio pour modifier les inscriptions uniquement pendant le développement ou les tests, et avec un nombre limité d’inscriptions. Si vous avez besoin de modifier vos inscriptions en bloc, utilisez la fonctionnalité d’exportation et d’importation des inscriptions décrite dans [Exportation et modification des inscriptions en bloc](https://msdn.microsoft.com/library/dn790624.aspx).
   > 
   > 
* **Explorateur Service Bus**
   
    De nombreux clients utilisent [Service Bus Explorer] pour afficher et gérer leur hub de notification. Service Bus Explorer est un projet open source. Pour obtenir des exemples, consultez [Exemples de code Service Bus Explorer].

### <a name="verify-message-notifications"></a>Vérification des notifications de messages
* **Portail Azure**
   
    Pour envoyer une notification de test à vos clients sans backend de service opérationnel, sous **Support + dépannage**, sélectionnez **Envoi de test**. 
   
    ![Fonctionnalité Envoi de test dans Azure][7]
* **Visual Studio**
   
    Vous pouvez également envoyer des notifications de test à partir de Visual Studio.
   
    ![Fonctionnalité Envoi de test dans Visual Studio][10]
   
    Pour plus d’informations sur l’utilisation de Notification Hubs avec l’Explorateur de serveurs Visual Studio, consultez les articles suivants : 
   
   * [Afficher les inscriptions d’appareils pour les hubs de notification]
   * [Deep dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3]
   * [Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>Résoudre les échecs de remise de notification et analyser les résultats des notifications
**Propriété EnableTestSend**

Lorsque vous envoyez une notification via Notification Hubs, la notification est d’abord mise en file d’attente en vue d’être traitée dans Notification Hubs. Notification Hubs détermine les cibles qui conviennent, puis envoie la notification au service de notifications Push. Si vous utilisez l’API REST ou l’un des SDK clients, le retour réussi de votre appel d’envoi signifie donc uniquement que le message a été correctement placé dans la file d’attente de Notification Hubs. Vous ne savez pas exactement ce qu’il s’est passé lorsque Notification Hubs a finalement envoyé le message au service de notifications Push. 

Si votre notification n’arrive pas jusqu’à l’appareil client, il est possible qu’une erreur se soit produite au moment où Notification Hubs a tenté de remettre le message au service de notifications Push. Par exemple, la taille de la charge utile peut dépasser le seuil maximal autorisé par le service de notifications Push, ou les informations d’identification configurées dans Notification Hubs peuvent ne pas être valides. 

Pour comprendre ce qu’il s’est passé lors d’une erreur du service de notifications Push, vous pouvez utiliser la propriété [EnableTestSend]. Cette propriété est activée automatiquement lorsque vous envoyez des messages de test à partir du portail ou du client Visual Studio. Elle vous permet d’afficher des informations de débogage détaillées. Vous pouvez également utiliser cette propriété via l’API. Actuellement, vous pouvez l’utiliser dans le SDK .NET. Il est prévu qu’elle soit ajoutée à tous les SDK clients. 

Pour utiliser la propriété **EnableTestSend** avec l’appel REST, ajoutez un paramètre de chaîne de requête appelé *test* à la fin de votre appel d’envoi. Par exemple :  

    https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test

**Exemple (Kit de développement logiciel (SDK) .NET)**

Voici un exemple d’utilisation du SDK .NET pour envoyer une notification (toast) sous forme de fenêtre contextuelle native :

```csharp
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
    var result = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(result.State);
```

À la fin de l’exécution, **result.State** indique seulement la valeur **Enqueued**. Les résultats ne fournissent pas d’informations sur ce qui est arrivé à votre notification Push. 

Ensuite, vous pouvez utiliser la propriété booléenne **EnableTestSend**. Utilisez la propriété **EnableTestSend** lorsque vous initialisez **NotificationHubClient** pour obtenir des informations détaillées sur les erreurs du service de notifications Push qui se produisent lors de l’envoi de la notification. L’appel d’envoi met davantage de temps à être retourné, car il doit attendre que Notification Hubs ait remis la notification au service de notifications Push pour déterminer le résultat. 

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

**Exemple de sortie**

    DetailedStateAvailable
    windows
    7619785862101227384-7840974832647865618-3
    The Token obtained from the Token Provider is wrong

Ce message indique que des informations d’identification non valides sont configurées dans Notification Hubs, ou qu’il existe un problème avec les inscriptions du hub. Nous vous recommandons de supprimer cette inscription et de laisser le client la recréer avant d’envoyer le message. 

> [!NOTE]
> L’utilisation de la propriété **EnableTestSend** entraîne une limitation importante. Utilisez cette option uniquement dans un environnement de développement et de test, et avec un nombre limité d’inscriptions. Les notifications de débogage sont envoyées à un maximum de 10 appareils. De plus, le traitement des envois de débogage est limité à 10 envois par minute. 
> 
> 

### <a name="review-telemetry"></a>Révision de la télémétrie
* **Utiliser le portail Azure**
   
    Le portail vous permet d’obtenir un aperçu rapide de toutes les activités sur votre hub de notification. 
   
    1. Sous l’onglet **Vue d’ensemble**, vous pouvez voir les inscriptions, les notifications et les erreurs regroupées par plateforme. 
   
        ![Tableau de bord de Notification Hubs][5]
   
    2. Sous l’onglet **Surveiller**, vous pouvez ajouter de nombreuses autres métriques relatives à la plateforme. Vous pouvez ainsi obtenir des informations plus précises sur les erreurs liées au service de notifications Push qui sont retournées lorsque Notification Hubs tente d’envoyer la notification au service de notifications Push. 
   
        ![Journal d’activités - Portail Azure][6]
   
    3. Commencez par consultez les **messages entrants**, les **opérations d’inscription** et les **notifications réussies**. Ensuite, accédez à l’onglet de la plateforme pour examiner les erreurs relatives au service de notifications Push. 
   
    4. Si les paramètres d’authentification du hub de notification sont incorrects, le message **Erreur d’authentification PNS** s’affiche. Dans ce cas, il est nécessaire de vérifier les informations d’identification du service de notifications Push. 

* **Accès par programme**

    Pour plus d’informations sur l’accès par programmation, consultez les articles suivants : 

    * [Accès par programmation à la télémétrie]  
    * [Exemple d’accès à la télémétrie via les API] 

    > [!NOTE]
    > Plusieurs fonctionnalités de télémétrie, comme l’exportation et l’importation des inscriptions, et l’accès à la télémétrie via des API, sont disponibles uniquement avec le niveau de service Standard. Si vous tentez d’utiliser ces fonctionnalités avec le niveau de service Gratuit ou De base, un message d’exception s’affiche lorsque vous utilisez le SDK, et une erreur HTTP 403 (Refusé) s’affiche si vous utilisez les fonctionnalités directement dans les API REST. 
    >
    >Pour utiliser les fonctionnalités de télémétrie, vérifiez d’abord dans le portail Azure que vous utilisez le niveau de service Standard.  
> 
> 

<!-- IMAGES -->
[0]: ./media/notification-hubs-diagnosing/Architecture.png
[1]: ./media/notification-hubs-diagnosing/FCMConfigure.png
[3]: ./media/notification-hubs-diagnosing/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-diagnosing/PortalDashboard.png
[6]: ./media/notification-hubs-diagnosing/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-diagnosing/VSRegistrations.png
[9]: ./media/notification-hubs-diagnosing/VSServerExplorer.png
[10]: ./media/notification-hubs-diagnosing/VSTestNotification.png

<!-- LINKS -->
[Présentation de Notification Hubs]: notification-hubs-push-notification-overview.md
[Bien démarrer avec Azure Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Modèles]: https://msdn.microsoft.com/library/dn530748.aspx 
[Présentation d’Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[À propos des messages Firebase Cloud Messaging]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: http://msdn.microsoft.com/library/dn790624.aspx
[Service Bus Explorer]: https://msdn.microsoft.com/library/dn530751.aspx#sb_explorer
[Exemples de code Service Bus Explorer]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[Afficher les inscriptions d’appareils pour les hubs de notification]: http://msdn.microsoft.com/library/windows/apps/xaml/dn792122.aspx 
[Deep dive: Visual Studio 2013 Update 2 RC and Azure SDK 2.3]: http://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs 
[Announcing release of Visual Studio 2013 Update 3 and Azure SDK 2.4]: http://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/ 
[EnableTestSend]: https://docs.microsoft.com/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Accès par programmation à la télémétrie]: http://msdn.microsoft.com/library/azure/dn458823.aspx
[Exemple d’accès à la télémétrie via les API]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel

