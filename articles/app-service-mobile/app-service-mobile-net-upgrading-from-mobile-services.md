---
title: "Mettre à niveau à partir de Mobile Services vers Azure App Service"
description: "Découvrez comment facilement mettre à niveau votre application Mobile Services vers App Service Mobile Apps"
services: app-service\mobile
documentationcenter: 
author: conceptdev
manager: crdun
editor: 
ms.assetid: 9c0ac353-afb6-462b-ab94-d91b8247322f
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: f07b1d6037ff8ca16b673e6a1a235769355a9993
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="upgrade-your-existing-net-azure-mobile-service-to-app-service"></a>Mettre à niveau votre application .NET Azure Mobile Services existante vers App Service
App Service Mobile représente une nouvelle façon de créer des applications mobiles avec Microsoft Azure. Pour en savoir plus, consultez [Que sont les applications Mobile Apps ?]

Cette rubrique décrit comment mettre à niveau une application principale .NET existante depuis Azure Mobile Services vers une nouvelle application App Service Mobile Apps. Pendant cette mise à niveau, votre application Mobile Services existante peut continuer à fonctionner.   Si vous devez mettre à niveau une application principale Node.js, voir [Mettre à niveau votre application .NET Azure Mobile Services existante vers App Service](app-service-mobile-node-backend-upgrading-from-mobile-services.md).

Quand un serveur principal mobile est mis à niveau vers Azure App Service, il a accès à toutes les fonctionnalités App Service et c’est la [tarification App Service]qui est appliquée, et non celle de Mobile Services.

## <a name="migrate-vs-upgrade"></a>Migration et mise à niveau
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Il est recommandé d’ [effectuer une migration](app-service-mobile-migrating-from-mobile-services.md) avant une mise à niveau. Ainsi, vous pouvez placer les deux versions de votre application sur le même plan App Service sans générer de frais supplémentaires.
>
>

### <a name="improvements-in-mobile-apps-net-server-sdk"></a>Améliorations du Kit de développement logiciel (SDK) serveur .NET Mobile Apps
La mise à niveau vers le nouveau [Kit de développement logiciel (SDK) Mobile Apps](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) offre les avantages suivants :

* Plus de flexibilité sur les dépendances NuGet. L'environnement d'hébergement ne fournit plus ses propres versions des packages NuGet : vous pouvez donc utiliser d'autres versions compatibles. Toutefois, si de nouvelles résolutions de bogues critiques ou mises à jour de sécurité sont apportées au SDK du serveur mobile ou à ses dépendances, vous devez mettre à jour manuellement votre service.
* Plus de souplesse dans le SDK mobile. Vous pouvez contrôler explicitement les fonctionnalités et itinéraires définis, comme l'authentification, les API de table et le point de terminaison de l'enregistrement Push. Pour en savoir plus, consultez [Utilisation du Kit de développement logiciel (SDK) serveur .NET pour Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).
* Prise en charge d'autres itinéraires et types de projets ASP.NET. Vous pouvez désormais héberger des contrôleurs d'API web et MVC dans votre projet de backend mobile.
* Prise en charge de nouvelles fonctionnalités d'authentification App Service. Cela vous permet d'utiliser une configuration d'authentification commune à vos applications mobiles et web.

## <a name="overview"></a>Présentation de la mise à niveau de base
Dans de nombreux cas, la mise à niveau consiste simplement à basculer vers le nouveau SDK serveur Mobile Apps et à republier votre code dans une nouvelle instance Mobile Apps. Il existe toutefois des scénarios qui nécessitent une configuration supplémentaire, tels que les scénarios d'authentification avancée et l'utilisation de tâches planifiées. Ils sont couverts individuellement dans les sections ultérieures.

> [!TIP]
> Il est conseillé de lire et de comprendre entièrement le reste de cette rubrique avant de commencer une mise à niveau. Prenez note de toutes les fonctionnalités répertoriées ci-dessous que vous utilisez.
>
>

Les Kits de développement logiciel (SDK) clients Mobile Services ne sont **pas** compatibles avec le nouveau Kit de développement logiciel (SDK) serveur Mobile Apps. Afin d’assurer la continuité du service pour votre application, vous ne devez pas publier des modifications apportées à un site en train de desservir des clients publiés. Vous devez plutôt créer une application mobile qui sert de doublon. Vous pouvez placer cette application sur le même plan App Service pour éviter d’encourir des frais supplémentaires.

