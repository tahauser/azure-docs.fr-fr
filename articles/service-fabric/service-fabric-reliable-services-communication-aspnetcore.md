---
title: "Communication de service avec ASP.NET Core | Microsoft Docs"
description: "Découvrez comment utiliser ASP.NET Core dans Reliable Services avec et sans état."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 4f5bc49bf58773a1510b552ce6fc20aa61076348
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>ASP.NET Core dans le modèle Reliable Services de Service Fabric

ASP.NET Core est une nouvelle infrastructure open source et multiplateforme destinée à générer des applications modernes connectées à Internet basées sur le cloud, comme des applications web, des applications IoT et des serveurs principaux mobiles. 

Cet article constitue un guide détaillé pour héberger des services ASP.NET Core dans le modèle Reliable Services de Service Fabric à l’aide de l’ensemble **Microsoft.ServiceFabric.AspNetCore.*** de packages NuGet.

Pour obtenir un didacticiel de présentation d’ASP.NET Core dans Service Fabric et des instructions de configuration de votre environnement de développement, consultez [Créer une application .NET](service-fabric-tutorial-create-dotnet-app.md).

Pour lire la suite de cet article, vous devez déjà maîtriser ASP.NET Core. Si ce n’est pas le cas, nous vous conseillons de lire les [notions de base d’ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core dans l’environnement Service Fabric

Si les applications ASP.NET Core peuvent s’exécuter sur .NET Core ou sur l’infrastructure .NET Framework complète, pour le moment, les services Service Fabric ne peuvent s’exécuter que sur l’infrastructure .NET Framework complète. Cela signifie que lorsque vous générez un service Service Fabric ASP.NET Core, vous devez toujours cibler l’infrastructure .NET Framework complète.

L’infrastructure ASP.NET Core peut être utilisée de deux manières dans Service Fabric :
 - **Hébergée en tant qu’exécutable invité**. Cette méthode est principalement utilisée pour exécuter des applications ASP.NET Core existantes sur Service Fabric sans modification de code.
 - **Exécutée dans un service Reliable Service**. Cette méthode permet des services ASP.NET Core avec état ainsi qu’une meilleure intégration au runtime Service Fabric.

La suite de cet article explique comment utiliser ASP.NET Core dans un service Reliable Service à l’aide des composants d’intégration ASP.NET Core fournis avec le Kit de développement logiciel (SDK) Service Fabric. 

## <a name="service-fabric-service-hosting"></a>Hébergement du service Service Fabric

Dans Service Fabric, une ou plusieurs instances et/ou un ou plusieurs réplicas de votre service s’exécutent dans un *processus hôte de service*, un fichier exécutable qui exécute le code de votre service. En tant que créateur du service, vous possédez le processus hôte de service qui est activé et surveillé par Service Fabric à votre place.

Traditionnellement, ASP.NET (jusqu’à MVC 5) est étroitement lié à IIS via System.Web.dll. ASP.NET Core fournit une séparation entre le serveur web et votre application web. Ce faisant, les applications web peuvent être déplacées entre différents serveurs web, et les serveurs web peuvent être *auto-hébergés*, ce qui signifie que vous pouvez démarrer un serveur web dans votre propre processus, par opposition à un processus appartenant à un logiciel serveur web dédié, par exemple IIS. 

Pour combiner un service Service Fabric et ASP.NET en tant qu’exécutable invité ou dans un service Reliable Service, vous devez être en mesure de démarrer ASP.NET dans votre processus hôte de service. L’auto-hébergement d’ASP.NET Core vous le permet.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hébergement d’ASP.NET Core dans un service Reliable Service
En règle générale, les applications ASP.NET Core auto-hébergées créent une méthode WebHost dans le point d’entrée d’une application, par exemple la méthode `static void Main()` dans `Program.cs`. Dans ce cas, le cycle de vie de la méthode WebHost est lié au cycle de vie du processus.

![Hébergement d’ASP.NET Core dans un processus][0]

Néanmoins, le point d’entrée de l’application n’est pas l’emplacement approprié pour créer une méthode WebHost dans un service Reliable Service, car ce point d’entrée est utilisé uniquement pour inscrire un type de service auprès du runtime Service Fabric afin de pouvoir créer des instances de ce type de service. La méthode WebHost doit être créée dans un service Reliable Service. Dans le processus hôte de service, les instances de service et/ou les réplicas peuvent connaître plusieurs cycles de vie. 

Une instance du service Reliable Service est représentée par votre classe de service dérivant de `StatelessService` ou `StatefulService`. La pile de communication d’un service est contenue dans une implémentation `ICommunicationListener` dans votre classe de service. Les packages NuGet `Microsoft.ServiceFabric.Services.AspNetCore.*` contiennent les implémentations de `ICommunicationListener`, qui démarrent et gèrent le WebHost ASP.NET Core pour Kestrel ou HttpSys dans un service Reliable Services.

![Hébergement d’ASP.NET Core dans un service Reliable Service][1]

## <a name="aspnet-core-icommunicationlisteners"></a>Écouteurs ICommunicationListeners d’ASP.NET Core
Les modèles d’utilisation des implémentations `ICommunicationListener` pour Kestrel et HttpSys dans les packages NuGet `Microsoft.ServiceFabric.Services.AspNetCore.*` sont similaires, mais ces implémentations effectuent des actions légèrement différentes propres à chaque serveur web. 

Les deux écouteurs de communications fournissent un constructeur qui accepte les arguments suivants :
 - **`ServiceContext serviceContext`** : objet `ServiceContext` qui contient des informations sur le service en cours d’exécution.
 - **`string endpointName`** : nom d’une configuration `Endpoint` dans le fichier ServiceManifest.xml. C’est essentiellement là que les deux écouteurs de communication diffèrent : HttpSys **nécessite** une configuration `Endpoint`, contrairement à Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`** : expression lambda que vous implémentez dans laquelle vous créez et retournez un `IWebHost`. Vous pouvez ainsi configurer `IWebHost` comme vous le feriez normalement dans une application ASP.NET Core. L’expression lambda fournit une URL, qui est générée pour vous en fonction des options d’intégration de Service Fabric que vous utilisez et de la configuration `Endpoint` que vous fournissez. Cette URL peut ensuite être modifiée ou utilisée telle quelle pour démarrer le serveur web.

## <a name="service-fabric-integration-middleware"></a>Intergiciel (middleware) d’intégration à Service Fabric
Le paquet NuGet `Microsoft.ServiceFabric.Services.AspNetCore` inclut la méthode d’extension `UseServiceFabricIntegration` sur `IWebHostBuilder` qui ajoute l’intergiciel (middleware) prenant en charge Service Fabric. Cet intergiciel (middleware) configure l’élément `ICommunicationListener` Kestrel ou HttpSys pour inscrire une URL de service unique auprès de Service Fabric Naming Service, puis valide les requêtes des clients pour s’assurer qu’ils se connectent au service approprié. Cela est nécessaire dans un environnement hôte partagé comme Service Fabric, où plusieurs applications web peuvent s’exécuter sur une même machine physique ou virtuelle, mais n’utilisent pas de noms d’hôte uniques, pour empêcher les clients de se connecter par erreur à un service incorrect. Ce scénario est décrit plus en détail dans la section suivante.

### <a name="a-case-of-mistaken-identity"></a>Cas d’erreur d’identité
Quel que soit le protocole utilisé, les réplicas de service écoutent sur une combinaison IP:port unique. Lorsqu’un réplica de service a commencé à écouter sur un point de terminaison IP:port, il indique l’adresse de ce point de terminaison à Service Fabric Naming Service où elle peut être détectée par des clients ou d’autres services. Si des services utilisent des ports d’application affectés de manière dynamique, un réplica de service peut utiliser par hasard le même point de terminaison IP:port d’un autre service qui existait précédemment sur la même machine physique ou virtuelle. Cela peut provoquer la connexion d’un client par erreur à un service incorrect. Cela peut se produire si la séquence d’événements suivante se produit :

 1. Le service A écoute sur 10.0.0.1:30000 via HTTP. 
 2. Le client résout le service A et obtient l’adresse 10.0.0.1:30000.
 3. Le service A passe à un autre nœud.
 4. Le service B est placé sur 10.0.0.1 et utilise par hasard le même port 30000.
 5. Le client essaie de se connecter au service A avec l’adresse mise en cache 10.0.0.1:30000.
 6. Le client est maintenant connecté au service B, sans détecter qu’il est connecté à un service incorrect.

Cela peut provoquer des bogues de façon aléatoire qui peuvent être difficiles à diagnostiquer. 

### <a name="using-unique-service-urls"></a>Utilisation d’URL de service uniques
Pour éviter ce problème, les services peuvent publier un point de terminaison dans Naming Service avec un identificateur unique, puis valider cet identificateur lors des demandes des clients. Il s’agit d’une action de coopération entre les services dans un environnement approuvé de client non hostile. Cette action ne permet pas d’authentification de service sécurisée dans un environnement de client hostile.

Dans un environnement approuvé, l’intergiciel (middleware) qui est ajouté par la méthode `UseServiceFabricIntegration` ajoute automatiquement un identificateur unique à l’adresse qui est publiée dans Naming Service et valide cet identificateur à chaque demande. Si l’identificateur ne correspond pas, l’intergiciel (middleware) retourne immédiatement une réponse HTTP 410 Supprimé.

Les services qui utilisent un port attribué dynamiquement doivent avoir recours à cet intergiciel (middleware).

Les services qui utilisent un port unique fixe ne connaissent pas ce problème dans un environnement coopératif. Un port de ce type est généralement utilisé pour les services externes qui nécessitent un port connu pour que les applications clientes s’y connectent. Par exemple, la plupart des applications web accessibles sur Internet utilisent les ports 80 ou 443 pour les connexions de navigateur web. Dans ce cas, l’identificateur unique ne doit pas être activé.

Le diagramme suivant illustre le flux de demande avec l’intergiciel (middleware) activé :

![Intégration d’ASP.NET Core à Service Fabric][2]

Les implémentations `ICommunicationListener` de Kestrel et HttpSys utilisent ce mécanisme de la même façon. Même si HttpSys peut différencier en interne les requêtes d’après leur chemin d’URL unique à l’aide de la fonctionnalité de partage de port *http.sys* sous-jacente, cette fonctionnalité *n’est pas* utilisée par l’implémentation `ICommunicationListener` de HttpSys, car cela entraîne la survenue des erreurs HTTP 503 et HTTP 404 dans le scénario décrit précédemment. Il est alors très difficile pour les clients de déterminer l’objet de l’erreur, car les codes HTTP 503 et HTTP 404 sont couramment utilisés pour indiquer d’autres erreurs. Par conséquent, les implémentations `ICommunicationListener` de Kestrel et HttpSys sont normalisées dans l’intergiciel (middleware) fourni par la méthode d’extension `UseServiceFabricIntegration` afin que les clients n’aient à effectuer qu’une action de nouvelle résolution de point de terminaison de service sur les réponses HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HttpSys dans Reliable Services
Vous pouvez utiliser HttpSys dans un service Reliable Services en important le package NuGet **Microsoft.ServiceFabric.AspNetCore.HttpSys**. Ce package contient `HttpSysCommunicationListener`, une implémentation de `ICommunicationListener`, qui vous permet de créer un WebHost ASP.NET Core dans un service Reliable Services utilisant HttpSys comme serveur web.

HttpSys est basé sur [l’API du serveur HTTP Windows](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx). Elle utilise le pilote du noyau *http.sys* utilisé par IIS pour traiter les requêtes HTTP et les acheminer vers les processus exécutant les applications web. De cette façon, plusieurs processus d’une même machine physique ou virtuelle peuvent héberger des applications web sur un même port, dont l’ambiguïté est levée par un chemin d’accès d’URL ou un nom d’hôte unique. Ces fonctionnalités sont utiles dans Service Fabric pour héberger plusieurs sites web dans un même cluster.

Le diagramme suivant illustre l’utilisation du pilote de noyau *http.sys* par HttpSys sur Windows pour le partage de port :

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HttpSys dans un service sans état
Pour utiliser `HttpSys` dans un service sans état, remplacez la méthode `CreateServiceInstanceListeners` et retournez une instance `HttpSysCommunicationListener` :

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HttpSys dans un service avec état

Pour le moment, `HttpSysCommunicationListener` n’est pas conçu pour être utilisé dans des services avec état en raison de complications liées à la fonctionnalité de partage de port *http.sys* sous-jacente. Pour plus d’informations, consultez la section suivante sur l’allocation de ports dynamiques avec HttpSys. Pour les services avec état, il est recommandé d’utiliser Kestrel comme serveur web.

### <a name="endpoint-configuration"></a>Configuration du point de terminaison

Une configuration `Endpoint` est nécessaire pour les serveurs web qui utilisent l’API du serveur HTTP Windows, y compris HttpSys. Les serveurs web qui utilisent cette API doivent d’abord réserver leur URL avec *http.sys* (cela s’effectue normalement avec l’outil [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx)). Cette action requiert des privilèges élevés dont vos services sont dépourvus par défaut. Les options « http » ou « https » de la propriété `Protocol` de la configuration `Endpoint` dans le fichier *ServiceManifest.xml* sont utilisées plus particulièrement pour indiquer au runtime Service Fabric d’inscrire une URL avec *http.sys* pour votre compte en utilisant le préfixe d’URL à [*caractère générique fort*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx).

Par exemple, pour réserver `http://+:80` pour un service, il convient d’utiliser la configuration suivante dans le fichier ServiceManifest.xml :

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

Par ailleurs, le nom du point de terminaison doit être transmis au constructeur `HttpSysCommunicationListener` :

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Utiliser HttpSys avec un port statique
Pour utiliser un port statique avec HttpSys, indiquez le numéro de port dans la configuration `Endpoint` :

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Utiliser HttpSys avec un port dynamique
Pour utiliser un port affecté dynamiquement avec HttpSys, omettez la propriété `Port` dans la configuration `Endpoint` :

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Notez qu’un port dynamique alloué par une configuration `Endpoint` fournit un seul port *par processus hôte*. Le modèle d’hébergement Service Fabric actuel permet à plusieurs instances de service et/ou réplicas d’être hébergés dans le même processus, ce qui signifie que chacun d’entre eux partagera le même port s’il est alloué via la configuration `Endpoint`. Plusieurs instances de HttpSys peuvent partager un même port à l’aide de la fonctionnalité de partage de port *http.sys* sous-jacente, mais celle-ci n’est pas prise en charge par `HttpSysCommunicationListener` en raison des complications qu’elle induit pour les requêtes des clients. Pour une utilisation de port dynamique, il est recommandé d’utiliser Kestrel comme serveur web.

## <a name="kestrel-in-reliable-services"></a>Kestrel dans Reliable Services
Kestrel peut être utilisé dans un service Reliable Service en important le paquet NuGet **Microsoft.ServiceFabric.AspNetCore.Kestrel**. Ce paquet contient `KestrelCommunicationListener`, une implémentation de `ICommunicationListener`, qui vous permet de créer une méthode WebHost d’ASP.NET Core dans un service Reliable Service utilisant Kestrel en tant que serveur web.

Kestrel est un serveur web multiplateforme pour ASP.NET Core basé sur libuv, une bibliothèque d’E/S asynchrone multiplateforme. Contrairement à HttpSys, Kestrel n’utilise pas de gestionnaire de points de terminaison centralisé comme *http.sys*. Et contrairement à HttpSys, Kestrel ne prend pas en charge le partage de port entre plusieurs processus. Chaque instance de Kestrel doit utiliser un port unique.

![kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel dans un service sans état
Pour utiliser `Kestrel` dans un service sans état, remplacez la méthode `CreateServiceInstanceListeners` et retournez une instance `KestrelCommunicationListener` :

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel dans un service avec état
Pour utiliser `Kestrel` dans un service avec état, remplacez la méthode `CreateServiceReplicaListeners` et retournez une instance `KestrelCommunicationListener` :

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

Dans cet exemple, une instance singleton de `IReliableStateManager` est fournie au conteneur d’injection de dépendance WebHost. Cela n’est pas rigoureusement nécessaire, mais elle vous permet d’utiliser `IReliableStateManager` et Reliable Collections dans vos méthodes d’action de contrôleur MVC.

Notez qu’aucun nom de configuration `Endpoint` **n’est** fourni à `KestrelCommunicationListener` dans un service avec état. La section suivante contient une explication plus détaillée à ce sujet.

### <a name="endpoint-configuration"></a>Configuration du point de terminaison
Aucune configuration `Endpoint` n’est requise pour utiliser Kestrel. 

Kestrel est un serveur web autonome simple. Contrairement à HttpSys (ou HttpListener), il ne nécessite pas de configuration `Endpoint` dans le fichier *ServiceManifest.xml*, car il ne nécessite pas d’inscription d’URL avant le démarrage. 

#### <a name="use-kestrel-with-a-static-port"></a>Utiliser Kestrel avec un port statique
Un port statique peut être défini dans la configuration `Endpoint` du fichier ServiceManifest.xml pour une utilisation avec Kestrel. Même si cela n’est pas rigoureusement nécessaire, cela confère deux avantages potentiels :
 1. Si le port n’est pas compris dans la plage de ports d’application, il est ouvert via le pare-feu du système d’exploitation par Service Fabric.
 2. L’URL qui vous est fournie via `KestrelCommunicationListener` utilise ce port.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Si un `Endpoint` est configuré, son nom doit être transmis au constructeur `KestrelCommunicationListener` : 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Si aucune configuration `Endpoint` n’est utilisée, omettez le nom dans le constructeur `KestrelCommunicationListener`. Dans ce cas, un port dynamique est utilisé. Pour en savoir plus, consultez la section suivante.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Utiliser Kestrel avec un port dynamique
Kestrel ne peut pas utiliser l’affectation de port automatique de la configuration `Endpoint` indiquée dans le fichier ServiceManifest.xml, car l’affectation de port automatique d’une configuration `Endpoint` affecte un port unique par *processus hôte*, et un processus hôte unique peut contenir plusieurs instances de Kestrel. Comme Kestrel ne prend pas en charge le partage de port, cela ne fonctionne pas, car chaque instance de Kestrel doit être ouverte sur un port unique.

Pour utiliser l’affectation de port dynamique avec Kestrel, omettez simplement entièrement la configuration `Endpoint` indiquée dans le fichier ServiceManifest.xml, et ne transmettez pas le nom d’un point de terminaison au constructeur `KestrelCommunicationListener` :

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

Dans cette configuration, `KestrelCommunicationListener` sélectionne automatiquement un port inutilisé dans la plage de ports d’application.

## <a name="scenarios-and-configurations"></a>Scénarios et configurations
Cette section décrit les scénarios suivants, et fournit la combinaison recommandée de serveur web, configuration de port, options d’intégration de Service Fabric et divers paramètres pour obtenir un service fonctionnant correctement :
 - Service sans état d’ASP.NET Core exposé en externe
 - Service sans état d’ASP.NET Core interne uniquement
 - Service avec état d’ASP.NET Core interne uniquement

Un service **exposé en externe** expose un point de terminaison accessible depuis l’extérieur du cluster, généralement par le biais d’un équilibreur de charge.

Un service **interne uniquement** possède un point de terminaison qui est uniquement accessible à partir de l’intérieur du cluster.

> [!NOTE]
> En règle générale, les points de terminaison de service avec état ne doivent pas être exposés à Internet. Les clusters qui se trouvent derrière des équilibreurs de charge qui ne prennent pas en charge la résolution de service Service Fabric, par exemple Azure Load Balancer, ne peuvent pas exposer des services avec état, car l’équilibreur de charge n’est pas en mesure de localiser ni d’acheminer le trafic vers le réplica de service avec état approprié. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Services sans état d’ASP.NET Core exposés en externe
Kestrel est le serveur web recommandé pour les services frontend qui exposent des points de terminaison HTTP externes accessibles sur Internet. Sur Windows, HttpSys peut être utilisé pour fournir des fonctionnalités de partage de port. Vous pouvez ainsi héberger plusieurs services web sur un même ensemble de nœuds utilisant le même port et se distinguant par leur nom d’hôte ou leur chemin, sans devoir compter sur un proxy ou une passerelle frontend pour assurer le routage HTTP.
 
Lorsqu’il est exposé à Internet, un service sans état doit utiliser un point de terminaison connu et stable accessible via un équilibreur de charge. Il s’agit de l’URL que vous fournissez aux utilisateurs de votre application. La configuration suivante est recommandée :

|  |  | **Remarques** |
| --- | --- | --- |
| Serveur web | Kestrel | Kestrel est le serveur web par défaut, car il est pris en charge sur Windows et Linux. |
| Configuration du port | statique | Un port statique connu doit être défini dans la configuration `Endpoints` du fichier ServiceManifest.xml, par exemple 80 pour HTTP et 443 pour HTTPS. |
| ServiceFabricIntegrationOptions | Aucun | Il convient d’utiliser l’option `ServiceFabricIntegrationOptions.None` lors de la configuration de l’intergiciel (middleware) d’intégration à Service Fabric afin que le service n’essaie pas de valider les demandes entrantes pour un identificateur unique. Les utilisateurs externes de votre application ne connaissent pas les informations d’identification uniques utilisées par l’intergiciel (middleware). |
| Nombre d'instances | -1 | Dans les cas d’utilisation standard, le paramètre du nombre d’instances doit être défini sur « -1 » afin qu’une instance soit disponible sur tous les nœuds recevant le trafic à partir d’un équilibreur de charge. |

Si plusieurs services exposés en externe partagent le même ensemble de nœuds, HttpSys peut être utilisé avec un chemin URL unique, mais stable. Cela peut être accompli en modifiant l’URL fournie lors de la configuration d’IWebHost. Notez que cela s’applique uniquement à HttpSys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Service d’ASP.NET Core sans état interne uniquement
Les services sans état qui sont appelés uniquement à partir du cluster doivent utiliser des URL uniques et des ports affectés dynamiquement afin d’assurer une coopération entre plusieurs services. La configuration suivante est recommandée :

|  |  | **Remarques** |
| --- | --- | --- |
| Serveur web | Kestrel | Même si HttpSys peut être utilisé pour les services sans état internes, Kestrel est le serveur qui est recommandé pour permettre à plusieurs instances de service de partager un hôte.  |
| Configuration du port | affecté de manière dynamique | Plusieurs réplicas d’un service avec état peuvent partager un processus hôte ou un système d’exploitation hôte, et nécessitent donc des ports uniques. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Avec l’affectation de port dynamique, ce paramètre empêche le problème d’erreur d’identité décrit précédemment. |
| InstanceCount | any | Le paramètre du nombre d’instances peut être défini sur toute valeur nécessaire au bon fonctionnement du service. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Service d’ASP.NET Core avec état interne uniquement
Les services avec état qui sont appelés uniquement à partir du cluster doivent utiliser des ports affectés dynamiquement afin d’assurer une coopération entre plusieurs services. La configuration suivante est recommandée :

|  |  | **Remarques** |
| --- | --- | --- |
| Serveur web | Kestrel | Le `HttpSysCommunicationListener` n’est pas conçu pour être utilisé par les services avec état dans lesquels les réplicas partagent un processus hôte. |
| Configuration du port | affecté de manière dynamique | Plusieurs réplicas d’un service avec état peuvent partager un processus hôte ou un système d’exploitation hôte, et nécessitent donc des ports uniques. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | Avec l’affectation de port dynamique, ce paramètre empêche le problème d’erreur d’identité décrit précédemment. |

## <a name="next-steps"></a>Étapes suivantes
[Débogage de votre application Service Fabric à l’aide de Visual Studio](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
