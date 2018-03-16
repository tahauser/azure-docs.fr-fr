---
title: Reliable Actors dans Service Fabric | Microsoft Docs
description: "Décrit comment les Reliable Actors sont superposés en couches sur des Reliable Services et utilisent les fonctionnalités de la plateforme Service Fabric."
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
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: ee248cb656eeb54e259ff1adf45080a207b5a866
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Comment le service Reliable Actors utilise la plateforme Service Fabric
Cet article décrit le fonctionnement du service Reliable Actors sur la plateforme Azure Service Fabric. La solution Reliable Actors s’exécute dans une infrastructure hébergée dans une implémentation d’un service fiable avec état nommé *service d’acteur*. Le service d’acteur contient tous les composants nécessaires pour gérer le cycle de vie et la distribution des messages destinés à vos acteurs :

* Le runtime de l’acteur gère le cycle de vie et le nettoyage de la mémoire, et applique une accès monothread.
* Un écouteur de communication à distance du service d’acteur accepte les appels d’accès à distance adressés aux acteurs, et les transmet à un répartiteur qui les route vers l’instance d’acteur appropriée.
* Le fournisseur d’état de l’acteur encapsule des fournisseurs d’état (par exemple, le fournisseur d’état Collections fiables), et fournit un adaptateur pour la gestion de l’état de l’acteur.

Ces composants forment ensemble l’infrastructure d’acteur fiable.

## <a name="service-layering"></a>Couches de service
Étant donné que le service d’acteur est un service fiable, l’ensemble des concepts des Reliable Services relatifs au [modèle d’application](service-fabric-application-model.md), au cycle de vie, à [l’empaquetage](service-fabric-package-apps.md), au [déploiement](service-fabric-deploy-remove-applications.md), à la mise à niveau et à la mise à l’échelle s’appliquent de la même manière aux services d’acteur.

![Superposition de service d’acteur][1]

Le diagramme précédent montre la relation entre les infrastructures d’application de Service Fabric et le code utilisateur. Les éléments en bleu représentent l’infrastructure d’application de Reliable Services, ceux en orange l’infrastructure de Reliable Actors, et ceux en vert le code utilisateur.

Dans Reliable Services, votre service hérite de la classe `StatefulService`. Cette classe est dérivée de `StatefulServiceBase` (ou `StatelessService` pour les services sans état). Dans les Reliable Actors, vous utilisez le service d’acteur. Le service d’acteur est une implémentation différente de la classe `StatefulServiceBase` qui implémente le modèle d’acteur dans lequel vos acteurs s’exécutent. Le service d’acteur proprement dit est simplement une implémentation de `StatefulServiceBase`. Vous pouvez donc écrire votre propre service qui dérive de `ActorService` et implémenter des fonctionnalités au niveau du service de la même façon que si vous héritiez de `StatefulService`, par exemple :

* Sauvegarde et restauration du service.
* Fonctionnalité partagée par tous les acteurs. Par exemple, un disjoncteur.
* Appels de procédure à distance sur le service d’acteur proprement dit et sur chaque acteur.

### <a name="using-the-actor-service"></a>Utilisation du service d’acteur
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

