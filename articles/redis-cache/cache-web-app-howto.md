---
title: "Création d’une application web avec le Cache Redis | Microsoft Docs"
description: "Découvrez comment créer une application web avec le Cache Redis"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 454e23d7-a99b-4e6e-8dd7-156451d2da7c
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: hero-article
ms.date: 05/09/2017
ms.author: wesmc
ms.openlocfilehash: 98750c4f8d2449fb4fdf68b03a00d846e636a93a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-create-a-web-app-with-redis-cache"></a>Création d’une application web avec le Cache Redis
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Ce didacticiel montre comment créer et déployer une application web ASP.NET dans une application web dans Azure App Service en utilisant Visual Studio 2017. L’exemple d’application affiche une liste des statistiques d’équipe d’une base de données et montre les différentes façons d’utiliser le Cache Redis Azure pour stocker et récupérer des données à partir du cache. Lorsque vous avez terminé le didacticiel, vous disposerez d’une application web, optimisée avec le Cache Redis Azure et hébergée dans Azure, effectuant des opérations de lecture et écriture sur une base de données.

Vous allez découvrir :

* comment créer une application web ASP.NET MVC 5 dans Visual Studio ;
* comment accéder aux données à partir d’une base de données à l’aide d’Entity Framework ;
* comment améliorer le débit des données et réduire la charge de la base de données en stockant et en récupérant des données à l’aide du Cache Redis Azure ;
* comment utiliser un ensemble trié Redis pour récupérer les cinq meilleures équipes ;
* comment approvisionner des ressources Azure pour l’application à l’aide d’un modèle Resource Manager ;
* comment publier l’application sur Azure avec Visual Studio.

## <a name="prerequisites"></a>configuration requise
Pour suivre ce didacticiel, vous devez disposer des éléments suivants :

