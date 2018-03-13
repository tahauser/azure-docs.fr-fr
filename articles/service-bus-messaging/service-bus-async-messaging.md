---
title: Messagerie asynchrone Service Bus | Microsoft Docs
description: Description de la messagerie asynchrone Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: f1435549-e1f2-40cb-a280-64ea07b39fc7
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: e48e95d99847e68bdb218b341ad2fbcd44eb31f4
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Modèles de messagerie asynchrone et haute disponibilité

Une messagerie asynchrone peut être mise en œuvre de différentes façons. Avec les files d’attente, rubriques et abonnements, Azure Service Bus prend en charge des opérations asynchrones en utilisant un mécanisme de stockage et de transfert. En cas de fonctionnement normal (synchrone), des messages sont envoyés à des files d’attente et des rubriques et vous recevez des messages de la part de files d’attente et d’abonnements. Les applications que vous rédigez dépendent de la disponibilité permanente de ces entités. Lorsque, suite à certaines circonstances, l’état de l’entité change, vous devez disposer d’une entité à fonctionnalités réduites pouvant satisfaire la plupart des besoins.

Les applications utilisent en général des modèles de messagerie asynchrones autorisant un certain nombre de scénarios de communication. Vous pouvez créer des applications dans lesquelles les clients peuvent envoyer des messages aux services, même si le service n’est pas en cours d’exécution. Pour les applications soumises à des pics de communication, une file d’attente peut permettre de répartir la charge en fournissant un emplacement pour mettre les communications en mémoire tampon. Enfin, vous avez accès à un équilibreur de charge simple mais efficace pour distribuer les messages sur plusieurs ordinateurs.

Pour maintenir la disponibilité de toutes ces entités, vous devez trouver des moyens de faire apparaître ces entités comme indisponibles pour un système de messagerie durable. En règle générale, l’entité devient indisponible pour les applications que nous écrivons pour différentes raisons :

* Impossible d’envoyer des messages
* Impossible de recevoir des messages
* Impossible de gérer (créer, récupérer, mettre à jour ou supprimer) des entités.
* Impossible de contacter le service.

Pour chacune de ces erreurs, différents modes d’échec existent et permettent à une application de continuer de fonctionner à un niveau de fonctionnalités limité. Par exemple, un système qui peut envoyer des messages mais pas de les recevoir peut encore recevoir des commandes des clients, mais ne peut pas traiter ces commandes. Cette rubrique aborde les problèmes qui peuvent se présenter et explique comment y remédier. Service Bus présente un certain nombre de mesures d’atténuation à votre disposition, et cette rubrique traite également des règles régissant l’utilisation de ces actions.

## <a name="reliability-in-service-bus"></a>Fiabilité de Service Bus
Il existe plusieurs méthodes permettant de gérer les problèmes de messagerie et d’entités et certaines instructions régissent l’utilisation appropriée de ces actions de correction. Pour comprendre ces instructions, vous devez d’abord connaître les défaillances susceptibles de se produire dans Service Bus. En raison de la conception des systèmes Azure, tous ces problèmes sont en général éphémères. À un niveau élevé, les différentes causes d’indisponibilité sont les suivantes :

* Limitation d’un système externe dont Service Bus dépend. La limitation se produit à la suite d’interactions avec les ressources de stockage et de calcul.
* Problème d’un système dont Service Bus dépend. Par exemple, une partie donnée du stockage peut rencontrer des problèmes.
* Défaillance de Service Bus dans un sous-système unique Dans ce cas, un nœud de calcul peut passer à l’état incohérent et redémarrer seul, ce qui fait que toutes les entités desservies doivent équilibrer leur charge sur d’autres nœuds. Pendant un bref laps de temps, le traitement des messages peut se trouver ralenti.
* Échec de Service Bus dans un centre de données Azure. Il s’agit d’une « défaillance irrémédiable », et le système reste inaccessible pendant plusieurs minutes, voire quelques heures.

> [!NOTE]
> Le terme **stockage** peut désigner le stockage Azure et SQL Azure.
> 
> 

Service Bus propose un certain nombre de mesures d’atténuation des problèmes mentionnés ci-dessus. Les sections suivantes décrivent chaque problème et les actions correctives correspondantes.

