---
title: "Mettre à niveau à partir de Mobile Services vers Azure App Service - Node.js"
description: "Découvrez comment facilement mettre à niveau votre application Mobile Services vers App Service Mobile Apps"
services: app-service\mobile
documentationcenter: 
author: ggailey777
manager: yochayk
editor: 
ms.assetid: c58f6df0-5aad-40a3-bddc-319c378218e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: glenga
ms.openlocfilehash: 888717afe14f29fd50da6478c2bba077616a5379
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="upgrade-your-existing-nodejs-azure-mobile-service-to-app-service"></a>Mettre à niveau votre application .NET Azure Mobile Services existante vers App Service
App Service Mobile représente une nouvelle façon de créer des applications mobiles avec Microsoft Azure. Pour en savoir plus, consultez [Que sont les applications Mobile Apps ?]

Cette article décrit comment mettre à niveau une application backend Node.js existante depuis Azure Mobile Services vers une nouvelle fonctionnalité App Service Mobile Apps. Pendant cette mise à niveau, votre application Mobile Services existante peut continuer à fonctionner.  Si vous devez mettre à niveau une application principale Node.js, voir [Mettre à niveau votre application .NET Azure Mobile Services existante vers App Service](app-service-mobile-net-upgrading-from-mobile-services.md).

Quand un serveur principal mobile est mis à niveau vers Azure App Service, il a accès à toutes les fonctionnalités App Service et c’est la [tarification App Service]qui est appliquée, et non celle de Mobile Services.

