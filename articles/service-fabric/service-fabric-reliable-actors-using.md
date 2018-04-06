---
title: Mise en œuvre de fonctionnalités dans Azure Service Fabric Actors | Microsoft Docs
description: Décrit comment écrire votre propre service d’acteur qui met en œuvre les fonctionnalités au niveau du service de la même façon que vous le feriez lors de l’héritage d’un élément StatefulService.
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
ms.openlocfilehash: 60989825ecdefa853d0e2df99619e3cb350cb6bc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Mise en œuvre de fonctionnalités de niveau de service dans votre service d’acteur
Comme décrit dans les [couches de service](service-fabric-reliable-actors-platform.md#service-layering), le service d’acteur lui-même est un service fiable.  Vous pouvez écrire votre propre service qui dérive de `ActorService` et implémenter des fonctionnalités au niveau du service de la même façon que si vous héritiez d’un élément StatefulService, par exemple :

- Sauvegarde et restauration du service.
- Fonctionnalité partagée par tous les acteurs. Par exemple, un disjoncteur.
- Appels de procédure à distance sur le service d’acteur proprement dit et sur chaque acteur.

## <a name="using-the-actor-service"></a>Utilisation du service d’acteur
Les instances d’acteur ont accès au service d’acteur dans lequel elles s’exécutent. Via le service d’acteur, les instances d’acteur peuvent obtenir par programmation le contexte de service. Le contexte de service comprend l’ID de partition, le nom du service, le nom de l’application et d’autres informations spécifiques de la plateforme Service Fabric :

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Comme tous les services Reliable Services, le service d’acteur doit être inscrit avec un type de service dans le runtime de Service Fabric. Pour que le service d’acteur exécute vos instances d’acteur, votre type d’acteur doit également être inscrit auprès du service d’acteur. La méthode d’inscription `ActorRuntime` effectue ce travail pour les acteurs. Dans le cas le plus simple, vous pouvez simplement enregistrer votre type d’acteur. Le service d’acteur avec les paramètres par défaut est alors utilisé de façon implicite :

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Vous pouvez également utiliser une expression lambda fournie par la méthode d’inscription pour construire vous-même le service d’acteur. Vous pouvez alors configurer le service d’acteur et construire explicitement vos instances d’acteur, pour y injecter des dépendances à votre acteur via son constructeur :

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Méthodes du service d’acteur
Le service d’acteur implémente `IActorService` (C#) `ActorService` (Java), qui implémente à son tour `IService` (C#) ou `Service` (Java). Il s’agit de l’interface qu’utilise la communication à distance de Reliable Services, qui autorise des appels de procédure distante sur les méthodes de service. Elle contient des méthodes de niveau de service qui peuvent être appelées à distance via la communication à distance des services et vous permet [d’énumérer](service-fabric-reliable-actors-enumerate.md) et de [supprimer](service-fabric-reliable-actors-delete-actors.md) des acteurs.


## <a name="custom-actor-service"></a>Service d’acteur personnalisé
En utilisant la fonction lambda de l’inscription d’acteur, vous pouvez enregistrer votre propre service d’acteur personnalisé qui dérive de `ActorService` (c#) et `FabricActorService` (Java). Dans ce service d’acteur personnalisé, vous pouvez implémenter vos propres fonctionnalités de niveau de service en écrivant une classe de service qui hérite de `ActorService` (c#) ou `FabricActorService` (Java). Un service d’acteur personnalisé hérite de toutes les fonctionnalités de runtime d’acteur de `ActorService` (C#) ou `FabricActorService` (Java). Vous pouvez l’utiliser pour implémenter vos propres méthodes de service.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implementing-actor-backup-and-restore"></a>Implémentation d’une sauvegarde et restauration d’acteur
Un service d’acteur personnalisé peut exposer une méthode pour sauvegarder des données d’acteur en tirant parti de l’écouteur de communication à distance déjà présent dans `ActorService`.  Pour obtenir un exemple, consultez [Sauvegarder et restaurer des acteurs](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2-stack"></a>Acteur utilisant la pile Remoting V2
Avec le package nuget 2.8, les utilisateurs peuvent désormais utiliser la pile Remoting V2, qui est plus performante et offre des fonctionnalités comme la sérialisation personnalisée. Remoting V2 n’est pas rétrocompatible avec la pile Remoting existante (nous l’appelons désormais pile V1 Remoting).

Les modifications suivantes sont requises pour utiliser la pile Remoting V2.
 1. Ajoutez l’attribut d’assembly suivant sur les interfaces Actor.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Créez et mettez à jour des projets ActorService et Actor Client pour démarrer à l’aide de la pile V2.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Mise à niveau du service d’acteur vers la pile Remoting V2 sans impact sur la disponibilité des services.
Cette modification sera une mise à niveau en 2 étapes. Suivez les étapes dans l’ordre indiqué.

1.  Ajoutez l’attribut d’assembly suivant sur les interfaces Actor. Cet attribut démarre deux écouteurs pour ActorService, V1 (existant) et V2 Listener. Mettez à niveau ActorService avec cette modification.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. Mettez à niveau ActorClients après avoir effectué la mise à niveau ci-dessus.
Cette étape permet de s’assurer de qu’Actor Proxy utilise la pile Remoting V2.

3. Cette étape est facultative. Modifiez l’attribut ci-dessus pour supprimer V1 Listener.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
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
