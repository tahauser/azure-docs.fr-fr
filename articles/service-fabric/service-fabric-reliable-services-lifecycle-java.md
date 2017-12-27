---
title: "Vue d’ensemble du cycle de vie de Reliable Services dans Azure Service Fabric | Microsoft Docs"
description: "En savoir plus sur les différents événements de cycle de vie de Reliable Services dans Service Fabric"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 93fd003ff5ba7673e5a719fb1ced0cbb97034610
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="reliable-services-lifecycle-overview"></a>Vue d’ensemble du cycle de vie de Reliable Services
> [!div class="op_single_selector"]
> * [C# sur Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java sur Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Lorsque l’on songe aux cycles de vie de Reliable Services, les principes de base du cycle de vie sont les plus importants. En général :

* Lors du démarrage
  * Les services sont construits
  * Ils ont l’opportunité de se construire et de renvoyer zéro ou plusieurs écouteurs
  * Les écouteurs retournés sont ouverts, ce qui permet la communication avec le service
  * La méthode runAsync du service est appelée, ce qui permet au service d’exécuter des tâches de longue durée ou d’arrière-plan
* Lors de l’arrêt
  * Le jeton d’annulation passé à runAsync est annulé et les écouteurs sont fermés
  * Une fois cette opération terminée, l’objet de service lui-même est détruit

Il existe plus d’informations sur l’ordre exact de ces événements. En particulier, l’ordre des événements peut varier légèrement selon que le Reliable Service est un service sans état ou avec état. En outre, pour les services avec état, nous devons gérer le scénario d’échange principal. Au cours de cette séquence, le rôle principal est transféré vers un autre réplica (ou revient) sans que le service ne s’arrête. Enfin, nous devons penser aux conditions d’erreur ou d’échec.

## <a name="stateless-service-startup"></a>Démarrage de service sans état
Le cycle de vie d’un service sans état est assez simple. Voici l’ordre des événements :

1. Le service est construit
2. Ensuite, deux choses se produisent en parallèle :
    - `StatelessService.createServiceInstanceListeners()` est appelée et les écouteurs retournés sont ouverts (`CommunicationListener.openAsync()` est appelée sur chaque écouteur)
    - La méthode runAsync (`StatelessService.runAsync()`) du service est appelée
3. Si elle est présente, la méthode onOpenAsync propre au service est appelée (en l’occurrence, c’est `StatelessService.onOpenAsync()` qui est appelée). Il s’agit d’un remplacement rare, mais il est disponible).

Il est important de noter qu’il n’existe pas d’ordre particulier entre les appels pour créer et ouvrir les écouteurs, et runAsync. Les écouteurs peuvent s’ouvrir avant que la méthode runAsync ne soit démarrée. De même, runAsync peut être appelée avant que les écouteurs de communication ne soient ouverts ou même construits. Si une synchronisation est nécessaire, elle est considérée comme un exercice à exécuter par le responsable d’implémentation. Solutions courantes :

* Parfois, les écouteurs ne peuvent pas fonctionner avant que d’autres informations ne soient créées ou un travail effectué. Pour les services sans état, ce travail peut généralement s’exécuter dans le constructeur du service, au cours de l’appel `createServiceInstanceListeners()`, ou dans le cadre de la construction de l’écouteur lui-même.
* Parfois, le code de runAsync ne démarre pas tant que les écouteurs ne sont pas ouverts. Dans ce cas, une coordination supplémentaire est nécessaire. Une solution courante consiste à utiliser un indicateur dans les écouteurs, indiquant quand ceux-ci ont terminé, ce qui est vérifié dans runAsync avant de poursuivre le travail réel.

## <a name="stateless-service-shutdown"></a>Arrêt de service sans état
Lorsque vous arrêtez un service sans état, le même modèle est suivi dans l’ordre inverse :

1. En parallèle
    - Les écouteurs ouverts sont fermés (`CommunicationListener.closeAsync()` est appelée sur chaque écouteur)
    - Le jeton d’annulation passé à `runAsync()` est annulé (en vérifiant que la propriété `isCancelled` du jeton d’annulation retourne la valeur true et que, si elle est appelée, la méthode `throwIfCancellationRequested` du jeton lève une `CancellationException`).
2. Lorsque `closeAsync()` se termine sur chaque écouteur et que `runAsync()` se termine également, la méthode `StatelessService.onCloseAsync()` du service est appelée, le cas échéant (il s’agit à nouveau d’un remplacement rare).
3. Lorsque `StatelessService.onCloseAsync()` se termine, l’objet de service est détruit

## <a name="stateful-service-startup"></a>Démarrage de service avec état
Les services avec état ont un modèle semblable aux services sans état, avec quelques modifications. Pour démarrer un service avec état, l’ordre des événements est le suivant :

1. Le service est construit.
2. `StatefulServiceBase.onOpenAsync()` est appelée. Il est rare de remplacer cet appel dans le service.
3. Les événements suivants se produisent en parallèle :
    - `StatefulServiceBase.createServiceReplicaListeners()` est appelé. 
      - Si le service est un service principal, tous les écouteurs retournés sont ouverts. `CommunicationListener.openAsync()` est appelé sur chaque écouteur.
      - Si le service est un service secondaire, seuls les écouteurs marqués comme `listenOnSecondary = true` sont ouverts. Il est plus rare d’avoir des écouteurs ouverts dans les services secondaires.
    - Si le service est un service principal, la méthode `StatefulServiceBase.runAsync()` du service est appelée.