### <a name="throttling"></a>Limitation
Avec Service Bus, la limitation permet une gestion coopérative de la vitesse des messages. Chaque nœud Service Bus héberge de nombreuses entités. Chacune de ces entités effectue des demandes sur le système en termes de processeur, de mémoire, de stockage et autres. Lorsqu’une de ces facettes détecte une utilisation dépassant les seuils définis, Service Bus peut refuser une demande donnée. L’appelant reçoit alors une exception [ServerBusyException][ServerBusyException] et refait une tentative après 10 secondes.

Pour remédier au problème, le code doit lire l’erreur et stopper toute nouvelle tentative du message pendant au moins 10 secondes. Dans la mesure où l’erreur peut se produire sur différents éléments de l’application client, chaque élément doit exécuter la nouvelle tentative de manière indépendante. Le code peut réduire la probabilité de limitation en activant le partitionnement sur une file d’attente ou une rubrique.

### <a name="issue-for-an-azure-dependency"></a>Problème sur une dépendance Azure
D’autres composants dans Azure peuvent parfois rencontrer des problèmes de service. Par exemple, lorsqu’un système Service Bus est en cours de mise à niveau, ce système peut provisoirement présenter des fonctionnalités réduites. Pour résoudre ces types de problèmes, Service Bus procède régulièrement à des analyses et met en œuvre les solutions de correction. Des effets secondaires de ces mesures de correction peuvent apparaître. Par exemple, pour gérer les problèmes temporaires liés au stockage, Service Bus fait appel à un système qui permet aux opérations d’envoi de message de continuer de fonctionner manière cohérente. En raison de la nature de cette mesure de correction, un message envoyé peut prendre jusqu’à 15 minutes pour apparaître dans la file d’attente ou l’abonnement affecté et être prêt pour une opération de réception. En règle générale, la plupart des entités ne rencontrent pas ce problème. Toutefois, étant donné le nombre d’entités présentes dans Service Bus au sein d’Azure, cette opération est parfois nécessaire pour un petit sous-ensemble de clients de Service Bus.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Défaillance Service Bus dans un sous-système unique
Quelle que soit l’application, les circonstances peuvent générer une défaillance chez un composant interne de Service Bus. Lorsque Service Bus détecte cela, il rassemble les données de l’application pour vous aider à diagnostiquer ce qui est arrivé. Une fois les données collectées, l’application est redémarrée afin de revenir à un état cohérent. Ce processus est assez rapidement et peut faire en sorte qu’une entité semble indisponible pendant quelques minutes, bien que les interruptions de service par défaut soient beaucoup plus courtes.

Dans ce cas, l’application cliente génère une exception [System.TimeoutException][System.TimeoutException] ou [MessagingException][MessagingException]. Service Bus contient un correctif pour ce problème sous la forme d’un programme de nouvelle tentative cliente automatisée. Une fois la période de nouvelle tentative épuisée, si le message n’est pas remis, vous pouvez entamer des recherches à l’aide d’autres fonctionnalités, par exemple, les [espaces de noms associés][paired namespaces]. Les espaces de noms associés génèrent d’autres notifications, qui sont abordées dans cet article.

### <a name="failure-of-service-bus-within-an-azure-datacenter"></a>Panne de Service Bus dans un centre de données Azure.
La cause la plus probable de défaillance dans un centre de données Azure est l’échec du déploiement d’une mise à niveau de Service Bus ou d’un système dépendant. Au fur et à mesure que la plateforme évolue, l’éventualité de ce type de panne diminue. Une défaillance de centre de données peut également se produire pour les raisons suivantes :

* Panne électrique (l’alimentation électrique et les générateurs de courant sont interrompus).
* Connectivité (interruption d’Internet entre vos clients et Azure).

Dans les deux cas, une catastrophe naturelle ou une erreur humaine a provoqué le problème. Pour contourner ce problème et vous assurer que vous pouvez toujours envoyer des messages, vous pouvez utiliser des [espaces de noms associés][paired namespaces] pour acheminer les messages vers un second emplacement pendant la restauration de l’emplacement principal. Pour plus d’informations, consultez la section [Meilleures pratiques pour protéger les applications contre les pannes de Service Bus et les sinistres][Best practices for insulating applications against Service Bus outages and disasters].

