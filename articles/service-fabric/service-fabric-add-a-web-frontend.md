---
title: "Créer un serveur web frontal pour votre application Azure Service Fabric à l’aide d’ASP.NET Core | Microsoft Docs"
description: "Exposez votre application Service Fabric sur le web à l’aide d’un projet ASP.NET Core et de la communication interservice via Service Remoting."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: d4f78c63117e5c54eb855178c75d6c294957f2a1
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Créer un service web frontal pour votre application à l’aide d’ASP.NET Core
Par défaut, les services Azure Service Fabric ne fournissent pas une interface publique sur le web. Pour exposer les fonctionnalités de votre application pour les clients HTTP, vous devez créer un projet web qui agit comme point d'entrée et ensuite établir la communication avec vos services individuels.

Dans ce didacticiel, nous reprenons les choses là où nous les avions laissées dans [Création de votre première application dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) et ajoutons un service web ASP.NET Core devant le service de compteur avec état. Si vous ne l’avez pas encore fait, revenez d’abord à ce didacticiel et suivez-le.

## <a name="set-up-your-environment-for-aspnet-core"></a>Configurer votre environnement pour ASP.NET Core

Pour suivre ce didacticiel, vous avez besoin de Visual Studio 2017. N’importe quelle édition fera l’affaire. 

 - [Installer Visual Studio 2017](https://www.visualstudio.com/)

Pour développer des applications ASP.NET Core Service Fabric, les charges de travail suivantes doivent être installées :
 - **Développement Azure** (sous *Web &amp; Cloud*)
 - **Développement web et ASP.NET** (sous *Web &amp; Cloud*)
 - **Développement multiplateforme .NET Core** (sous *Autres jeux d’outils*)

>[!Note] 
>Les outils .NET Core pour Visual Studio 2015 ne sont plus mis à jour, mais si vous utilisez encore Visual Studio 2015, [.NET Core VS 2015 Tooling Preview 2](https://www.microsoft.com/net/download/core) doit être installé.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Ajouter un service ASP.NET Core à votre application
ASP.NET Core est une infrastructure légère de développement web inter-plateformes, que vous pouvez utiliser pour créer des API web et d’interfaces utilisateur web modernes. 

Ajoutons un projet d’API web ASP.NET à notre application existante.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur **Services** dans le projet d’application et choisissez **Ajouter > Nouveau Service Fabric**.
   
    ![Ajout d’un nouveau service à une application existante][vs-add-new-service]
2. Sur la page **Créer un service**, choisissez **ASP.NET Core** et donnez un nom au nouveau service.
   
    ![Choix du service web ASP.NET dans la boîte de dialogue Nouveau service][vs-new-service-dialog]

3. La page suivante fournit un ensemble de modèles de projets ASP.NET Core. Notez que vous disposez des mêmes options que si vous créez un projet ASP.NET Core en dehors d’une application Service Fabric, avec un peu de code supplémentaire pour enregistrer le service auprès du runtime Service Fabric. Pour ce didacticiel, sélectionnez **API web**. Toutefois, vous pouvez appliquer les mêmes concepts pour la création d'une application web complète.
   
    ![Choix du type de projet ASP.NET][vs-new-aspnet-project-dialog]
   
    Une fois votre projet d’API web créé, vous devriez avoir deux services dans votre application. Lorsque vous continuez à générer votre application, vous pouvez ajouter davantage de services exactement de la même façon. Chacun peut être mis à niveau et faire l'objet d'un contrôle de version indépendamment.

## <a name="run-the-application"></a>Exécution de l'application
Pour obtenir une idée de ce que nous avons fait, nous allons déployer la nouvelle application et observer le comportement par défaut fourni par le modèle d’API web ASP.NET Core.

1. Appuyez sur F5 dans Visual Studio pour déboguer l'application.
2. À la fin du déploiement, Visual Studio lance un navigateur à la racine du service API web ASP.NET. Le modèle d’API web ASP.NET Core ne fournit pas de comportement par défaut pour la racine, donc vous devriez voir une erreur 404 dans le navigateur.
3. Ajoutez `/api/values` à l'emplacement dans le navigateur. Cette action appelle la méthode `Get` sur ValuesController dans le modèle d’API web. Elle retourne la réponse par défaut fournie par le modèle, un tableau JSON contenant deux chaînes :
   
    ![Valeurs par défaut retournées par le modèle d’API web ASP.NET Core][browser-aspnet-template-values]
   
    À la fin du didacticiel, cette page indiquera la valeur de compteur la plus récente de notre service avec état au lieu des chaînes par défaut.

## <a name="connect-the-services"></a>Connecter les services
Service Fabric fournit une flexibilité complète sur votre façon de communiquer avec Reliable Services. Dans une seule application, vous pouvez avoir des services accessibles via TCP, d'autres services accessibles via une API REST HTTP et encore d'autres services qui sont accessibles via des sockets web. Pour obtenir des informations sur les options disponibles et leurs avantages/inconvénients respectifs, consultez [Communication avec les services](service-fabric-connect-and-communicate-with-services.md). Dans ce didacticiel, nous utilisons [Service à distance Service Fabric](service-fabric-reliable-services-communication-remoting.md), fourni dans le Kit de développement logiciel.

Dans l’approche Service à distance (modélisée sur les appels de procédure distante ou RPC), vous définissez une interface qui agit comme contrat public pour le service. Ensuite, vous utilisez cette interface pour générer une classe proxy pour l'interaction avec le service.

### <a name="create-the-remoting-interface"></a>Créer l’interface de communication à distance
Commençons par la création de l’interface en tant que contrat entre le service avec état et les autres services, dans le cas présent le projet web ASP.NET Core. Cette interface doit être partagée par tous les services qui l’utilisent pour effectuer des appels RPC. Nous allons donc la créer dans son propre projet de bibliothèque de classes.

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre solution et choisissez **Ajoutez** > **Nouveau projet**.

2. Choisissez l’entrée **Visual C#** dans le volet de navigation gauche, puis sélectionnez le modèle **Bibliothèque de classes**. Assurez-vous que la version du .NET Framework est définie sur **4.5.2**.
   
    ![Création d’un projet d’interface de votre service avec état][vs-add-class-library-project]

3. Installez le package NuGet **Microsoft.ServiceFabric.Services.Remoting**. Recherchez **Microsoft.ServiceFabric.Services.Remoting** dans le gestionnaire de packages NuGet et installez-le pour tous les projets de la solution qui utilisent le service à distance, y compris :
   - Le projet de bibliothèque de classes qui contient l’interface de service
   - Le projet de service avec état
   - Le projet de service web ASP.NET Core
   
    ![Obtention du package NuGet de services][vs-services-nuget-package]

4. Dans la bibliothèque de classes, créez une interface avec une méthode unique, `GetCountAsync`, et étendez l'interface depuis `Microsoft.ServiceFabric.Services.Remoting.IService`. L’interface de communication à distance doit dériver de cette interface pour indiquer qu’il s’agit d’une interface de service à distance.
   
    ```csharp
    using Microsoft.ServiceFabric.Services.Remoting;
    using System.Threading.Tasks;
        
    ...

    namespace MyStatefulService.Interface
    {
        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```

### <a name="implement-the-interface-in-your-stateful-service"></a>Mettre en œuvre l’interface dans votre service avec état
Maintenant que nous avons défini l'interface, nous devons la mettre en œuvre dans notre service avec état.

1. Dans votre service avec état, ajoutez une référence au projet de la bibliothèque de classes contenant l'interface.
   
    ![Ajout d’une référence au projet de bibliothèque de classes dans le service avec état][vs-add-class-library-reference]
2. Recherchez la classe qui hérite de `StatefulService`, par exemple `MyStatefulService`, et étendez-la pour implémenter l’interface `ICounter`.
   
    ```csharp
    using MyStatefulService.Interface;
   
    ...
   
    public class MyStatefulService : StatefulService, ICounter
    {        
         ...
    }
    ```
3. À présent, implémentez la méthode unique définie par l’interface `ICounter`, `GetCountAsync`.
   
    ```csharp
    public async Task<long> GetCountAsync()
    {
        var myDictionary = 
            await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
   
        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```

### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Exposer le service avec état à l’aide d’un écouteur de communication à distance
Une fois l’interface `ICounter` implémentée, l’étape finale consiste à ouvrir le canal de communication du service à distance. Pour les services avec état, Service Fabric fournit une méthode remplaçable appelée `CreateServiceReplicaListeners`. Avec cette méthode, vous pouvez spécifier un ou plusieurs écouteurs de communication, en fonction du type de communication que vous voulez activer pour votre service.

> [!NOTE]
> La méthode équivalente pour ouvrir un canal de communication sur des services sans état est appelée `CreateServiceInstanceListeners`.

Dans ce cas, nous remplaçons la méthode `CreateServiceReplicaListeners` existante par une instance de `ServiceRemotingListener`, ce qui crée un point de terminaison RPC pouvant être appelé à partir de clients via `ServiceProxy`.  

La méthode d’extension `CreateServiceRemotingListener` sur l’interface `IService` vous permet de créer facilement un `ServiceRemotingListener` avec tous les paramètres par défaut. Pour utiliser cette méthode d’extension, assurez-vous d’avoir importé l’espace de noms `Microsoft.ServiceFabric.Services.Remoting.Runtime`. 

```csharp
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Utiliser la classe ServiceProxy pour interagir avec le service
Notre service avec état est maintenant prêt à recevoir le trafic provenant d'autres services via RPC. Il ne reste donc qu'à ajouter le code pour communiquer avec celui-ci à partir du service web ASP.NET.

1. Dans votre projet ASP.NET, ajoutez une référence à la bibliothèque de classes contenant l’interface `ICounter` .

2. Précédemment, vous avez ajouté le package NuGet **Microsoft.ServiceFabric.Services.Remoting** au projet ASP.NET. Ce package fournit la classe `ServiceProxy`, que vous utilisez pour effectuer des appels RPC au service avec état. Vérifiez que ce package NuGet est installé dans le projet de service web ASP.NET Core.

4. Dans le dossier **Contrôleurs**, ouvrez la classe `ValuesController`. Notez que la méthode `Get` renvoie actuellement uniquement un tableau de chaînes codées en dur avec « valeur1 » et « valeur2 », ce qui correspond à ce que nous avons vu précédemment dans le navigateur. Remplacez par le code suivant :
   
    ```csharp
    using MyStatefulService.Interface;
    using Microsoft.ServiceFabric.Services.Client;
    using Microsoft.ServiceFabric.Services.Remoting.Client;
   
    ...

    [HttpGet]
    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));
   
        long count = await counter.GetCountAsync();
   
        return new string[] { count.ToString() };
    }
    ```
   
    La première ligne de code est la clé. Pour créer le proxy ICounter sur le service avec état, vous devez fournir deux informations : un ID de partition et le nom du service.
   
    Vous pouvez utiliser le partitionnement pour mettre à l'échelle des services avec état en les fractionnant dans différents groupes en fonction de la clé définie, comme l'ID client ou le code postal. Dans notre exemple d'application, le service avec état ne dispose que d'une seule partition, donc la clé importe peu. N'importe quelle clé fournie aboutira à la même partition. Pour en savoir plus sur le partitionnement des services, consultez [Comment partitionner les services fiables (Reliable Services) de Service Fabric](service-fabric-concepts-partitioning.md).
   
    Le nom du service est un URI de la forme : fabric:/&lt;nom_application&gt;/&lt;nom_service&gt;.
   
    Avec ces deux éléments d'information, Service Fabric peut identifier de façon unique l'ordinateur auquel les demandes doivent être envoyées. La classe `ServiceProxy` traite également sans problème le cas dans lequel la machine qui héberge la partition de service avec état échoue et une autre machine est promue pour prendre sa place. Cette abstraction facilite de manière significative l’écriture du code client pour la gestion avec d’autres services.
   
    Une fois que nous avons le proxy, nous appelons simplement la méthode `GetCountAsync` et retournons son résultat.

5. Appuyez sur F5 de nouveau pour exécuter l'application modifiée. Comme précédemment, Visual Studio lance automatiquement le navigateur à la racine du projet web. Ajoutez le chemin « api/values » et vous devriez voir la valeur actuelle du compteur retournée.
   
    ![La valeur du compteur avec état affichée dans le navigateur][browser-aspnet-counter-value]
   
    Actualisez le navigateur régulièrement afin de vérifier la mise à jour de la valeur du compteur.

## <a name="connecting-to-a-reliable-actor-service"></a>Connexion à un service Reliable Actor
Ce didacticiel s'est concentré sur l'ajout d'un serveur web frontal communiquant avec un service avec état. Toutefois, vous pouvez suivre un modèle très similaire pour communiquer avec les acteurs. Lorsque vous créez un projet Reliable Actor, Visual Studio génère automatiquement un projet d’interface pour vous. Vous pouvez utiliser cette interface pour générer un proxy d’acteur intervenant dans le projet web pour communiquer avec l’acteur. Le canal de communication est fourni automatiquement. Vous n’avez donc aucune opération à faire, telle que l’établissement d’un `ServiceRemotingListener` comme vous l’avez fait pour le service avec état dans ce didacticiel.

## <a name="how-web-services-work-on-your-local-cluster"></a>Fonctionnement des services web sur votre cluster local
En général, vous pouvez déployer exactement la même application Service Fabric sur un cluster de plusieurs ordinateurs déployé sur votre cluster local et être assuré qu'elle fonctionne comme prévu. Cela est dû au fait que votre cluster local est simplement une configuration de cinq nœuds réduite à un seul ordinateur.

En ce qui concerne les services web, toutefois, il existe une nuance clé. Lorsque votre cluster se trouve derrière un équilibreur de charge, comme c’est le cas dans Azure, vous devez vous assurer que vos services web sont déployés sur chaque ordinateur, car l’équilibreur de charge alterne simplement le trafic entre les ordinateurs. Vous pouvez le faire en définissant `InstanceCount` pour le service sur la valeur spécifique « -1 ».

En revanche, lorsque vous exécutez un service web localement, vous devez vous assurer qu'une seule instance du service est en cours d'exécution. Sinon, vous rencontrez des conflits à cause de plusieurs processus qui sont à l'écoute sur le même chemin d'accès et le même port. Par conséquent, le nombre d'instances de service web doit être défini sur « 1 » pour les déploiements locaux.

Pour apprendre à configurer des valeurs différentes pour un environnement différent, consultez [Gestion des paramètres d'application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>étapes suivantes
Maintenant que vous avez configuré un serveur web frontal pour votre application avec ASP.NET Core, consultez [ASP.NET Core dans le modèle Reliable Services de Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) pour comprendre comment ASP.NET Core fonctionne avec Service Fabric.

Ensuite, [accédez à des informations supplémentaires sur la communication avec les services](service-fabric-connect-and-communicate-with-services.md) en général pour obtenir une image complète de la façon dont les communications avec les services fonctionnent dans Service Fabric.

Une fois que vous avez une bonne compréhension du fonctionnement des communications avec les services, [créez un cluster dans Azure et déployez votre application sur le cloud](service-fabric-cluster-creation-via-portal.md).

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