### <a name="actor-service-methods"></a>Méthodes du service d’acteur
Le service d’acteur implémente `IActorService` (C#) `ActorService` (Java), qui implémente à son tour `IService` (C#) ou `Service` (Java). Il s’agit de l’interface qu’utilise la communication à distance de Reliable Services, qui autorise des appels de procédure distante sur les méthodes de service. Elle contient des méthodes de niveau de service qui peuvent être appelées à distance via la communication à distance des services.

#### <a name="enumerating-actors"></a>Énumération des acteurs
Le service d’acteur permet à un client d’énumérer des métadonnées sur les acteurs hébergés par le service. Le service d’acteur est un service avec état partitionné. L’énumération est donc effectuée par partition. Étant donné que chaque partition peut contenir de nombreux acteurs, l’énumération est renvoyée sous la forme d’un ensemble de résultats paginés. Les pages sont traitées en boucle jusqu’à ce qu’elles aient toutes été lues. L’exemple suivant montre comment créer une liste de tous les acteurs actifs dans une partition d’un service d’acteur :

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

#### <a name="deleting-actors"></a>Suppression d’acteurs
Le service d’acteur fournit également une fonction permettant de supprimer des acteurs :

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Pour plus d’informations sur la suppression des acteurs et de leur état, consultez la [documentation sur le cycle de vie des acteurs](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Service d’acteur personnalisé
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

#### <a name="implementing-actor-backup-and-restore"></a>Implémentation d’une sauvegarde et restauration d’acteur
 Dans l’exemple suivant, le service d’acteur personnalisé expose une méthode pour sauvegarder des données d’acteur en tirant parti de l’écouteur de communication à distance déjà présent dans `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```


Dans cet exemple, `IMyActorService` est un contrat de communication à distance qui implémente `IService` (C#) et `Service` (Java) et est ensuite implémenté par `MyActorService`. Suite à l’ajout de ce contrat de communication à distance, vous pouvez rendre les méthodes sur `IMyActorService` également disponibles pour un client en créant un proxy de communication à distance via `ActorServiceProxy` :

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

## <a name="application-model"></a>Modèle d'application
Les services d’acteur étant des Reliable Services, le modèle d’application est le même. Toutefois, les outils de génération d’infrastructure d’acteur produisent certains fichiers de modèle d’application pour vous.

### <a name="service-manifest"></a>Manifeste de service
Les outils de génération d’infrastructure d’acteur génèrent automatiquement le contenu du fichier ServiceManifest.xml de votre service d’acteur. Ce fichier inclut :

* Le type de service d’acteur. Le nom du type est généré en fonction du nom du projet d’acteur. En fonction de l’attribut de persistance sur votre acteur, l’indicateur HasPersistedState est également défini en conséquence.
* le package de code ;
* le package de configuration ;
* les ressources et points de terminaison.

### <a name="application-manifest"></a>Manifeste d’application
Les outils de génération d’infrastructure d’acteur créent automatiquement une définition de service par défaut pour votre service d’acteur. Les outils de génération définissent les propriétés de service par défaut :

* Le nombre de jeux de réplicas est déterminé par l’attribut de persistance sur votre acteur. À chaque modification de l’attribut de persistance sur votre acteur, le nombre de jeux de réplicas dans la définition de service par défaut est réinitialisé en conséquence.
* La plage et le schéma de partition ont une valeur Int64 uniforme avec la plage complète de clés Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Concepts de partition Service Fabric pour les acteurs
Les services d’acteur sont des services partitionnés avec état. Chaque partition d’un service d’acteur contient un ensemble d’acteurs. Les partitions de service sont automatiquement distribuées sur plusieurs nœuds dans Service Fabric. Par conséquent, les instances d’acteur sont distribuées.

![Partitionnement et distribution d’acteur][5]

Vous pouvez créer des Reliable Services avec différents schémas de partition et plages de clés de partition. Le service d’acteur utilise le schéma de partitionnement Int64 avec la plage complète de clés Int64 pour mapper des acteurs à des partitions.

### <a name="actor-id"></a>ID d’acteur
Chaque acteur créé dans le service possède un ID unique associé, représenté par la classe `ActorId` . `ActorId` est une valeur d’ID opaque qui peut être utilisée pour une distribution uniforme des acteurs sur les partitions de service en générant des identifiants aléatoires :

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Chaque `ActorId` est haché en Int64. C’est pourquoi le service d’acteur doit utiliser un schéma de partitionnement Int64 avec la plage complète de clés Int64. Toutefois, vous pouvez utiliser des valeurs d’ID personnalisées pour un `ActorID`, dont des GUID / UUID, des chaînes et des valeurs Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Lorsque vous utilisez des chaînes et des GUID / UUID, les valeurs sont hachées en Int64. Toutefois, lorsque vous fournissez explicitement une valeur Int64 à un `ActorId`, la valeur Int64 mappe directement à une partition sans hachage supplémentaire. Vous pouvez utiliser cette technique pour contrôler la partition dans laquelle les acteurs sont placés.

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