## <a name="paired-namespaces"></a>Espaces de noms associés
La fonctionnalité d’[espaces de noms associés][paired namespaces] prend en charge les scénarios dans lesquels une entité Service Bus ou un déploiement dans un centre de données n’est plus disponible. Bien qu’un tel événement soit rare, les systèmes distribués toujours doivent être préparés aux pires scénarios. En règle générale, cet événement se produit lorsque certains éléments dont dépend Service Bus rencontrent un problème de courte durée. Pour maintenir la disponibilité des applications pendant une panne, les utilisateurs de Service Bus peuvent utiliser deux espaces de noms différents, de préférence dans des centres de données distincts, pour héberger leurs entités de messagerie. Le reste de cette section utilise la terminologie suivante :

* Espace de noms principal : espace de noms avec lequel l’application interagit pour envoyer et recevoir des opérations.
* Espace de noms secondaire : espace de noms servant de sauvegarde de l’espace de noms principal. La logique d’application n’interagit pas avec cet espace de noms.
* Intervalle de basculement : durée d’une panne permettant d’accepter les pannes normales avant que l’application bascule de l’espace de noms principal vers l’espace de noms secondaire.

Les espaces de noms associés prennent en charge la *disponibilité des fonctions d’envoi*. La disponibilité des fonctions d’envoi maintient la possibilité d’envoyer des messages. Pour utiliser la disponibilité d’envoi, votre application doit répondre aux exigences suivantes :

1. Les messages sont reçus uniquement à partir de l’espace de noms principal.
2. Les messages envoyés à une file d’attente ou rubrique donnée peuvent arriver dans le désordre.
3. Les messages au sein d’une session peuvent arriver dans le désordre. Ce comportement diffère du comportement normal des sessions. Cela signifie que votre application utilise des sessions pour regrouper les messages de façon logique.
4. L’état de la session est conservé uniquement au niveau de l’espace de noms principal.
5. La file d’attente principale peut se mettre en ligne et commencer à accepter des messages avant que la file d’attente secondaire ait placé tous les messages dans la file d’attente principale.

Les sections suivantes décrivent les API, la façon dont les API sont implémentées et présente des exemples de code qui utilisent la fonctionnalité. Notez que cette fonctionnalité est associée à des coûts.

### <a name="the-messagingfactorypairnamespaceasync-api"></a>API MessagingFactory.PairNamespaceAsync
La fonctionnalité d’espaces de noms associés inclut la méthode [PairNamespaceAsync][PairNamespaceAsync] sur la classe [Microsoft.ServiceBus.Messaging.MessagingFactory][Microsoft.ServiceBus.Messaging.MessagingFactory] :

```csharp
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Lorsque la tâche est terminée, l’association de l’espace de noms se termine elle aussi et est prête à fonctionner pour chaque entité [MessageReceiver][MessageReceiver], [QueueClient][QueueClient] ou [TopicClient][TopicClient] créée avec l’instance [MessagingFactory][MessagingFactory]. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][Microsoft.ServiceBus.Messaging.PairedNamespaceOptions] est la classe de base correspondant aux différents types d’associations disponibles avec l’objet [MessagingFactory][MessagingFactory]. Actuellement, la seule classe dérivée est la classe nommée [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions], qui met en œuvre les exigences associées à la disponibilité des envois. [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions] possède un ensemble de constructeurs qui s’appuient les uns sur les autres. En examinant le constructeur comportant le plus grand nombre de paramètres, vous pouvez comprendre le comportement des autres constructeurs.

```csharp
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Ces paramètres ont les significations suivantes :