4. Une fois que tous les appels `openAsync()` de l’écouteur de réplica sont terminés et que `runAsync()` est appelé, `StatefulServiceBase.onChangeRoleAsync()` est appelé. Il est rare de remplacer cet appel dans le service.

Comme pour les services sans état, il n’y a aucune coordination entre l’ordre dans lequel les écouteurs sont créés et ouverts, et le moment auquel **runAsync** est appelé. Si vous avez besoin d’une coordination, les solutions à utiliser sont similaires. Il existe un autre cas pour un service avec état. Supposons que les appels qui arrivent sur les écouteurs de communication nécessitent des informations conservées dans des [collections fiables](service-fabric-reliable-services-reliable-collections.md). Étant donné que les écouteurs de communication peuvent s’ouvrir avant que les collections fiables ne soient accessibles en lecture ou en écriture, et avant que **runAsync** ne démarre, une coordination supplémentaire est nécessaire. La solution la plus simple et la plus courante est que les écouteurs de communication retournent un code d’erreur que le client utilise pour relancer la requête.

## <a name="stateful-service-shutdown"></a>Arrêt de service avec état
Comme pour les services sans état, les événements de cycle de vie lors de l’arrêt sont les mêmes que lors du démarrage, mais dans l’ordre inverse. Lorsqu’un service avec état est arrêté, les événements suivants se produisent :

1. En parallèle :
    - Les écouteurs ouverts sont fermés. `CommunicationListener.closeAsync()` est appelé sur chaque écouteur.
    - Le jeton d’annulation passé à `runAsync()` est annulé. Un appel à la méthode `isCancelled()` du jeton d’annulation retourne la valeur true et, si elle est appelée, la méthode `throwIfCancellationRequested()` du jeton lève une `OperationCanceledException`.
2. Quand `closeAsync()` se termine sur chaque écouteur et que `runAsync()` se termine également, la méthode `StatefulServiceBase.onChangeRoleAsync()` du service est appelée. Il est rare de remplacer cet appel dans le service.

   > [!NOTE]  
   > Ce n’est que si ce réplica est un réplica principal que vous devez attendre que **runAsync** se termine.

3. Une fois la méthode `StatefulServiceBase.onChangeRoleAsync()` terminée, la méthode `StatefulServiceBase.onCloseAsync()` est appelée. Il s’agit d’un remplacement rare, mais possible.
3. Quand `StatefulServiceBase.onCloseAsync()` se termine, l’objet de service est détruit.

## <a name="stateful-service-primary-swaps"></a>Échanges de réplica principal de service avec état
Pendant l’exécution d’un service avec état, seuls les réplicas principaux de ce service avec état ont leurs écouteurs de communication ouverts et leur méthode **runAsync** appelée. Les réplicas secondaires sont construits, mais ne reçoivent aucun autre appel. Pendant l’exécution d’un service avec état, le réplica principal peut changer. Que cela signifie-t-il en termes des événements du cycle de vie qu’un réplica peut voir ? Le comportement que voit le réplica avec état varie selon qu’il s’agit du réplica qui est rétrogradé ou promu au cours de l’échange.

### <a name="for-the-primary-thats-demoted"></a>Pour le réplica principal rétrogradé
Pour le réplica principal rétrogradé, Service Fabric nécessite que ce réplica arrête de traiter des messages et quitte tout travail en arrière-plan qu’il exécute. Par conséquent, cette étape est similaire à l’arrêt du service. La différence est que le service n’est pas détruit ni fermé, car il est conservé en tant que réplica secondaire. Les API suivantes sont appelées :

1. En parallèle :
    - Les écouteurs ouverts sont fermés. `CommunicationListener.closeAsync()` est appelé sur chaque écouteur.
    - Le jeton d’annulation passé à `runAsync()` est annulé. Vérification que la méthode `isCancelled()` du jeton d’annulation retourne la valeur true et que, si elle est appelée, la méthode `throwIfCancellationRequested()` du jeton lève une `OperationCanceledException`.
2. Quand `closeAsync()` se termine sur chaque écouteur et que `runAsync()` se termine également, la méthode `StatefulServiceBase.onChangeRoleAsync()` du service est appelée. Il est rare de remplacer cet appel dans le service.

### <a name="for-the-secondary-thats-promoted"></a>Pour le réplica secondaire promu
De même, Service Fabric nécessite que le réplica secondaire promu commence à écouter les messages sur le réseau et qu’il démarre les tâches en arrière-plan qui le concernent. Par conséquent, ce processus est similaire à la création du service, à ceci près que le réplica lui-même existe déjà. Les API suivantes sont appelées :

1. En parallèle :
    - `StatefulServiceBase.createServiceReplicaListeners()` est appelé et les écouteurs retournés sont ouverts. `CommunicationListener.openAsync()` est appelé sur chaque écouteur.
    - La méthode `StatefulServiceBase.runAsync()` du service est appelée.
