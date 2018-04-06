---
title: Énumérer des acteurs sur Azure Service Fabric | Documents Microsoft
description: Découvrez comment énumérer des Reliable Actors et leurs métadonnées.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 037138f68e5c18822e4d7b3fa47591411e8a2407
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Avantages des Reliable Actors dans Service Fabric
Le service Reliable Actors permet à un client d’énumérer des métadonnées concernant les acteurs hébergés par le service. Le service d’acteur est un service avec état partitionné. L’énumération est donc effectuée par partition. Étant donné que chaque partition peut contenir de nombreux acteurs, l’énumération est renvoyée sous la forme d’un ensemble de résultats paginés. Les pages sont traitées en boucle jusqu’à ce qu’elles aient toutes été lues. L’exemple suivant montre comment créer une liste de tous les acteurs actifs dans une partition d’un service d’acteur :

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Étapes suivantes
* [Gestion des états d’acteur](service-fabric-reliable-actors-state-management.md)
* [Cycle de vie des acteurs et Garbage Collection](service-fabric-reliable-actors-lifecycle.md)
* [Documentation de référence de l’API Actors](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Exemple de code .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Exemple de code Java](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