* *secondaryNamespaceManager* : instance [NamespaceManager][NamespaceManager] initialisée pour l’espace de noms secondaire que la méthode [PairNamespaceAsync][PairNamespaceAsync] peut utiliser pour configurer l’espace de noms secondaire. Le gestionnaire d’espaces de noms sera utilisé pour obtenir la liste des files d’attente dans l’espace de noms et s’assurer que les files d’attente backlog requises existent. Si ce n’est pas le cas, ces files d’attente sont créées. [NamespaceManager][NamespaceManager] doit avoir la possibilité de créer un jeton avec la revendication **Gérer**.
* *messagingFactory* : instance [MessagingFactory][MessagingFactory] de l’espace de noms secondaire. L’objet [MessagingFactory][MessagingFactory] est utilisé pour procéder aux envois, et si la propriété [EnableSyphon][EnableSyphon] est définie sur **true**, pour recevoir des messages de la part des files d’attente.
* *backlogQueueCount* : nombre de files d’attente à créer. La valeur doit être au moins à 1. Lorsque vous envoyez des messages à la file d’attente, l’une d’entre elles est sélectionnée de manière aléatoire. Si vous définissez la valeur sur 1, une seule file d’attente peut être utilisée. Lorsque cela se produit et que la seule file d’attente génère des erreurs, le client ne peut pas faire de tentative avec une autre file d’attente et risque de ne pas envoyer votre message. Nous vous recommandons de définir cette valeur sur une valeur supérieure et la valeur par défaut est de 10. Vous pouvez lui attribuer une valeur supérieure ou inférieure selon la quantité de données que votre application envoie quotidiennement. Chaque file d’attente peut contenir jusqu’à 5 Go de messages.
* *failoverInterval* : durée pendant laquelle vous allez accepter les défaillances sur l’espace de noms principal avant de basculer chaque entité sur l’espace de noms secondaire. Les basculements se produisent sur la base entité par entité. Les entités dans un espace de noms unique résident fréquemment sur différents nœuds au sein de Service Bus. La défaillance d’une entité n’implique pas nécessairement la défaillance d’une autre. Vous pouvez définir cette valeur sur [System.TimeSpan.Zero][System.TimeSpan.Zero] pour basculer sur le second espace de noms tout de suite après votre première défaillance non temporaire. Les défaillances qui déclenchent le minuteur de basculement sont les exceptions [MessagingException][MessagingException] dans lesquelles la propriété [IsTransient][IsTransient] a la valeur false ou [System.TimeoutException][System.TimeoutException]. D’autres exceptions, telles que [UnauthorizedAccessException][UnauthorizedAccessException] ne provoquent pas de basculement, car elles signalent que le client est configuré de façon incorrecte. Une exception [ServerBusyException][ServerBusyException] n’entraîne pas de basculement, car le modèle correct consiste à attendre 10 secondes, puis à renvoyer le message.
* *enableSyphon* : indique que cette association particulière doit siphonner les messages de l’espace de noms secondaire et les placer sur l’espace de noms principal. En général, les applications qui envoient des messages doivent définir cette valeur sur**false** ; les applications qui reçoivent des messages doivent définir cette valeur sur **true**. La raison est que souvent, les destinataires de messages sont moins nombreux que les expéditeurs. En fonction du nombre de destinataires, vous pouvez choisir d’utiliser une seule instance d’application pour gérer les tâches de siphon. L’utilisation de nombreux destinataires a un coût pour chaque file d’attente backlog.

Pour utiliser le code, créez une instance principale de [MessagingFactory][MessagingFactory], une instance secondaire de [MessagingFactory][MessagingFactory], une instance secondaire de [NamespaceManager][NamespaceManager] et une instance de [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions]. L’appel peut se réduire à ce qui suit :

```csharp
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Lorsque la tâche retournée par la méthode [PairNamespaceAsync][PairNamespaceAsync] se termine, tout est configuré et prêt à l’emploi. Avant que la tâche soit renvoyée, il se peut que vous n’ayez pas terminé toutes les tâches en arrière-plan nécessaires pour que l’association fonctionne correctement. Par conséquent, vous ne devez pas commencer à envoyer les messages avant que la tâche ne revienne. En cas de défaillance (informations d’identification incorrectes ou échec de création des files d’attente), ces exceptions seront levées lorsque la tâche est achevée. Une fois que la tâche est retournée, vérifiez que les files d’attente ont été détectées ou créées en examinant la propriété [BacklogQueueCount][BacklogQueueCount] de votre instance [SendAvailabilityPairedNamespaceOptions][SendAvailabilityPairedNamespaceOptions]. Pour le code qui précède, cette opération se présente comme suit :

```csharp
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris les principes fondamentaux de la messagerie asynchrone dans Service Bus, approfondissez le sujet sur les [espaces de noms associés][paired namespaces].

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_PairNamespaceAsync_Microsoft_ServiceBus_Messaging_PairedNamespaceOptions_
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_EnableSyphon
[System.TimeSpan.Zero]: https://msdn.microsoft.com/library/system.timespan.zero.aspx
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient
[UnauthorizedAccessException]: https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions?redirectedfrom=MSDN#Microsoft_ServiceBus_Messaging_SendAvailabilityPairedNamespaceOptions_BacklogQueueCount
[paired namespaces]: service-bus-paired-namespaces.md