Vous avez alors deux versions de l’application : l’une qui reste la même et délivre les applications publiées et l’autre que vous pouvez mettre à niveau et cibler avec une nouvelle version du client. Vous pouvez déplacer et tester votre code à votre rythme, mais vous devez vous assurer que tous les correctifs de bogues que vous apportez sont appliqués aux deux versions. Une fois que le nombre souhaité d’applications clientes ont effectué la mise à jour vers la dernière version, vous supprimez l’application originale qui a migré si vous le souhaitez.

Le processus de mise à niveau est le suivant :

1. Créer une application Mobile App
2. Mettre à jour le projet pour utiliser les nouveaux Kits de développement logiciel (SDK) serveur
3. Publier une nouvelle version de votre application cliente
4. (Facultatif) Supprimer l’instance originale qui a migré

## <a name="mobile-app-version"></a>Création d’une seconde instance d’application
La première étape de la mise à niveau consiste à créer la ressource Mobile Apps qui hébergera la nouvelle version de votre application. Si vous avez déjà migré un service mobile existant, vous voulez créer cette version sur le même plan d’hébergement. Ouvrez le [portail Azure] et accédez à votre application migrée. Notez le plan App Service sur lequel elle s’exécute.

Ensuite, créez la seconde instance d’application en suivant les [instructions de création d’un serveur principal .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Quand vous êtes invité à sélectionner votre plan App Service ou « plan d’hébergement », choisissez celui de votre application qui a migré.

Vous souhaiterez probablement utiliser les mêmes base de données et hub de notifications que dans Mobile Services. Copiez ces valeurs en ouvrant le [portail Azure] et en accédant à l’application d’origine, puis cliquez sur **Paramètres** > **Paramètres d’application**. Sous **Chaînes de connexion**, copiez `MS_NotificationHubConnectionString` et `MS_TableConnectionString`. Accédez à votre nouveau site de mise à niveau et collez-les en remplaçant les valeurs existantes. Répétez ce processus pour tous les autres paramètres d’application dont votre application a besoin. Si vous n’utilisez pas un service qui a migré, vous pouvez lire des chaînes de connexion et des paramètres d’application sous l’onglet **Configurer** de la section Mobile Services du [portail Azure Classic].

Faites une copie du projet ASP.NET de votre application et publiez-la sur votre nouveau site. En utilisant une copie de votre application cliente mise à jour avec la nouvelle URL, vérifiez que tout fonctionne comme prévu.

## <a name="updating-the-server-project"></a>Mise à jour du projet serveur
Mobile Apps fournit une nouveau [Kit de développement logiciel (SDK) Mobile App Server] qui fournit de nombreuses fonctionnalités identiques à celles du runtime Mobile Services. Tout d’abord, vous devez supprimer toutes les références aux packages Mobile Services. Dans le gestionnaire de packages NuGet, recherchez `WindowsAzure.MobileServices.Backend`. La plupart des applications affichent plusieurs packages, notamment `WindowsAzure.MobileServices.Backend.Tables` et `WindowsAzure.MobileServices.Backend.Entity`. Dans ce cas, commencez avec le package le plus bas dans l’arborescence des dépendances, notamment `Entity`et supprimez-le. Quand vous y êtes invité, ne supprimez pas tous les packages dépendants. Répétez ce processus jusqu’à ce que vous ayez supprimé `WindowsAzure.MobileServices.Backend` .

À ce stade, vous avez un projet qui ne fait plus référence aux SDK Mobile Services.

Ensuite, vous allez ajouter des références aux SDK Mobile Apps. Pour cette mise à niveau, la plupart des développeurs préfèrent télécharger et installer le package `Microsoft.Azure.Mobile.Server.Quickstart` , car il permet d’extraire la totalité de l’ensemble requis.

De nombreuses erreurs de compilateur proviennent des différences entre les SDK, mais elles sont faciles à corriger et elles sont décrites plus loin dans cette section.

### <a name="base-configuration"></a>Configuration de base
Ensuite, dans WebApiConfig.cs, vous pouvez remplacer :

        // Use this class to set configuration options for your mobile service
        ConfigOptions options = new ConfigOptions();

        // Use this class to set WebAPI configuration options
        HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

par

        HttpConfiguration config = new HttpConfiguration();
        new MobileAppConfiguration()
            .UseDefaultConfiguration()
        .ApplyTo(config);

> [!NOTE]
> Si vous souhaitez en savoir plus sur le nouveau SDK serveur .NET et sur la façon d’ajouter/de supprimer des fonctionnalités de votre application, consultez la rubrique [Utilisation du Kit de développement logiciel (SDK) serveur .NET] .
>
>

Si votre application utilise les fonctionnalités d’authentification, vous devez également inscrire un intergiciel (middleware) OWIN. Dans ce cas, vous devez déplacer le code de configuration ci-dessus dans une nouvelle classe de démarrage OWIN.

1. Ajoutez le package NuGet `Microsoft.Owin.Host.SystemWeb` s’il n’est pas déjà inclus dans votre projet.
2. Dans Visual Studio, cliquez avec le bouton droit sur votre projet, puis sélectionnez **Ajouter** -> **Nouvel élément**. Sélectionnez **Web** -> **Général** -> **Classe de démarrage OWIN**.
3. Déplacez le code ci-dessus pour MobileAppConfiguration de `WebApiConfig.Register()` vers la méthode `Configuration()` de votre nouvelle classe de démarrage.

Assurez-vous que la méthode `Configuration()` se termine par :

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Il existe des modifications supplémentaires liées à l’authentification qui sont abordées dans la section dédiée à l’authentification ci-après.

### <a name="working-with-data"></a>Utilisation des données
Dans Mobile Services, le nom de l’application mobile servait de nom de schéma par défaut dans la configuration Entity Framework.

Pour vous assurer d’avoir le même schéma référencé que celui d’avant, utilisez la commande suivante pour définir le schéma dans le DbContext de votre application :

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");

Vérifiez que MS_MobileServiceName est défini si vous effectuez l’étape ci-dessus. Vous pouvez également fournir un autre nom de schéma si votre application l’a personnalisé précédemment.

### <a name="system-properties"></a>Propriétés système
#### <a name="naming"></a>Dénomination
Dans le Kit de développement logiciel (SDK) serveur Azure Mobile Services, les propriétés système contiennent toujours un préfixe constitué de deux traits de soulignement (`__`) :

* __createdAt
* __updatedAt
* __deleted
* __version

Les Kits de développement logiciel (SDK) clients Mobile Services ont une logique spéciale pour l’analyse des propriétés système dans ce format.

Dans Azure Mobile Apps, les propriétés système n’ont plus de format spécial et portent les noms suivants :

* createdAt
* updatedAt
* deleted
* version

Les Kits de développement logiciel (SDK) clients Mobile Apps utilisent les nouveaux noms de propriétés système, donc aucune modification du code client n’est exigée. Toutefois, si vous effectuez directement des appels REST à votre service, alors vous devez modifier vos requêtes en conséquence.

#### <a name="local-store"></a>Magasin local
Les modifications apportées aux noms des propriétés système signifient qu’une base de données locale de synchronisation hors connexion pour Mobile Services n’est pas compatible avec Mobile Apps. Si possible, vous devez éviter de mettre à niveau des applications clientes depuis Mobile Services vers Mobile Apps tant que les modifications en attente n’ont pas été envoyées au serveur. Ensuite, l’application mise à niveau doit utiliser un nouveau nom de fichier de base de données.

Si une application cliente est mise à niveau depuis Mobile Services vers Mobile Apps alors qu’il existe des modifications hors connexion en attente dans la file d’attente des opérations, alors la base de données système doit être mise à jour pour utiliser les nouveaux noms de colonne. Sur iOS, cette mise à jour est possible en utilisant de légères migrations pour modifier les noms de colonne. Sur Android et le client géré .NET, vous devez écrire du code SQL personnalisé pour renommer les colonnes de vos tables d’objets de données.

Sur iOS, vous devez modifier votre schéma de base de données principal pour vos entités de données afin qu’il corresponde à ce qui suit. Notez que les propriétés `createdAt`, `updatedAt` et `version` n’ont plus de préfixe `ms_` :

| Attribut | type | Remarque |
| --- | --- | --- |
| id |Chaîne, marquée requise |clé primaire dans le magasin distant |
| createdAt |Date |(facultatif) correspond à la propriété système createdAt |
| updatedAt |Date |(facultatif) correspond à la propriété système updatedAt |
| version |Chaîne |(facultatif) permet de détecter les conflits, correspond à version |

#### <a name="querying-system-properties"></a>Interrogation des propriétés système
Dans Azure Mobile Services, les propriétés système ne sont pas envoyées par défaut, mais uniquement quand elles sont demandées à l’aide de la chaîne de requête `__systemProperties`. Par opposition, dans Azure Mobile Apps, les propriétés système sont **toujours sélectionnées** car elles font partie du modèle objet du SDK serveur.

Cette modification affecte principalement les implémentations personnalisées des gestionnaires de domaine, comme les extensions de `MappedEntityDomainManager`. Dans Mobile Services, si un client ne demande jamais de propriétés système, il est possible d’utiliser un `MappedEntityDomainManager` qui ne correspond en fait pas à toutes les propriétés. Toutefois, dans Azure Mobile Apps, ces propriétés non mappées provoquent une erreur dans les requêtes GET.

Pour résoudre ce problème, le plus simple consiste à modifier vos DTO pour qu’ils héritent de `ITableData` au lieu de `EntityData`. Ensuite, ajoutez l’attribut `[NotMapped]` aux champs à omettre.

L’exemple suivant définit `TodoItem` sans aucune propriété système :

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Remarque : si vous obtenez des erreurs sur `NotMapped`, ajoutez une référence à l’assembly `System.ComponentModel.DataAnnotations`.

### <a name="cors"></a>CORS
Mobile Services incluait une prise en charge de CORS en encapsulant la solution ASP.NET CORS. Cette couche d’encapsulation a été supprimée pour donner plus de contrôle au développeur, donc vous pouvez exploiter directement la [prise en charge ASP.NET CORS](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api).

Les principales sources de préoccupation en cas d’utilisation de CORS concernent les en-têtes `eTag` et `Location` qui doivent être autorisés pour que les Kits de développement logiciel (SDK) clients puissent fonctionner correctement.

### <a name="push-notifications"></a>Notifications Push
En matière de notifications Push, l'élément principal qui vous semblera peut-être manquant dans le kit de développement logiciel de serveur est la classe PushRegistrationHandler. Les inscriptions sont traitées légèrement différemment dans Mobile Apps et les inscriptions sans balise sont activées par défaut. La gestion des balises peut être accomplie à l'aide d'API personnalisées. Consultez les instructions liées à l’ [inscription des balises](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags) pour plus d’informations.

### <a name="scheduled-jobs"></a>Tâches planifiées
Les tâches planifiées ne sont pas intégrées à Mobile Apps, si bien que toutes les tâches existantes dans votre serveur principal .NET doivent être mises à niveau individuellement. Une option consiste à créer une [tâche web] planifiée sur le site de code Mobile App. Vous pouvez également configurer un contrôleur contenant le code de votre tâche et configurer [Azure Scheduler] pour traiter ce point de terminaison au moment prévu.

### <a name="miscellaneous-changes"></a>Modifications diverses
Tous les ApiController consommés par un client mobile doivent désormais avoir l’attribut `[MobileAppController]` . Celui-ci n’est plus inclus par défaut si bien que les autres ApiController ne sont pas affectés par les formateurs mobiles.

L’objet `ApiServices` ne fait plus partie du SDK. Pour accéder aux paramètres de Mobile Apps, vous pouvez utiliser les éléments suivants :

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

De même, la journalisation s’effectue désormais avec le traçage ASP.NET standard :

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

## <a name="authentication"></a>Considérations relatives à l’authentification
Les composants d’authentification de Mobile Services sont maintenant déplacés vers la fonctionnalité d’authentification/autorisation App Service. Pour en savoir plus sur l’activation de cette fonctionnalité pour votre site, consultez la rubrique [Ajouter l’authentification à votre application mobile](app-service-mobile-ios-get-started-users.md) .

Pour certains fournisseurs, comme AAD, Facebook et Google, vous devez être en mesure d’exploiter l’inscription existante à partir de l’application de votre copie. Il vous suffit simplement d’accéder au portail du fournisseur d’identité et d’ajouter une nouvelle URL de redirection à l’inscription. Ensuite, configurez l’authentification/autorisation App Service avec l’ID client et le secret.

### <a name="controller-action-authorization"></a>Autorisation des actions de contrôleur
Toutes les instances de l’attribut `[AuthorizeLevel(AuthorizationLevel.User)]` doivent désormais être modifiées pour utiliser l’attribut `[Authorize]` ASP.NET standard. En outre, les contrôleurs sont désormais anonymes par défaut, comme dans d’autres applications ASP.NET.
Si vous utilisez une des autres options AuthorizeLevel, comme Admin ou Application, notez qu’elles ont disparu. Vous pouvez à la place configurer AuthorizationFilters pour utiliser des secrets partagés ou configurer un principal du service AAD pour activer les appels de service à service en toute sécurité.

### <a name="getting-additional-user-information"></a>Obtention d’informations utilisateur supplémentaires
Vous pouvez obtenir des informations utilisateur supplémentaires, notamment des jetons d’accès par le biais de la méthode `GetAppServiceIdentityAsync()` :

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();

De plus, si votre application accepte les dépendances vis-à-vis des ID utilisateur, notamment en les stockant dans une base de données, il est important de noter que les ID utilisateur sont différents entre Mobile Services et App Service Mobile Apps. Vous pouvez quand même obtenir l’ID utilisateur Mobile Services. Toutes les sous-classes ProviderCredentials ont une propriété UserId. Ainsi, en reprenant l’exemple précédent :

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

si votre application accepte les dépendances vis-à-vis des ID utilisateur, il est important d’exploiter la même inscription avec un fournisseur d’identité dans la mesure du possible. Les ID utilisateur sont généralement limités à l'inscription de l'application qui a été utilisée, si bien que l'introduction d'une nouvelle inscription peut créer des problèmes de mise en correspondance des utilisateurs avec leurs données.

### <a name="custom-authentication"></a>Authentification personnalisée
Si votre application utilise une solution d’authentification personnalisée, assurez-vous que le site mis à niveau a accès au système. Suivez les nouvelles instructions liées à l’authentification personnalisée indiquées dans [Présentation du Kit de développement logiciel (SDK) serveur .NET] pour intégrer votre solution. Notez que les composants d’authentification personnalisée sont encore en version préliminaire.

## <a name="updating-clients"></a>Mise à jour des clients
Une fois que vous avez un serveur principal Mobile App opérationnel, vous pouvez travailler sur une nouvelle version de votre application cliente qui la consomme. Mobile Apps inclut également une nouvelle version des Kits de développement logiciel (SDK) clients, et comme pour la mise à niveau serveur ci-dessus, vous devez supprimer toutes les références aux Kits de développement logiciel (SDK) Mobile Services avant d’installer les versions Mobile Apps.

L’une des principales modifications entre les versions a trait aux constructeurs qui n’exigent plus de clé d’application. Désormais, vous passez simplement l’URL de votre application mobile. Par exemple, sur les clients .NET, le constructeur `MobileServiceClient` est désormais :

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Pour en savoir plus sur l’installation des nouveaux Kits de développement logiciel (SDK) et l’utilisation de la nouvelle structure, cliquez sur les liens ci-dessous :

* [iOS version 3.0.0 ou ultérieure](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) version 2.0.0 ou ultérieure](app-service-mobile-dotnet-how-to-use-client-library.md)

Si votre application emploie des notifications Push, notez les instructions d’inscription spécifiques de chaque plateforme, car elles ont également fait l’objet de quelques modifications.

Une fois la nouvelle version cliente prête, essayez-la par rapport à votre projet de serveur mis à niveau. Après avoir vérifié qu’elle fonctionne, vous pouvez publier une nouvelle version de votre application pour vos clients. Enfin, une fois que vos clients ont eu l’occasion de recevoir ces mises à jour, vous pouvez supprimer la version Mobile Services de votre application. À ce stade, vous avez entièrement mis à niveau votre application vers App Service Mobile Apps avec le tout dernier SDK serveur Mobile Apps.

<!-- URLs. -->

[portail Azure]: https://portal.azure.com/
[portail Azure Classic]: https://manage.windowsazure.com/
[Que sont les applications Mobile Apps ?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Kit de développement logiciel (SDK) Mobile App Server]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[tâche web]: https://github.com/Azure/azure-webjobs-sdk/wiki
[Utilisation du Kit de développement logiciel (SDK) serveur .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[tarification App Service]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[Présentation du Kit de développement logiciel (SDK) serveur .NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