## <a name="migrate-vs-upgrade"></a>Migration et mise à niveau
[!INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

> [!TIP]
> Il est recommandé d’ [effectuer une migration](app-service-mobile-migrating-from-mobile-services.md) avant une mise à niveau. Ainsi, vous pouvez placer les deux versions de votre application sur le même plan App Service sans générer de frais supplémentaires.
>
>

### <a name="improvements-in-mobile-apps-nodejs-server-sdk"></a>Améliorations du Kit de développement logiciel (SDK) serveur Node.js Mobile Apps
La mise à niveau vers le nouveau [Kit SDK Mobile Apps](https://www.npmjs.com/package/azure-mobile-apps) offre de nombreuses améliorations, notamment :

* Basé sur [l’infrastructure Express](http://expressjs.com/en/index.html), le nouveau Kit de développement logiciel (SDK) Node est léger et conçu pour s’adapter aux nouvelles versions Node au fur et à mesure de leur sortie. Vous pouvez personnaliser le comportement de l’application avec l’intergiciel Express.
* Les améliorations des performances sont significatives par rapport au Kit de développement logiciel (SDK) Mobile Services.
* Vous pouvez désormais héberger un site web avec votre serveur principal mobile. De même, il est facile d’ajouter le Kit de développement logiciel (SDK) Azure Mobile à n’importe quelle application v4 existante.
* Conçu pour le développement multiplateforme et local, le Kit de développement logiciel (SDK) Mobile Apps peut être développé et exécuté localement sur les plateformes Windows, Linux et OSX. Il est désormais facile d’utiliser des techniques de développement Node courantes comme l’exécution de tests [Mocha](https://mochajs.org/) avant le déploiement.

## <a name="overview"></a>Présentation de la mise à niveau de base
Pour faciliter la mise à niveau d’une application principale Node.js, Azure App Service fournit un package de compatibilité.  Après la mise à niveau, vous disposez d’un nouveau site qui peut être déployé vers un nouveau site App Service.

Les Kits de développement logiciel (SDK) clients Mobile Services ne sont **pas** compatibles avec le nouveau Kit de développement logiciel (SDK) serveur Mobile Apps. Afin d’assurer la continuité du service pour votre application, vous ne devez pas publier des modifications apportées à un site en train de desservir des clients publiés. Vous devez plutôt créer une application mobile qui sert de doublon. Vous pouvez placer cette application sur le même plan App Service pour éviter d’encourir des frais supplémentaires.

Vous avez alors deux versions de l’application : l’une qui reste la même et délivre les applications publiées et l’autre que vous pouvez mettre à niveau et cibler avec une nouvelle version du client. Vous pouvez déplacer et tester votre code à votre rythme, mais vous devez vous assurer que tous les correctifs de bogues que vous apportez sont appliqués aux deux versions. Une fois que le nombre souhaité d’applications clientes ont effectué la mise à jour vers la dernière version, vous supprimez l’application originale qui a migré si vous le souhaitez. Cela n’entraîne aucun frais supplémentaire si l’application est hébergée sur le même plan App Service que votre application Mobile App.

Le processus de mise à niveau est le suivant :

1. Téléchargez votre projet Azure Mobile Service existant (migré).
2. Convertissez le projet en projet Azure Mobile App à l’aide du package de compatibilité.
3. Corrigez les éventuelles différences (par exemple les paramètres d’authentification).
4. Déployez votre projet Azure Mobile App converti vers un nouveau App Service.
5. Publiez une nouvelle version de votre application cliente qui utilise la nouvelle application mobile.
6. (Facultatif) Supprimez l’application de service mobile d’origine migrée.

La suppression peut se produire si vous ne voyez pas de trafic sur votre service mobile d’origine migré.

## <a name="install-npm-package"></a> Installer les éléments requis
Vous devez installer [Node] sur votre ordinateur local  ainsi que le package de compatibilité.  Une fois Node installé, vous pouvez exécuter la commande suivante à partir d’une nouvelle commande ou d’une invite PowerShell :

```npm i -g azure-mobile-apps-compatibility```

## <a name="obtain-ams-scripts"></a> Obtenir vos scripts Azure Mobile Services
* Connectez-vous au [Portail Azure].
* Dans **Toutes les ressources** ou **App Services**, recherchez votre site Mobile Services.
* Sur le site, cliquez sur **Outils** -> **Kudu** -> **Accéder** pour ouvrir le site Kudu.
* Cliquez sur **Console de débogage** -> **PowerShell** pour ouvrir la console de débogage.
* Accédez à `site/wwwroot/App_Data/config` en cliquant successivement sur chaque répertoire.
* Cliquez sur l’icône de téléchargement située à côté du répertoire `scripts` .

Les scripts sont alors téléchargés au format ZIP.  Créez un répertoire sur votre ordinateur local et décompressez le fichier `scripts.ZIP` dans ce répertoire.  Le répertoire `scripts` est alors créé.

## <a name="scaffold-app"></a> Structurer le nouveau backend Azure Mobile Apps
Exécutez la commande suivante à partir du répertoire contenant le répertoire de scripts :

```scaffold-mobile-app scripts out```

Un backend Azure Mobile Apps structuré est alors créé dans le répertoire `out` .  Même si ce n’est pas obligatoire, il est recommandé d’intégrer le répertoire `out` dans le référentiel de code source de votre choix.

## <a name="deploy-ama-app"></a> Déployer votre backend Azure Mobile Apps
Au cours du déploiement, vous devez effectuer les opérations suivantes :

1. Créer une application Mobile App dans le [Portail Azure].
2. Exécuter le script `createViews.sql` sur votre base de données connectée.
3. Lier la base de données liée à votre Mobile Service à votre nouvel App Service.
4. Lier d’autres ressources (par exemple Notification Hubs) au nouvel App Service.
5. Déployer le code généré sur votre nouveau site.

### <a name="create-a-new-mobile-app"></a>Créer une application Mobile App
1. Connectez-vous au [Portail Azure].
2. Cliquez sur **+NOUVEAU** > **Web + Mobile** > **Application mobile**, puis indiquez le nom de votre serveur principal Mobile App.
3. Pour **Groupe de ressources**, sélectionnez un groupe de ressources existant ou créez-en un (en utilisant le même nom que votre application).

    Sélectionnez un autre plan App Service ou créez-en un. Pour plus d’informations sur les plans App Service et sur la création d’un plan à un autre niveau de tarification et à l’emplacement souhaité, voir [Présentation détaillée des plans Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
4. Pour le **plan App Service**, le plan par défaut (du [niveau Standard](https://azure.microsoft.com/pricing/details/app-service/)) est sélectionné. Vous pouvez sélectionner un autre plan ou en [créer un](../app-service/app-service-plan-manage.md#create-an-app-service-plan). Les paramètres du plan App Service déterminent [l’emplacement, les fonctionnalités, les coûts et les ressources de calcul](https://azure.microsoft.com/pricing/details/app-service/) associés à votre application.

    Après avoir choisi le plan, cliquez sur **Créer**. Cette action crée le serveur principal Mobile App.

### <a name="run-createviewssql"></a>Exécuter CreateViews.SQL
L’application structurée contient un fichier appelé `createViews.sql`.  Ce script doit être exécuté sur la base de données cible.  La chaîne de connexion de la base de données cible peut être obtenue à partir de votre service mobile migré dans la page **Paramètres** sous **Chaînes de connexion**.  Elle est nommée `MS_TableConnectionString`.

Vous pouvez exécuter ce script à partir de SQL Server Management Studio ou de Visual Studio.

### <a name="link-the-database-to-your-app-service"></a>Lier la base de données à votre App Service
Liez la base de données existante à votre App Service :

* Dans le [Portail Azure], ouvrez votre App Service.
* Sélectionnez **Tous les paramètres** -> **Connexions de données**.
* Cliquez sur **+ Ajouter**.
* Dans la liste déroulante, sélectionnez **Base de données SQL**
* Sous **Base de données SQL**, sélectionnez votre base de données existante, puis cliquez sur **Sélectionner**.
* Sous **Chaîne de connexion**, entrez le nom d’utilisateur et le mot de passe de la base de données, puis cliquez sur **OK**.
* Dans la page **Ajouter des connexions de données**, cliquez sur **OK**.

Vous trouverez le nom d’utilisateur et le mot de passe en affichant la chaîne de connexion de la base de données cible dans votre service mobile migré.

### <a name="set-up-authentication"></a>Configurer l’authentification
Azure Mobile Apps vous permet de configurer l’authentification Azure Active Directory, Facebook, Google, Microsoft et Twitter au sein du service.  Une authentification personnalisée doit être développée séparément.  Pour plus d’informations, reportez-vous à la documentation relative aux [concepts d’authentification] et au [démarrage rapide de l’authentification].  

## <a name="updating-clients"></a>Mettre à jour les clients mobiles
Une fois que vous avez un serveur principal Mobile App opérationnel, vous pouvez travailler sur une nouvelle version de votre application cliente qui la consomme. Mobile Apps inclut également une nouvelle version des Kits de développement logiciel (SDK) clients, et comme pour la mise à niveau serveur ci-dessus, vous devez supprimer toutes les références aux Kits de développement logiciel (SDK) Mobile Services avant d’installer les versions Mobile Apps.

L’une des principales modifications entre les versions a trait aux constructeurs qui n’exigent plus de clé d’application.
Désormais, vous passez simplement l’URL de votre application mobile. Par exemple, sur les clients .NET, le constructeur `MobileServiceClient` est désormais :

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net" // URL of the Mobile App
        );

Pour en savoir plus sur l’installation des nouveaux Kits de développement logiciel (SDK) et l’utilisation de la nouvelle structure, cliquez sur les liens ci-dessous :

* [Android version 2.2 ou ultérieure](app-service-mobile-android-how-to-use-client-library.md)
* [iOS version 3.0.0 ou ultérieure](app-service-mobile-ios-how-to-use-client-library.md)
* [.NET (Windows/Xamarin) version 2.0.0 ou ultérieure](app-service-mobile-dotnet-how-to-use-client-library.md)
* [Apache Cordova version 2.0 ou ultérieure](app-service-mobile-cordova-how-to-use-client-library.md)

Si votre application emploie des notifications Push, notez les instructions d’inscription spécifiques de chaque plateforme, car elles ont également fait l’objet de quelques modifications.

Une fois la nouvelle version cliente prête, essayez-la par rapport à votre projet de serveur mis à niveau. Après avoir vérifié qu’elle fonctionne, vous pouvez publier une nouvelle version de votre application pour vos clients. Enfin, une fois que vos clients ont eu l’occasion de recevoir ces mises à jour, vous pouvez supprimer la version Mobile Services de votre application. À ce stade, vous avez entièrement mis à niveau votre application vers App Service Mobile Apps avec le tout dernier SDK serveur Mobile Apps.

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Que sont les applications Mobile Apps ?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /en-us/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /en-us/documentation/services/scheduler/
[Web Job]: https://github.com/Azure/azure-webjobs-sdk/wiki
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[tarification App Service]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[concepts d’authentification]: ../app-service/app-service-authentication-overview.md
[démarrage rapide de l’authentification]: app-service-mobile-auth.md

[Portail Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
