---
title: "Présentation du modèle de programmation Reliable Services de Service Fabric | Microsoft Docs"
description: "Apprenez-en plus sur le modèle de programmation Service fiable de Service Fabric et commencez à écrire vos propres services."
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: masnider;
ms.openlocfilehash: 3c583d99a63c13a0a2ab351f82a4f5ff6840788a
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="reliable-services-overview"></a>Présentation de Reliable Services
Azure Service Fabric simplifie l’écriture et la gestion de Reliable Services avec et sans état. Cette rubrique couvre :

* Le modèle de programmation Reliable Services pour les services avec et sans état.
* Les choix que vous devez faire lors de l’écriture d’un modèle Reliable Services.
* Des scénarios et exemples d’utilisation du modèle Reliable Services et de son mode d’écriture.

Les Services fiables sont l'un des modèles de programmation disponibles sous Service Fabric. L’autre est le modèle de programmation Reliable Actor, qui fournit un modèle de programmation Actor virtuel sur le modèle Reliable Services. Pour plus d’informations sur le modèle de programmation Reliable Actors, consultez l’article [Présentation du modèle Reliable Actors de Service Fabric](service-fabric-reliable-actors-introduction.md).

Service Fabric gère la durée de vie des services, depuis l’approvisionnement et le déploiement jusqu’à la mise à niveau et la suppression au moyen de la [gestion des applications Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Définition de Reliable Services
Reliable Services vous offre un modèle de programmation global, simple et puissant qui vous aidera à faire ressortir ce qui est important dans votre application. Avec le modèle de programmation Reliable Services, vous obtenez :

* Accès au reste des API de programmation de Service Fabric. Contrairement aux services Service Fabric modélisés en tant [qu’invités exécutables](service-fabric-deploy-existing-app.md), Reliable Services peut utiliser directement les autres API de Service Fabric. Les services peuvent ainsi :
  * interroger le système
  * créer des rapports sur l’intégrité des entités dans le cluster
  * recevoir des notifications sur les modifications de la configuration et du code
  * rechercher et communiquer avec d’autres services
  * (facultatif) utiliser les [Collections fiables](service-fabric-reliable-services-reliable-collections.md)
  * ...et accéder à de nombreuses autres fonctionnalités, toutes issues d’un modèle de programmation de première classe dans plusieurs langages de programmation.
* Un modèle simple pour exécuter votre propre code qui ressemble aux modèles de programmation auxquels vous êtes habitué. Votre code comporte un point d’entrée bien défini, et son cycle de vie est facile à gérer.
* Un modèle de communication enfichable. Utilisez le transport de votre choix, comme HTTP avec [l’API Web](service-fabric-reliable-services-communication-webapi.md), WebSockets, des protocoles TCP personnalisés, etc. Le modèle Reliable Services fournit de remarquables options prêtes à l’emploi que vous pouvez utiliser. Vous pouvez également définir les vôtres.
* Pour les services avec état, le modèle de programmation Reliable Services vous permet de stocker de manière cohérente et fiable votre droit d’état dans votre service à l’aide des [Collections fiables](service-fabric-reliable-services-reliable-collections.md). Les Collections fiables sont un ensemble simple de classes de collection hautement disponibles et fiables, qui sera familier à tous ceux qui ont utilisé les collections C#. Traditionnellement, les services nécessitaient des systèmes externes pour la gestion d’état fiable. Avec les Collections fiables, vous pouvez stocker l’état à proximité du calcul avec la disponibilité élevée et la fiabilité que vous attendez de la part de magasins externes hautement disponibles. De plus, ce modèle permet d’améliorer la latence, car vous effectuez la colocalisation du calcul et de l’état dont il a besoin pour fonctionner.

Regardez cette vidéo Microsoft Virtual Academy pour une vue d’ensemble de Reliable Services : <center>
<a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=HhD9566yC_4106218965">
<img src="./media/service-fabric-reliable-services-introduction/ReliableServicesVid.png" WIDTH="360" HEIGHT="244" />
</a>
</center>

## <a name="what-makes-reliable-services-different"></a>En quoi le modèle Reliable Services est-il différent ?
Les Services fiables dans Service Fabric sont différents des services que vous avez peut-être écrits auparavant. Service Fabric fournit la fiabilité, la disponibilité, la cohérence et l'évolutivité.

* **Fiabilité** : votre service reste opérationnel même dans des environnements non fiables où vos machines sont confrontées à des pannes ou des problèmes réseau, ou dans le cas où les services eux-mêmes rencontrent des erreurs et subissent un incident ou une panne. Pour les services avec état, l’état est conservé même en cas de panne du réseau ou d’autres incidents.
* **Disponibilité** : votre service est accessible et réactif. Service Fabric gère le nombre souhaité de copies en cours d’exécution.
* **Évolutivité** : les services sont dissociés du matériel spécifique, et peuvent croître ou diminuer en fonction des besoins au moyen de l’ajout ou de la suppression de matériel ou d’autres ressources. Les services peuvent être facilement partitionnés (surtout dans le cas des services avec état) pour s’assurer que le service peut s’adapter et répondre aux défaillances partielles. Les services peuvent être créés et supprimés de manière dynamique à l’aide du code, ce qui permet de lancer autant d’instances que nécessaire, par exemple en réponse aux demandes des clients. Enfin, Service Fabric encourage les services à être légers. Service Fabric autorise des milliers de services à être configurés dans un processus unique, plutôt que de nécessiter ou de consacrer des instances ou des processus entiers du système d'exploitation à une instance unique d’un service.
* **Cohérence** : toutes les informations stockées dans ce service sont garanties comme étant cohérentes. Cela s’applique également à plusieurs collections fiables au sein d’un service. Les modifications dans des collections au sein d’un service peuvent être effectuées de manière atomique au niveau transactionnel.

## <a name="service-lifecycle"></a>Cycle de vie du service
Que votre service soit avec état ou sans état, Reliable Services fournit un cycle de vie simple qui vous permet de rattacher rapidement votre code et de vous lancer.  Il vous suffit d’implémenter une ou deux méthodes pour que votre service soit opérationnel.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** : cette méthode correspond à l’emplacement dans lequel le service définit la ou les piles de communication qu’il souhaite utiliser. La pile de communication, par exemple [l’API Web](service-fabric-reliable-services-communication-webapi.md), définit le point de terminaison d’écoute ou les points de terminaison du service (mode d’accès des clients au service). Elle définit également comment les messages qui s’affichent interagissent avec le reste du code de service.
* **RunAsync** : cette méthode correspond à l’emplacement dans lequel votre service exécute sa logique métier et où il lance les tâches d’arrière-plan qui doivent s’exécuter pendant toute la durée de vie du service. Le jeton d'annulation fourni est un signal indiquant quand ce travail doit s'arrêter. Par exemple, si le service doit extraire des messages d’une file d’attente fiable et les traiter, c’est à cet emplacement que se passe ce travail.

Continuez votre lecture si vous débutez dans l’utilisation de Reliable Services. Si vous recherchez une procédure détaillée du cycle de vie de Reliable Services, vous pouvez consulter [cet article](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Exemples de service
En se basant sur ce modèle de programmation, observons rapidement deux services pour voir comment ces éléments s'imbriquent.

### <a name="stateless-reliable-services"></a>Services fiables sans état
Un service sans état est un service dans lequel l’état n’est pas conservé au sein du service entre les appels. Tout état présent est entièrement jetable et ne nécessite aucune synchronisation, réplication, persistance ou haute disponibilité.

Prenons pour exemple une calculatrice dépourvue de mémoire et qui reçoit tous les termes et les opérations à effectuer simultanément.

Dans ce cas, la méthode `RunAsync()` (C#) ou `runAsync()` (Java) du service peut être vide, car ce dernier n’a besoin d’effectuer aucun traitement de tâche en arrière-plan. Quand le service Calculatrice est créé, il renvoie un `ICommunicationListener` (C#) ou un `CommunicationListener` (Java) (par exemple [API web](service-fabric-reliable-services-communication-webapi.md)) qui ouvre un point de terminaison d’écoute sur un port. Ce point de terminaison d’écoute se raccorde aux différentes méthodes de calcul (exemple : « Add(n1, n2) ») qui définissent l’API publique de la calculatrice.

Lorsqu’un appel est effectué à partir d’un client, la méthode appropriée est appelée ; le service Calculatrice effectue les opérations sur les données fournies et renvoie le résultat. Il ne stocke aucun état.

Le fait de ne pas stocker d’état interne rend cet exemple de calculatrice simple, mais la plupart des services ne sont pas réellement sans état. Ils externalisent leur état vers un autre magasin. (Par exemple, toute application web qui s’appuie sur la conservation de l’état de session dans un magasin de stockage ou un cache n’est pas sans état.)

Un exemple courant illustrant comment les services sans état sont utilisés dans Service Fabric est celui d'un serveur frontal qui expose l'API publique pour une application Web. Le service frontal communique ensuite avec les services avec état pour terminer une demande d’utilisateur. Dans ce cas, les appels des clients sont dirigés vers un port connu, comme le port 80, où le service sans état écoute. Ce service sans état reçoit l’appel et détermine s’il provient d’une partie de confiance, et le service auquel il est destiné.  Le service sans état transfère alors l’appel vers la partition correcte du service avec état et attend une réponse. Lorsqu’il reçoit une réponse, il répond au client d’origine. Vous trouverez un exemple de ce type de service dans nos exemples [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/Actors/VisualObjectActor/VisualObjectWebService). Il ne s’agit que d’un seul exemple de ce modèle dans les exemples, vous en trouverez également d’autres dans d’autres exemples.

### <a name="stateful-reliable-services"></a>Services fiables avec état
Un service avec état est un service qui doit avoir une partie de l'état sans cesse cohérente et présente pour que le service fonctionne. Prenons pour exemple un service qui calcule constamment une moyenne mobile d’une valeur en fonction des mises à jour qu’il reçoit. Pour ce faire, il doit disposer de l’ensemble actuel des requêtes entrantes qu’il doit traiter et la moyenne actuelle. Tout service qui récupère, traite et stocke des informations dans un magasin externe (comme un magasin de tables ou d’objets blob Azure) est un service avec état. Il conserve seulement son état dans le magasin d’état externe.

Actuellement, la plupart des services stockent leur état en externe, car le magasin externe fournit la fiabilité, la disponibilité, l’évolutivité et la cohérence pour cet état. Dans Service Fabric, les services ne sont pas obligés de stocker leur état en externe. Service Fabric s’occupe de ces exigences pour le code de service et l’état du service.

> [!NOTE]
> La prise en charge de Reliable Services avec état n’est pas encore disponible sur Linux (pour C# ou Java).
>

Supposons que nous souhaitions écrire un service qui traite des images. Pour ce faire, le service récupère une image et la série de conversions à effectuer sur cette image. Ce service renvoie un écouteur de communication (par exemple, une API Web) qui expose une API telle que `ConvertImage(Image i, IList<Conversion> conversions)`. Lorsqu’il reçoit une requête, le service la stocke dans une `IReliableQueue` et renvoie un ID au client pour suivre la requête.

Dans ce service, `RunAsync()` pourrait être plus complexe. Le service a une boucle à l’intérieur de sa méthode `RunAsync()` qui extrait les requêtes de la `IReliableQueue` et effectue les conversions demandées. Les résultats sont stockés dans un `IReliableDictionary` de sorte que lorsque le client revient, il peut obtenir ses images converties. Pour garantir que, même en cas de problème, l’image n’est pas perdue, ce Reliable Service se sortirait de la file d’attente, exécuterait les conversions et stockerait le résultat, le tout dans une transaction unique. Dans ce cas, le message est supprimé de la file d’attente et les résultats sont stockés dans le dictionnaire de résultats uniquement lorsque les conversions sont terminées. Le service peut également extraire l’image de la file d’attente et la stocker immédiatement dans un magasin distant. Cela réduit la quantité d’état que le service doit gérer, mais augmente la complexité étant donné que le service doit conserver les métadonnées nécessaires pour gérer le magasin distant. Avec les deux approches, si quelque chose échoue au cours du traitement, la requête reste dans la file d’attente en attente d’être traitée.

Concernant ce service, il convient de noter qu’il ressemble à un service .NET normal. La seule différence est que les structures de données utilisées (`IReliableQueue` et `IReliableDictionary`) sont fournies par Service Fabric et sont plus fiables, disponibles et cohérentes.

## <a name="when-to-use-reliable-services-apis"></a>Quand utiliser les API Reliable Services
Si l’un des éléments suivants correspond aux besoins de votre service d’application, vous devez songer à utiliser les API Reliable Services :

* Vous souhaitez que le code de votre service (et éventuellement l’état) soit hautement disponible et fiable
* Vous avez besoin de garanties transactionnelles entre plusieurs unités d’état (par exemple, commandes et éléments de ligne de commande).
* L’état de votre application peut être naturellement modélisé sous forme de Files d’attente et de Dictionnaires fiables
* Le code ou l’état de votre application doit être hautement disponible avec des lectures et écritures à faible latence.
* Votre application doit contrôler la concurrence ou la granularité des opérations traitées dans une ou plusieurs Collections fiables.
* Vous souhaitez gérer les communications ou contrôler le schéma de partitionnement de votre service.
* Votre code a besoin d’un environnement d’exécution libre de threads.
* Votre application doit créer ou détruire des Files d’attente ou des Dictionnaires fiables ou des Services entiers de manière dynamique lors de l’exécution.
* Vous devez contrôler par programme les fonctionnalités de sauvegarde et de restauration fournies par Service Fabric pour l’état du service.
* Votre application doit conserver l’historique des modifications pour ses unités d’état.
* Vous souhaitez développer ou utiliser des fournisseurs d’état personnalisé développés par une tierce partie.

## <a name="next-steps"></a>Étapes suivantes
* [Démarrage rapide de Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Utilisation avancée de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
* [Modèle de programmation Reliable Actors](service-fabric-reliable-actors-introduction.md)