2. Une fois que tous les appels `openAsync()` de l’écouteur de réplica sont terminés et que `runAsync()` est appelé, `StatefulServiceBase.onChangeRoleAsync()` est appelé. Il est rare de remplacer cet appel dans le service.


### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problèmes courants se produisant pendant l’arrêt d’un service avec état et la rétrogradation du réplica principal
Service Fabric peut modifier le réplica principal d’un service avec état pour diverses raisons. Parmi les raisons les plus courantes figurent le [rééquilibrage des clusters](service-fabric-cluster-resource-manager-balancing.md) et la [mise à niveau des applications](service-fabric-application-upgrade.md). Pendant ces opérations (ainsi que lors de l’arrêt normal du service, comme lorsque le service a été supprimé), il est important que le service respecte le `cancellationToken`. 

Les services qui ne gèrent pas l’annulation « proprement » peuvent être confrontés à plusieurs problèmes. Ces opérations sont lentes, car Service Fabric attend que les services s’arrêtent normalement. Cela peut entraîner l’échec des mises à jour qui expirent et sont annulées. Si vous ne respectez pas le jeton d’annulation, vous pouvez également provoquer des clusters déséquilibrés. Les clusters sont déséquilibrés, car les nœuds deviennent actifs, mais les services ne peuvent pas être rééquilibrés, car leur déplacement prend trop de temps. 

Comme les services sont avec état, il est également probable qu’ils utilisent des [collections fiables](service-fabric-reliable-services-reliable-collections.md). Dans Service Fabric, lorsqu’un réplica principal est rétrogradé, l’une des premières choses qui se produisent est que l’accès en écriture à l’état sous-jacent est révoqué. Cela conduit à un deuxième type de problème qui peut avoir un impact sur le cycle de vie du service. Les collections retournent des exceptions selon le délai et selon que le réplica est déplacé ou arrêté. Ces exceptions doivent être gérées correctement. Les exceptions levées par Service Fabric sont soit permanentes [(`FabricException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception), soit passagères [(`FabricTransientException`)](https://docs.microsoft.com/en-us/java/api/system.fabric.exception._fabric_transient_exception). Les exceptions permanentes doivent être journalisées et levées, alors que les exceptions passagères peuvent être retentées selon une logique de nouvelle tentative.

Le fait de gérer des exceptions issues de l’utilisation de `ReliableCollections` conjointement aux événements du cycle de vie du service constitue une partie importante du test et de la validation d’un service fiable. Il est recommandé de toujours exécuter votre service sous une charge quand vous effectuez une mise à niveau et des [tests de chaos](service-fabric-controlled-chaos.md) avant de le déployer sur un environnement de production. Ces étapes de base vous permettent de vérifier que votre service est correctement implémenté et qu’il gère correctement les événements de cycle de vie.

## <a name="notes-on-service-lifecycle"></a>Remarques sur le cycle de vie du service
* La méthode `runAsync()` et l’appel `createServiceInstanceListeners/createServiceReplicaListeners` sont facultatifs. Un service peut avoir l’un des deux, les deux ou aucun. Par exemple, si le service effectue tout son travail en réponse aux appels d’utilisateur, il est inutile d’implémenter `runAsync()`. Seuls les écouteurs de communication et le code associé sont nécessaires. De même, la création et le renvoi d’écouteurs de communication sont facultatifs, car le service peut n’avoir que du travail en arrière-plan à exécuter et n’a donc besoin d’implémenter que `runAsync()`
* Il est valide pour un service de terminer `runAsync()` correctement et d’en revenir. Cela n’est pas considéré comme une condition d’échec et correspond à la fin du travail en arrière-plan du service. Pour les Reliable Services avec état, `runAsync()` est appelée à nouveau si le service a été rétrogradé depuis la fonction principale, puis promu de nouveau à la fonction principale.
* Si un service quitte `runAsync()` en levant une exception inattendue, il s’agit d’un échec, l’objet de service est arrêté et une erreur d’intégrité est signalée.
* S’il n’existe pas de limite de temps sur le retour de ces méthodes, vous perdez immédiatement la possibilité d’écrire, et vous ne pouvez donc pas effectuer de travail réel. Il est recommandé de procéder au renvoi aussi rapidement que possible dès la réception de la demande d’annulation. Si votre service ne répond pas à ces appels d’API dans un délai raisonnable, Service Fabric peut mettre fin à votre service. En général, cela se produit uniquement lors de mises à niveau d’application ou lorsqu’un service est en cours de suppression. Par défaut, ce délai d’attente est de 15 minutes.
* Les échecs dans le chemin d’accès `onCloseAsync()` entraînent l’appel de `onAbort()` qui constitue une opportunité de dernière chance pour le service de nettoyer et de libérer les ressources demandées.

## <a name="next-steps"></a>Étapes suivantes
* [Présentation de Reliable Services](service-fabric-reliable-services-introduction.md)
* [Démarrage rapide de Reliable Services](service-fabric-reliable-services-quick-start-java.md)
* [Utilisation avancée de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