* [Compte Azure](#azure-account)
* [Visual Studio 2017 avec le Kit de développement logiciel (SDK) Azure pour .NET](#visual-studio-2017-with-the-azure-sdk-for-net)

### <a name="azure-account"></a>Compte Azure
Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Vous pouvez :

* [Ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). Vous obtenez des crédits que vous pouvez utiliser pour essayer des services Azure payants. Même après que les crédits sont épuisés, vous pouvez conserver le compte et utiliser les services et fonctionnalités Azure gratuits.
* [Activez les avantages d’abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

### <a name="visual-studio-2017-with-the-azure-sdk-for-net"></a>Visual Studio 2017 avec le Kit de développement logiciel (SDK) Azure pour .NET
Ce didacticiel a été rédigé pour Visual Studio 2017 avec le [Kit de développement logiciel (SDK) Azure pour .NET](https://www.visualstudio.com/news/releasenotes/vs2017-relnotes#azuretools). Le Kit de développement logiciel (SDK) version 2.9.5 est inclus dans le programme d’installation de Visual Studio.

Si vous utilisez Visual Studio 2015, vous pouvez suivre le didacticiel avec le [Kit de développement logiciel (SDK) Azure pour .NET](../dotnet-sdk.md) version 2.8.2 ou ultérieure. [Cliquez ici pour télécharger la dernière version du Kit de développement logiciel (SDK) Azure pour Visual Studio 2015](http://go.microsoft.com/fwlink/?linkid=518003). Visual Studio est automatiquement installé avec le SDK si vous n’en disposez pas déjà. Certains écrans diffèrent des illustrations présentées dans ce didacticiel.

Si vous utilisez Visual Studio 2013, vous pouvez [télécharger la dernière version du Kit de développement logiciel (SDK) Azure pour Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkID=324322). Certains écrans diffèrent des illustrations présentées dans ce didacticiel.

## <a name="create-the-visual-studio-project"></a>Créer le projet Visual Studio
1. Ouvrez Visual Studio et cliquez sur **Fichier**, **Nouveau**, **Projet**.
2. Développez le nœud **Visual C#** dans la liste **Modèles**, sélectionnez **Cloud**, puis cliquez sur **Application web ASP.NET**. Vérifiez que l’option **.NET Framework 4.5.2** est sélectionnée.  Tapez **ContosoTeamStats** dans la zone de texte **Nom** et cliquez sur **OK**.
   
    ![Créer un projet][cache-create-project]
3. Sélectionnez le type de projet **MVC** . 

    Vérifiez que la valeur **Aucune authentification** est spécifiée dans les paramètres **Authentification**. Selon votre version de Visual Studio, la valeur par défaut peut être différente. Pour la modifier, cliquez sur **Modifier l’authentification** et sélectionnez **Aucune authentification**.

    Si vous poursuivez la procédure avec Visual Studio 2015, décochez la case **Héberger dans le cloud**. Vous [approvisionnez les ressources Azure](#provision-the-azure-resources) et [publiez l’application sur Azure](#publish-the-application-to-azure) au cours des étapes suivantes du didacticiel. Pour obtenir un exemple d’approvisionnement d’application web App Service à partir de Visual Studio en laissant la case à cocher **Héberger sur le cloud** activée, consultez [Prise en main de Web Apps dans Azure App Service à l’aide d’ASP.NET et Visual Studio](../app-service/app-service-web-get-started-dotnet.md).
   
    ![Sélectionner un modèle de projet][cache-select-template]
4. Cliquez sur **OK** pour créer le projet.

## <a name="create-the-aspnet-mvc-application"></a>Créer l’application ASP.NET MVC
Dans cette section du didacticiel, vous créez l’application de base qui lit et affiche les statistiques d’équipe à partir d’une base de données.

* [Ajouter le package NuGet Entity Framework](#add-the-entity-framework-nuget-package)
* [Ajouter le modèle](#add-the-model)
* [Ajouter le contrôleur](#add-the-controller)
* [Configurer les vues](#configure-the-views)

### <a name="add-the-entity-framework-nuget-package"></a>Ajouter le package NuGet Entity Framework

1. Dans Visual Studio, cliquez sur **Outils > Gestionnaire de package NuGet > Console du gestionnaire de package**.
2. Exécutez la commande suivante dans la fenêtre **Console du Gestionnaire de package** :
    
    ```
    Install-Package EntityFramework
    ```

Pour plus d’informations sur ce package, consultez la page NuGet [EntityFramework](https://www.nuget.org/packages/EntityFramework/).

### <a name="add-the-model"></a>Ajouter le modèle
1. Cliquez avec le bouton droit sur **Modèles** dans l’**Explorateur de solutions** et sélectionnez **Ajouter**, **Classe**. 
   
    ![Ajouter un modèle][cache-model-add-class]
2. Entrez le nom de classe `Team` et cliquez sur **Ajouter**.
   
    ![Ajouter une classe de modèle][cache-model-add-class-dialog]
3. Remplacez les instructions `using` au début du fichier `Team.cs` par les instructions `using` suivantes :

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```


1. Remplacez la définition de la classe `Team` par l’extrait de code suivant, qui contient une définition de classe `Team` mise à jour, ainsi que d’autres classes d’assistance Entity Framework. Pour plus d’informations sur l’approche Code First d’Entity Framework utilisée dans ce didacticiel, consultez [Code First pour une nouvelle base de données](https://msdn.microsoft.com/data/jj193542).

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }
    
        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();
    
            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }
    
    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }
    
        public DbSet<Team> Teams { get; set; }
    }
    
    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };
    
            Team.PlayGames(teams);
    
            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }
    
    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```


1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **web.config** pour l’ouvrir.
   
    ![Web.config][cache-web-config]
2. Ajoutez la section `connectionStrings` suivante dans la section `configuration`. Le nom de la chaîne de connexion doit correspondre au nom de la classe de contexte de base de données Entity Framework, qui est `TeamContext`.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    L’exemple suivant montre la nouvelle section `connectionStrings` suivant `configSections` à l’intérieur de la section `configuration` :

    ```xml
    <configuration>
      <configSections>
        <!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
      </configSections>
      <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
      </connectionStrings>
      ...
      ```

    > [!NOTE]
    > Votre chaîne de connexion peut être différente selon les versions de Visual Studio et de SQL Server Express utilisées pour suivre le didacticiel. Le modèle web.config doit être configuré pour correspondre à votre installation et peut contenir des entrées `Data Source` telles que `(LocalDB)\v11.0` (à partir de SQL Server Express 2012) ou `Data Source=(LocalDB)\MSSQLLocalDB` (à partir de SQL Server Express 2014 et versions ultérieures). Pour plus d’informations sur les chaînes de connexion et les versions de SQL Express, consultez [SQL Server 2016 Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-2016-express-localdb).

### <a name="add-the-controller"></a>Ajouter le contrôleur
1. Appuyez sur **F6** pour générer le projet. 
2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Contrôleurs**. Cliquez ensuite sur **Ajouter** puis sur **Contrôleur**.
   
    ![Ajouter un contrôleur][cache-add-controller]
3. Sélectionnez **Contrôleur MVC 5 avec vues, en utilisant Entity Framework**, puis cliquez sur **Ajouter**. Si vous obtenez une erreur après avoir cliqué sur **Ajouter**, assurez-vous que vous avez déjà généré le projet.
   
    ![Ajouter une classe de contrôleur][cache-add-controller-class]
4. Sélectionnez **Team (ContosoTeamStats.Models)** dans la liste déroulante **Classe de modèle**. Sélectionnez **TeamContext (ContosoTeamStats.Models)** dans la liste déroulante **Classe du contexte de données**. Tapez `TeamsController` dans la zone de texte **Nom du contrôleur** (si elle n’est pas remplie automatiquement). Cliquez sur **Ajouter** pour créer la classe de contrôleur et ajouter les vues par défaut.
   
    ![Configurer un contrôleur][cache-configure-controller]
5. Dans l’**Explorateur de solutions**, développez **Global.asax**, puis double-cliquez sur **Global.asax.cs** pour l’ouvrir.
   
    ![Global.asax.cs][cache-global-asax]
6. Ajoutez les deux instructions `using` suivantes au début du fichier, sous les autres instructions `using` :

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```


1. Ajoutez la ligne de code ci-après à la fin de la méthode `Application_Start` :

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```


1. Dans l’**Explorateur de solutions**, développez `App_Start` et double-cliquez sur `RouteConfig.cs`.
   
    ![RouteConfig.cs][cache-RouteConfig-cs]
2. Dans le code suivant, dans la méthode `RegisterRoutes`, remplacez `controller = "Home"` par `controller = "Teams"`, comme indiqué dans l’exemple suivant :

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```


### <a name="configure-the-views"></a>Configurer les vues
1. Dans l’**Explorateur de solutions**, développez le dossier **Vues** puis le dossier **Partagé** et double-cliquez sur **_Layout.cshtml**. 
   
    ![_Layout.cshtml][cache-layout-cshtml]
2. Modifiez le contenu de l’élément `title` et remplacez `My ASP.NET Application` par `Contoso Team Stats`, comme indiqué dans l’exemple suivant :

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```


1. Dans la section `body`, mettez à jour la première instruction `Html.ActionLink`, remplacez `Application name` par `Contoso Team Stats` et remplacez `Home` par `Teams`.
   
   * Avant : `@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })`
   * Après : `@Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
     
     ![Modifications du code][cache-layout-cshtml-code]
2. Appuyez sur **Ctrl+F5** pour générer et exécuter l’application. Cette version de l’application lit les résultats directement à partir de la base de données. Notez les actions **Créer**, **Modifier**, **Détails** et **Supprimer** qui ont été automatiquement ajoutées à l’application par le modèle automatique **Contrôleur MVC 5 avec vues, en utilisant Entity Framework**. Dans la section suivante du didacticiel, vous allez ajouter le Cache Redis pour optimiser l’accès aux données et fournir des fonctionnalités supplémentaires à l’application.

![Application de départ][cache-starter-application]

## <a name="configure-the-application-to-use-redis-cache"></a>Configurer l’application pour utiliser le Cache Redis
Dans cette section du didacticiel, vous configurez l’exemple d’application pour stocker et récupérer des statistiques d’équipe Contoso à partir d’une instance de Cache Redis Azure à l’aide du client de cache [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

* [Configurer l’application pour utiliser StackExchange.Redis](#configure-the-application-to-use-stackexchangeredis)
* [Mettre à jour la classe TeamsController pour retourner des résultats à partir du cache ou de la base de données](#update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database)
* [Mettre à jour les méthodes Create, Edit et Delete pour utiliser le cache](#update-the-create-edit-and-delete-methods-to-work-with-the-cache)
* [Mettre à jour la vue Teams Index pour utiliser le cache](#update-the-teams-index-view-to-work-with-the-cache)

### <a name="configure-the-application-to-use-stackexchangeredis"></a>Configurer l’application pour utiliser StackExchange.Redis
1. Pour configurer une application cliente dans Visual Studio avec le package NuGet [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis), cliquez sur **Outils > Gestionnaire de package NuGet > Console du Gestionnaire de package**.
2. Exécutez la commande suivante depuis la fenêtre `Package Manager Console` :
    
    ```
    Install-Package StackExchange.Redis
    ```
   
    Le package NuGet télécharge et ajoute les références d'assembly nécessaires pour que votre application cliente puisse accéder à Cache Redis Azure avec le client du cache StackExchange.Redis. Si vous préférez utiliser une version avec nom fort de la bibliothèque du client `StackExchange.Redis`, installez le package `StackExchange.Redis.StrongName`.
3. Dans l’**Explorateur de solutions**, développez le dossier **Contrôleurs** et double-cliquez sur **TeamsController.cs** pour l’ouvrir.
   
    ![Contrôleur Teams][cache-teamscontroller]
4. Ajoutez les deux instructions `using` suivantes à **TeamsController.cs** :

    ```csharp   
    using System.Configuration;
    using StackExchange.Redis;
    ```

5. Ajoutez les deux propriétés suivantes à la classe `TeamsController` :

    ```csharp   
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });
    
    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

6. Créez un fichier nommé `WebAppPlusCacheAppSecrets.config` sur votre ordinateur et placez-le dans un emplacement qui ne sera pas archivé avec le code source de votre exemple d’application au cas où vous décideriez de l’archiver à un emplacement quelconque. Dans cet exemple, le fichier `AppSettingsSecrets.config` se trouve sous `C:\AppSecrets\WebAppPlusCacheAppSecrets.config`.
   
    Modifiez le fichier `WebAppPlusCacheAppSecrets.config` et ajoutez le contenu suivant :

    ```xml
    <appSettings>
      <add key="CacheConnection" value="YourCacheName.redis.cache.windows.net,abortConnect=false,ssl=true,password=YourAccessKey"/>
    </appSettings>
    ```

    Si vous exécutez l’application localement, ces informations sont utilisées pour vous connecter à votre instance de Cache Redis Azure. Plus loin dans ce didacticiel, vous allez approvisionner une instance de Cache Redis Azure et mettre à jour le nom et le mot de passe du cache. Si vous ne souhaitez pas exécuter l’exemple d’application localement, vous pouvez ignorer la création de ce fichier et les étapes suivantes qui référencent le fichier. En effet, lorsque vous déployez sur Azure, l’application récupère les informations de connexion de cache à partir du paramètre d’application de l’application web et non à partir de ce fichier. Étant donné que `WebAppPlusCacheAppSecrets.config` n’est pas déployé sur Azure avec votre application, vous n’en avez pas besoin, sauf si vous souhaitez exécuter l’application localement.

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier **web.config** pour l’ouvrir.
   
    ![Web.config][cache-web-config]
2. Ajoutez l’attribut `file` suivant à l’élément `appSettings`. Si vous avez utilisé un autre nom de fichier ou un autre emplacement, remplacez ces valeurs pour celles indiquées dans l’exemple.
   
   * Avant : `<appSettings>`
   * Après : ` <appSettings file="C:\AppSecrets\WebAppPlusCacheAppSecrets.config">`
  
   Le runtime ASP.NET fusionne le contenu du fichier externe avec le balisage dans l’élément `<appSettings>`. Le runtime ignore l’attribut de fichier si le fichier spécifié est introuvable. Vos secrets (la chaîne de connexion à votre cache) ne sont pas inclus dans le code source de l’application. Lorsque vous déployez votre application web sur Azure, le fichier `WebAppPlusCacheAppSecrests.config` n’est pas déployé (c’est ce que vous souhaitez). Il existe plusieurs façons de spécifier ces secrets dans Azure. Ils seront configurés automatiquement pour vous lorsque vous [approvisionnerez les ressources Azure](#provision-the-azure-resources) plus loin dans ce didacticiel. Pour en savoir plus sur l’utilisation des secrets dans Azure, voir [Meilleures pratiques portant sur le déploiement de mots de passe et autres données sensibles dans ASP.NET et Azure App Service](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

### <a name="update-the-teamscontroller-class-to-return-results-from-the-cache-or-the-database"></a>Mettre à jour la classe TeamsController pour retourner des résultats à partir du cache ou de la base de données
Dans cet exemple, les statistiques d’équipe peuvent être récupérées à partir de la base de données ou à partir du cache. Les statistiques d’équipe sont stockées dans le cache comme `List<Team>`sérialisé et comme ensemble trié à l’aide des types de données Redis. Lors de la récupération des éléments d’un ensemble trié, vous pouvez récupérer certains éléments, récupérer tous les éléments ou effectuer une requête sur certains éléments. Dans cet exemple, vous allez interroger l’ensemble trié pour trouver les 5 meilleures équipes, classées par nombre de victoires.

> [!NOTE]
> Il n’est pas nécessaire de stocker les statistiques d’équipe dans plusieurs formats dans le cache pour utiliser le Cache Redis Azure. Ce didacticiel utilise plusieurs formats pour illustrer certaines façons de mettre des données en cache et les différents types de données que vous pouvez utiliser à cette fin.
> 
> 

1. Ajoutez les instructions `using` suivantes au début du fichier `TeamsController.cs`, avec les autres instructions `using` :

    ```csharp   
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

2. Remplacez la méthode d’implémentation `public ActionResult Index()` actuelle par l’implémentation suivante :

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```


1. Ajoutez les trois méthodes suivantes à la classe `TeamsController` pour implémenter les types d’action `playGames`, `clearCache` et `rebuildDB` de l’instruction switch ajoutée dans l’extrait de code précédent.
   
    La méthode `PlayGames` met à jour les statistiques d’équipe en simulant une saison de jeux, enregistre les résultats dans la base de données et efface les données désormais obsolètes à partir du cache.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    La méthode `RebuildDB` réinitialise la base de données avec l’ensemble d’équipes par défaut, génère des statistiques pour ces équipes et efface les données désormais obsolètes à partir du cache.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    La méthode `ClearCachedTeams` supprime du cache toutes les statistiques d’équipe mises en cache.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    } 
    ```


1. Ajoutez les quatre méthodes suivantes à la classe `TeamsController` pour implémenter les différentes façons de récupérer les statistiques d’équipe à partir du cache et de la base de données. Chacune de ces méthodes retourne un `List<Team>` qui est ensuite affiché par la vue.
   
    La méthode `GetFromDB` lit les statistiques d’équipe à partir de la base de données.
   
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t; 

        return results.ToList<Team>();
    }
    ```

    La méthode `GetFromList` lit les statistiques d’équipe à partir du cache en tant que `List<Team>` sérialisé. En cas d’absence dans le cache, les statistiques d’équipe sont lues à partir de la base de données, puis stockées dans le cache pour la prochaine fois. Dans cet exemple, nous utilisons la sérialisation JSON.NET pour sérialiser les objets .NET vers et depuis le cache. Pour plus d’informations, consultez la rubrique [Utilisation des objets .NET dans le cache Redis Azure](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache).

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    La méthode `GetFromSortedSet` lit les statistiques d’équipe à partir d’un ensemble trié en cache. En cas d’absence dans le cache, les statistiques d’équipe sont lues à partir de la base de données, puis stockées dans le cache en tant qu’ensemble trié.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    La méthode `GetFromSortedSetTop5` lit les 5 meilleures équipes à partir de l’ensemble trié en cache. Elle commence par vérifier l’existence de la clé `teamsSortedSet` dans le cache. Si cette clé n’est pas présente, la méthode `GetFromSortedSet` est appelée pour lire les statistiques d’équipe et les stocker dans le cache. Ensuite, l’ensemble trié mis en cache est interrogé de façon à trouver les 5 meilleures équipes, qui sont retournées.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>Mettre à jour les méthodes Create, Edit et Delete pour utiliser le cache
Le code de génération de modèles automatique qui a été généré dans le cadre de cet exemple inclut des méthodes permettant d’ajouter, modifier et supprimer des équipes. Chaque fois qu’une équipe est ajoutée, modifiée ou supprimée, les données dans le cache deviennent obsolètes. Dans cette section, vous allez modifier ces trois méthodes pour effacer les équipes en cache, de sorte que le cache ne soit pas désynchronisé avec la base de données.

1. Accédez à la méthode `Create(Team team)` dans la classe `TeamsController`. Ajoutez un appel à la méthode `ClearCachedTeams`, comme indiqué dans l’exemple suivant :

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```


1. Accédez à la méthode `Edit(Team team)` dans la classe `TeamsController`. Ajoutez un appel à la méthode `ClearCachedTeams`, comme indiqué dans l’exemple suivant :

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see http://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```


1. Accédez à la méthode `DeleteConfirmed(int id)` dans la classe `TeamsController`. Ajoutez un appel à la méthode `ClearCachedTeams`, comme indiqué dans l’exemple suivant :

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```


### <a name="update-the-teams-index-view-to-work-with-the-cache"></a>Mettre à jour la vue Teams Index pour utiliser le cache
1. Dans l’**Explorateur de solutions**, développez le dossier **Vues** puis le dossier **Équipes**, et double-cliquez sur **Index.cshtml**.
   
    ![Index.cshtml][cache-views-teams-index-cshtml]
2. Au début du fichier, recherchez l’élément de paragraphe suivant :
   
    ![Table d’actions][cache-teams-index-table]
   
    Il s’agit du lien permettant de créer une équipe. Remplacez l’élément de paragraphe par la table suivante. Cette table contient des liens d’action pour créer une nouvelle équipe, jouer une nouvelle saison de jeux, effacer le cache, récupérer les équipes à partir du cache dans plusieurs formats, récupérer les équipes à partir de la base de données et reconstruire la base de données avec de nouvelles données d’exemple.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>    
    </table>
    ```


1. Faites défiler le fichier **Index.cshtml** vers le bas pour visualiser la fin du fichier, puis ajoutez l’élément `tr` suivant, de sorte qu’il représente la dernière ligne de la dernière table du fichier :
   
    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
   
    Cette ligne affiche la valeur `ViewBag.Msg` qui contient un rapport d’état sur l’opération en cours. La valeur `ViewBag.Msg` est définie lorsque vous cliquez sur l’un des liens d’action à l’étape précédente.   
   
    ![Message d’état][cache-status-message]
2. Appuyez sur **F6** pour générer le projet.

## <a name="provision-the-azure-resources"></a>Approvisionner les ressources Azure
Pour héberger votre application dans Azure, vous devez d’abord approvisionner les services Azure requis par votre application. L’exemple d’application de ce didacticiel utilise les services Azure suivants :

* Cache Redis Azure
* Application web App Service
* Base de données SQL

Pour déployer ces services vers un groupe de ressources de votre choix, nouveau ou existant, cliquez sur le bouton **Déployer dans Azure** ci-dessous :

[![Déploiement sur Azure][deploybutton]](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-redis-cache-sql-database%2Fazuredeploy.json)

Le bouton **Déployer dans Azure** utilise le modèle de [démarrage rapide Microsoft Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) [Création d’une application web, du Cache Redis et d’une base de données SQL](https://github.com/Azure/azure-quickstart-templates) pour approvisionner ces services et définir la chaîne de connexion pour la base de données SQL et le paramètre d’application de la chaîne de connexion du Cache Redis Azure.

> [!NOTE]
> Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero) en quelques minutes.
> 
> 

Le bouton **Déployer dans Azure** vous permet d’accéder au Portail Azure et lance le processus de création des ressources décrit par le modèle.

![Déployer dans Azure][cache-deploy-to-azure-step-1]

1. Dans la section des **paramètres de base**, sélectionnez l’abonnement Azure à utiliser et un groupe de ressources existant ou créez-en un nouveau et spécifiez l’emplacement du groupe de ressources.
2. Dans la section **Paramètres**, spécifiez un **nom de connexion administrateur** (n’utilisez pas **admin**), un **mot de passe de connexion administrateur** et un **nom de base de données**. Les autres paramètres sont configurés pour un plan d’hébergement Free App Service et des options moins coûteuses pour la base de données SQL et le Cache Redis Azure, qui ne sont pas fournis avec un niveau Gratuit.

    ![Déployer dans Azure][cache-deploy-to-azure-step-2]

3. Après avoir configuré les paramètres souhaités, faites défiler jusqu’à la fin de la page, lisez les termes et conditions et cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**.
4. Pour commencer l’approvisionnement des ressources, cliquez sur **Purchase** (Acheter).

Pour afficher la progression de votre déploiement, cliquez sur l’icône de notification, puis cliquez sur **Le déploiement a commencé**.

![Le déploiement a commencé][cache-deployment-started]

Vous pouvez visualiser l’état de votre déploiement dans le panneau **Microsoft.Template** .

![Déployer dans Azure][cache-deploy-to-azure-step-3]

Une fois l’approvisionnement terminé, vous pouvez publier votre application sur Azure à partir de Visual Studio.

> [!NOTE]
> Les erreurs susceptibles de se produire pendant le processus d’approvisionnement sont affichées dans le panneau **Microsoft.Template** . Les erreurs courantes sont liées à un trop grand nombre de serveurs SQL ou de plans d’hébergement Free App Service par abonnement. Corrigez les erreurs et redémarrez le processus en cliquant sur **Redéployer** dans le panneau **Microsoft.Template** ou sur le bouton **Déployer dans Azure** de ce didacticiel.
> 
> 

## <a name="publish-the-application-to-azure"></a>Publier l’application sur Azure
Dans cette étape du didacticiel, vous allez publier l’application sur Azure et l’exécuter dans le cloud.

1. Cliquez avec le bouton droit sur le projet **ContosoTeamStats** dans Visual Studio, puis choisissez **Publier**.
   
    ![Publish][cache-publish-app]
2. Cliquez sur **Microsoft Azure App Service**, choisissez **Select Existing** (Sélectionner existant), puis cliquez sur **Publier**.
   
    ![Publier][cache-publish-to-app-service]
3. Sélectionnez l’abonnement utilisé lors de la création des ressources Azure, développez le groupe de ressources contenant les ressources et sélectionnez l’application web souhaitée. Si vous avez utilisé le bouton **Déployer dans Azure**, le nom de votre application web commence par **webSite** et contient des caractères supplémentaires.
   
    ![Sélectionner l’application web][cache-select-web-app]
4. Cliquez sur **OK** pour démarrer le processus de publication. Après quelques instants, le processus de publication se termine et un navigateur s’ouvre. L’exemple d’application y est exécuté. Si vous obtenez une erreur DNS lors de la validation ou de la publication et que le processus d’approvisionnement des ressources Azure pour l’application vient de se terminer, attendez un instant et réessayez.
   
    ![Cache ajouté][cache-added-to-application]

Le tableau suivant décrit chaque lien d’action de l’exemple d’application :

| Action | DESCRIPTION |
| --- | --- |
| Création |Crée une équipe. |
| Play Season |Joue une saison de jeux, met à jour les statistiques d’équipe et efface les données d’équipe obsolètes du cache. |
| Clear Cache |Efface les statistiques d’équipe du cache. |
| List from Cache |Récupère les statistiques d’équipe à partir cache. En cas d’absence dans le cache, charge les statistiques à partir de la base de données et les enregistre dans le cache pour la prochaine fois. |
| Sorted Set from Cache |Récupère les statistiques d’équipe du cache à l’aide d’un ensemble trié. En cas d’absence dans le cache, charge les statistiques à partir de la base de données et les enregistre dans le cache à l’aide d’un ensemble trié. |
| Top 5 Teams from Cache |Récupère les 5 meilleures équipes à partir du cache à l’aide d’un ensemble trié. En cas d’absence dans le cache, charge les statistiques à partir de la base de données et les enregistre dans le cache à l’aide d’un ensemble trié. |
| Load from DB |Récupère les statistiques d’équipe à partir de la base de données. |
| Rebuild DB |Reconstruit la base de données et la recharge avec les exemples de données d’équipe. |
| Edit / Details / Delete |Modifie une équipe, affiche les détails d’une équipe, supprime une équipe. |

Cliquez sur certaines actions et essayez de récupérer les données de différentes sources. Notez que le temps nécessaire pour récupérer les données à partir de la base de données et du cache varie selon la méthode utilisée.

## <a name="delete-the-resources-when-you-are-finished-with-the-application"></a>Supprimer les ressources une fois la procédure liée à l’application terminée
Lorsque vous avez terminé avec l’exemple d’application du didacticiel, vous pouvez supprimer les ressources Azure utilisées afin de réduire les coûts et de préserver les ressources. Si vous utilisez le bouton **Déployer dans Azure** dans la section [Approvisionner les ressources Azure](#provision-the-azure-resources) et que toutes vos ressources se trouvent dans le même groupe de ressources, vous pouvez les supprimer en une seule opération en supprimant le groupe de ressources.

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Groupes de ressources**.
2. Tapez le nom de votre groupe de ressources dans la zone de texte **Filtrer des éléments...** .
3. Cliquez sur **…** à droite de votre groupe de ressources.
4. Cliquez sur **Supprimer**.
   
    ![Supprimer][cache-delete-resource-group]
5. Tapez le nom de votre groupe de ressources et cliquez sur **Supprimer**.
   
    ![Confirmation de suppression][cache-delete-confirm]

Après quelques instants, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.

> [!IMPORTANT]
> Notez que la suppression d’un groupe de ressources est irréversible et que le groupe de ressources, ainsi que toutes les ressources qu’il contient, sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. Si vous avez créé les ressources pour l’hébergement de cet exemple à l’intérieur d’un groupe de ressources existant, vous pouvez supprimer individuellement chaque ressource à partir de leurs panneaux respectifs.
> 
> 

## <a name="run-the-sample-application-on-your-local-machine"></a>Exécuter l’exemple d’application sur votre ordinateur local
Pour exécuter l’application localement sur votre ordinateur, vous avez besoin d’une instance de Cache Redis Azure dans laquelle mettre en cache vos données. 

* Si vous avez publié votre application sur Azure comme décrit dans la section précédente, vous pouvez utiliser l’instance de Cache Redis Azure approvisionnée lors de cette étape.
* Si vous avez une autre instance de Cache Redis Azure, vous pouvez l’utiliser pour exécuter cet exemple localement.
* Si vous avez besoin de créer une instance de Cache Redis Azure, vous pouvez suivre les étapes de la section [Création d’un cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

Une fois que vous avez sélectionné ou créé le cache à utiliser, accédez au cache dans le Portail Azure et récupérez le [nom d’hôte](cache-configure.md#properties) et les [clés d’accès](cache-configure.md#access-keys) pour votre cache. Pour obtenir des instructions, consultez la page [Configuration des paramètres de cache Redis](cache-configure.md#configure-redis-cache-settings).

1. Ouvrez le fichier `WebAppPlusCacheAppSecrets.config` que vous avez créé au cours de l’étape [Configurer l’application pour utiliser le Cache Redis](#configure-the-application-to-use-redis-cache) de ce didacticiel à l’aide de l’éditeur de votre choix.
2. Modifiez l’attribut `value` et remplacez `YourCacheName.redis.cache.windows.net` par le [nom d’hôte](cache-configure.md#properties) de votre cache, puis remplacez `YourAccessKey` par la [clé primaire ou secondaire](cache-configure.md#access-keys) de votre cache en tant que mot de passe.

    ```xml
    <appSettings>
      <add key="CacheConnection" value="YourCacheName.redis.cache.windows.net,abortConnect=false,ssl=true,password=YourAccessKey"/>
    </appSettings>
    ```


1. Appuyez sur **Ctrl+F5** pour exécuter l’application.

> [!NOTE]
> Notez que, dans la mesure où l’application (y compris la base de données) s’exécute localement et où le Cache Redis est hébergé dans Azure, il est possible que le cache réduise les performances de la base de données. Pour de meilleures performances, l’application cliente et l’instance de Cache Redis Azure doivent se trouver au même emplacement. 
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Consultez [Prise en main d’ASP.NET MVC 5](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started) sur le site [ASP.NET](http://asp.net/) pour en savoir plus.
* Pour plus d’exemples de création d’une application web ASP.NET dans App Service, voir [Création d’une application web ASP.NET dans Azure App Service](https://github.com/Microsoft/HealthClinic.biz/wiki/Create-and-deploy-an-ASP.NET-web-app-in-Azure-App-Service) dans la [démonstration](https://github.com/Microsoft/HealthClinic.biz) de 2015 Connect pour [HealthClinic.biz](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/).
  * Pour d’autres démarrages rapides à partir de la démonstration pour HealthClinic.biz, consultez [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)(Démarrages rapides avec les outils de développement Azure).
* Apprenez-en plus sur l’approche d’Entity Framework [Code First pour une nouvelle base de données](https://msdn.microsoft.com/data/jj193542) utilisée dans ce didacticiel.
* Apprenez-en davantage sur les [applications web dans Azure App Service](../app-service/app-service-web-overview.md).
* Découvrez comment [surveiller](cache-how-to-monitor.md) votre cache dans le Portail Azure.
* Explorez les fonctionnalités Premium du Cache Redis Azure
  
  * [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md)
  * [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md)
  * [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md)
  * Pour plus d’informations sur la taille, le débit et la bande passante des caches Premium, voir le [Forum aux questions sur le Cache Redis Azure](cache-faq.md#what-redis-cache-offering-and-size-should-i-use) .

<!-- IMAGES -->
[cache-starter-application]: ./media/cache-web-app-howto/cache-starter-application.png
[cache-added-to-application]: ./media/cache-web-app-howto/cache-added-to-application.png
[cache-create-project]: ./media/cache-web-app-howto/cache-create-project.png
[cache-select-template]: ./media/cache-web-app-howto/cache-select-template.png
[cache-model-add-class]: ./media/cache-web-app-howto/cache-model-add-class.png
[cache-model-add-class-dialog]: ./media/cache-web-app-howto/cache-model-add-class-dialog.png
[cache-add-controller]: ./media/cache-web-app-howto/cache-add-controller.png
[cache-add-controller-class]: ./media/cache-web-app-howto/cache-add-controller-class.png
[cache-configure-controller]: ./media/cache-web-app-howto/cache-configure-controller.png
[cache-global-asax]: ./media/cache-web-app-howto/cache-global-asax.png
[cache-RouteConfig-cs]: ./media/cache-web-app-howto/cache-RouteConfig-cs.png
[cache-layout-cshtml]: ./media/cache-web-app-howto/cache-layout-cshtml.png
[cache-layout-cshtml-code]: ./media/cache-web-app-howto/cache-layout-cshtml-code.png
[redis-cache-manage-nuget-menu]: ./media/cache-web-app-howto/redis-cache-manage-nuget-menu.png
[redis-cache-stack-exchange-nuget]: ./media/cache-web-app-howto/redis-cache-stack-exchange-nuget.png
[cache-teamscontroller]: ./media/cache-web-app-howto/cache-teamscontroller.png
[cache-web-config]: ./media/cache-web-app-howto/cache-web-config.png
[cache-views-teams-index-cshtml]: ./media/cache-web-app-howto/cache-views-teams-index-cshtml.png
[cache-teams-index-table]: ./media/cache-web-app-howto/cache-teams-index-table.png
[cache-status-message]: ./media/cache-web-app-howto/cache-status-message.png
[deploybutton]: ./media/cache-web-app-howto/deploybutton.png
[cache-deploy-to-azure-step-1]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-1.png
[cache-deploy-to-azure-step-2]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-2.png
[cache-deploy-to-azure-step-3]: ./media/cache-web-app-howto/cache-deploy-to-azure-step-3.png
[cache-deployment-started]: ./media/cache-web-app-howto/cache-deployment-started.png
[cache-publish-app]: ./media/cache-web-app-howto/cache-publish-app.png
[cache-publish-to-app-service]: ./media/cache-web-app-howto/cache-publish-to-app-service.png
[cache-select-web-app]: ./media/cache-web-app-howto/cache-select-web-app.png
[cache-publish]: ./media/cache-web-app-howto/cache-publish.png
[cache-delete-resource-group]: ./media/cache-web-app-howto/cache-delete-resource-group.png
[cache-delete-confirm]: ./media/cache-web-app-howto/cache-delete-confirm.png

