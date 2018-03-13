---
title: "Utiliser le Kit de développement logiciel (SDK) de serveur principal Node.js pour Mobile Apps | Microsoft Docs"
description: "Découvrez comment utiliser le Kit de développement logiciel (SDK) du serveur principal Node.js pour Azure App Service Mobile Apps."
services: app-service\mobile
documentationcenter: 
author: elamalani
manager: elamalani
editor: 
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: bd423d6fb62b2ace16832f665c8834b4aea7e26f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Utiliser le Kit de développement logiciel (SDK) Node.js pour Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Cet article fournit des informations détaillées, ainsi que des exemples sur l’utilisation d’un serveur principal Node.js dans la fonctionnalité Mobile Apps d’Azure App Service.

## <a name="Introduction"></a>Introduction
Mobile Apps offre la possibilité d’ajouter à une application Web une API Web d’accès aux données optimisé pour les plateformes mobiles. Le Kit de développement logiciel (SDK) Mobile Apps est fourni pour les applications Web ASP.NET et Node.js. Le Kit offre les opérations suivantes :

* Opérations de table (read, insert, update, delete) pour l’accès aux données
* Opérations d’API personnalisées

Ces deux types d’opérations fournissent l’authentification sur l’ensemble des fournisseurs d’identité qu’Azure App Service autorise. Ces fournisseurs incluent des fournisseurs d’identité sociale tels que Facebook, Twitter, Google et Microsoft, ainsi qu’Azure Active Directory pour l’identité d’entreprise.

Vous trouverez des exemples de chaque cas de figure dans le [répertoire d’exemples sur GitHub].

## <a name="supported-platforms"></a>Plateformes prises en charge
Le Kit de développement logiciel (SDK) Node.js dans Mobile Apps prend en charge la version actuelle de LTS de Node, ainsi que les versions ultérieures. Actuellement, la dernière version LTS de Node est 4.5.0. Les autres versions de Node peuvent fonctionner, mais elles ne sont pas prises en charge.

Le Kit de développement logiciel (SDK) Node.js pour Mobile Apps prend en charge deux pilotes de base de données : 

* Le pilote node-mssql prend en charge Azure SQL Database et les instances SQL Server locales.  
* Le pilote sqlite3 prend en charge les bases de données SQLite sur une seule instance.

### <a name="howto-cmdline-basicapp"></a>Créer un serveur principal Node.js de base à l’aide de la ligne de commande
Chaque serveur principal Node.js pour Azure Mobile Apps démarre en tant qu’application ExpressJS. ExpressJS est l’infrastructure de service web la plus populaire pour le serveur principal Node.js. Vous pouvez créer une application [Express] de base de la manière suivante :

1. Dans une commande ou une fenêtre PowerShell, créez un répertoire pour votre projet :

        mkdir basicapp
2. Exécutez `npm init` pour initialiser la structure du package :

        cd basicapp
        npm init

   La commande `npm init` pose une série de questions pour initialiser le projet. Consultez l’exemple pour voir le résultat obtenu :

   ![La sortie npm init][0]
3. Installez les bibliothèques `express` et `azure-mobile-apps` à partir du référentiel npm :

        npm install --save express azure-mobile-apps
4. Créez un fichier app.js pour implémenter le serveur mobile de base :

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table.
        mobile.tables.add('TodoItem');

        // Add the Mobile API so it is accessible as a Web API.
        app.use(mobile);

        // Start listening on HTTP.
        app.listen(process.env.PORT || 3000);

Cette application crée une API Web optimisée pour les applications mobiles avec un seul point de terminaison (`/tables/TodoItem`) qui fournit un accès non authentifié à un magasin de données SQL sous-jacent à l’aide d’un schéma dynamique. Cette API est adaptée pour les démarrages rapides de bibliothèques de client suivants :

* [Démarrage rapide de client Android]
* [Démarrage rapide de client Apache Cordova]
* [Démarrage rapide de client iOS]
* [Démarrage rapide de client Windows Store]
* [Démarrage rapide de client Xamarin.iOS]
* [Démarrage rapide de client Xamarin.Android]
* [Démarrage rapide de client Xamarin.Forms]

Vous trouverez le code pour cette application de base dans l’ [exemple basicapp sur GitHub].

### <a name="howto-vs2015-basicapp"></a>Créer un serveur principal Node.js à l’aide de Visual Studio 2015
Visual Studio 2015 requiert une extension pour développer les applications Node.js dans l’IDE. Pour commencer, installez le fichier [Node.js Tools 1.1 pour Visual Studio]. Lorsque vous avez terminé l’installation, créez une application Express 4.x :

1. Ouvrez la boîte de dialogue **Nouveau projet** (dans le menu **Fichier** > **Nouveau** > **Projet**).
2. Développez **Modèles** > **JavaScript** > **Node.js**.
3. Sélectionnez **Basic Azure Node.js Express 4 Application**.
4. Renseignez le nom du projet. Sélectionnez **OK**.

   ![Visual Studio 2015 Nouveau projet][1]
5. Cliquez avec le bouton droit de la souris sur le nœud **npm** et sélectionnez **Installer de nouveaux packages npm**.
6. Vous devrez peut-être actualiser le catalogue npm après la création de votre première application Node.js. Sélectionnez **Actualiser** si nécessaire.
7. Dans la zone de recherche, entrez **azure-mobile-apps** . Sélectionnez le package **azure-mobile-apps 2.0.0**, puis sélectionnez **Installer le package**.

   ![Installer de nouveaux packages npm][2]
8. Sélectionnez **Fermer**.
9. Ouvrez le fichier app.js pour ajouter la prise en charge du Kit de développement logiciel (SDK) Mobile Apps. À la ligne 6 des instructions `require` de la bibliothèque, ajoutez le code suivant :

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

   Environ à la ligne 27, après les autres instructions `app.use`, ajoutez le code suivant :

        app.use('/users', users);

        // Mobile Apps initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

   Enregistrez le fichier .
10. Exécutez l’application en local (l’API est disponible sur http://localhost:3000) ou publiez-la dans Azure.

### <a name="create-node-backend-portal"></a>Créer un serveur principal Node.js à l’aide du portail Azure
Vous pouvez créer un serveur principal Mobile Apps dans le [portail Azure]. Vous pouvez suivre la procédure ci-dessous, ou créer simultanément un client et un serveur en suivant le didacticiel [Créer une application mobile](app-service-mobile-ios-get-started.md). Ce didacticiel contient une version simplifiée de ces instructions et convient mieux aux projets de preuve de concept.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Dans le volet **Prise en main**, sous **Create a table API** (Créer une API de table), sélectionnez **Node.js** en tant que langue de serveur principal.
Cochez la case **Je reconnais que cette opération va remplacer tout le contenu du site.**, puis sélectionnez sur **Créer une table TodoItem**.

### <a name="download-quickstart"></a>Télécharger le projet de code de démarrage rapide du serveur principal Node.js à l’aide de Git
Lorsque vous créez un serveur principal Node.js pour Mobile Apps à l’aide du volet **Démarrage rapide** du portail, un projet Node.js est créé et déployé sur votre site. Dans le portail, vous pouvez ajouter des tables et des API et modifier les fichiers de code pour le serveur principal Node.js. Vous pouvez également utiliser l’un des divers outils de déploiement pour télécharger le projet de serveur principal, afin de pouvoir ajouter ou modifier des tables et des API, avant de publier à nouveau le projet. Pour plus d’informations, consultez le guide [ guide Déploiement Git local vers Azure App Service]. 

La procédure suivante utilise un référentiel Git pour télécharger le code de projet de démarrage rapide :

1. Si vous ne l’avez pas déjà fait, installez Git. La procédure requise pour installer Git diffère selon les systèmes d'exploitation. Consultez la rubrique [Installing Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) (Installation de Git) pour accéder aux distributions et consignes d’installation propres aux différents systèmes d’exploitation.
2. Suivez la procédure indiquée dans [Étape 3 : activation du référentiel de l’application App Service](../app-service/app-service-deploy-local-git.md#Step3) pour activer le référentiel Git pour votre site de serveur principal. Notez le nom d’utilisateur et le mot de passe associés au déploiement.
3. Dans le volet de votre serveur principal Mobile Apps, prenez note du paramètre **URL de clone Git**.
4. Exécutez la commande `git clone` à l’aide de l’URL de clone Git. Entrez votre mot de passe lorsque vous y êtes invité, comme dans l’exemple suivant :

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git
5. Accédez au répertoire local (`/todolist` dans l’exemple précédent). Vous remarquerez que les fichiers de projet ont été téléchargés. Recherchez le fichier todoitem.json dans le répertoire `/tables`. Ce fichier définit les autorisations sur la table. Recherchez également le fichier todoitem.js dans le même répertoire. Il définit les scripts d’opération CRUD pour la table.
6. Après avoir apporté vos modifications aux fichiers de projet, exécutez les commandes suivantes pour ajouter, valider, puis charger les modifications sur le site :

        $ git commit -m "updated the table script"
        $ git push origin master

   Lorsque vous ajoutez de nouveaux fichiers au projet, vous devez tout d’abord exécuter la commande `git add .`.

Le site est republié chaque fois qu’un nouvel ensemble de validations est transmis au site.

### <a name="howto-publish-to-azure"></a>Publier votre serveur principal Node.js dans Azure
Microsoft Azure propose plusieurs mécanismes permettant de publier votre serveur principal Node.js pour Mobile Apps sur le service Azure. Ces mécanismes incluent des outils de déploiement intégrés à Visual Studio, des outils de ligne de commande et des options de déploiement continu en fonction du contrôle de la source. Pour plus d’informations, consultez le [ guide Déploiement Git local vers Azure App Service].

Avant de publier le serveur principal, vous devriez prendre connaissance des recommandations suivantes pour l’application Node.js avec Azure App Service :

* Comment [spécifier la version de Node]
* Comment [utiliser les modules Node]

### <a name="howto-enable-homepage"></a>Activer une page d’accueil pour votre application
De nombreuses applications sont une combinaison d’applications Web et mobiles. Vous pouvez utiliser l’infrastructure ExpressJS pour combiner les deux facettes. Néanmoins, il peut être parfois préférable d’implémenter uniquement une interface mobile. Il est utile de fournir une page d’accueil pour obtenir la garantie que le service d’application fonctionne correctement. Vous pouvez soit fournir votre propre page d’accueil, soit activer une page d’accueil temporaire. Pour activer une page d’accueil temporaire, utilisez ce qui suit pour instancier Mobile Apps :

    var mobile = azureMobileApps({ homePage: true });

Si vous voulez que cette option soit disponible uniquement pour un développement en local, vous pouvez ajouter ce paramètre à votre fichier azureMobile.js.

## <a name="TableOperations"></a>Opérations de table
Le Kit de développement logiciel (SDK) Node.js Server azure-mobile-apps fournit des mécanismes permettant d’exposer les tables de données stockées dans Azure SQL Database sous la forme d’une API Web. Il fournit cinq opérations :

| Opération | Description |
| --- | --- |
| GET /tables/*tablename* |Extraire tous les enregistrements de la table. |
| GET /tables/*tablename*/:id |Extraire un enregistrement spécifique de la table. |
| POST /tables/*tablename* |Créer un enregistrement dans la table. |
| PATCH /tables/*tablename*/:id |Mettre à jour un enregistrement dans la table. |
| DELETE /tables/*tablename*/:id |Supprimer un enregistrement de la table. |

Cette API Web prend en charge [OData] et étend le schéma de table pour prendre en charge [la synchronisation des données hors connexion].

### <a name="howto-dynamicschema"></a>Définir des tables à l’aide d’un schéma dynamique
Avant de pouvoir utiliser une table, vous devez la définir. Vous pouvez définir les tables avec un schéma statique (dans lequel vous définissez les colonnes du schéma) ou dynamique (dans lequel le Kit de développement logiciel (SDK) contrôle le schéma en fonction des demandes entrantes). En outre, vous pouvez contrôler des aspects spécifiques de l’API Web en ajoutant du code Javascript à la définition.

Il est recommandé de définir chaque table dans un fichier Javascript du répertoire `tables`, puis d’utiliser la méthode `tables.import()` pour importer les tables. En étendant l’exemple d’application de base, vous pourrez ajuster le fichier app.js :

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed.
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined.
    mobile.tables.initialize().then(function () {
        // Add the Mobile API so it is accessible as a Web API.
        app.use(mobile);

        // Start listening on HTTP.
        app.listen(process.env.PORT || 3000);
    });

Définissez la table dans ./tables/TodoItem.js :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here.

    module.exports = table;

Par défaut, les tables utilisent un schéma dynamique. Pour désactiver globalement le schéma dynamique, définissez le paramètre d’application `MS_DynamicSchema` sur false dans le portail Azure.

Vous en trouverez un exemple complet dans l’ [exemple todo sur GitHub].

### <a name="howto-staticschema"></a>Définir des tables à l’aide d’un schéma statique
Vous pouvez définir explicitement les colonnes à exposer via l’API Web. Le Kit de développement logiciel (SDK) Node.js azure-mobile-apps ajoute automatiquement à la liste que vous fournissez toutes les colonnes supplémentaires requises pour la synchronisation des données hors connexion. Par exemple, les applications clientes de démarrage rapide requièrent une table à deux colonnes : une colonne `text` (une chaîne) et une colonne `complete` (une valeur booléenne).  
Vous pouvez définir ces colonnes dans le fichier JavaScript de définition de table (situé dans le répertoire `tables`) comme suit :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    module.exports = table;

Si vous définissez des tables de manière statique, vous devez également appeler la méthode `tables.initialize()` pour créer le schéma de base de données au démarrage. La méthode `tables.initialize()` renvoie un objet [promise] qui permet de s’assurer que le service web ne traite pas les requêtes avant l’initialisation de la base de données.

### <a name="howto-sqlexpress-setup"></a>Utiliser SQL Server Express comme magasin de données de développement sur votre ordinateur local
Le Kit de développement logiciel (SDK) Node.js dans Mobile Apps fournit trois options pour traiter les données prêtes à l’emploi :

* Utilisez le pilote **memory** pour fournir un exemple de magasin non persistant.
* Utilisez le pilote **mssql** pour fournir un magasin de données SQL Express utilisé pour le développement.
* Utilisez le pilote **mssql** pour fournir un magasin de données Azure SQL Database utilisé pour la production.

Le Kit de développement logiciel (SDK) Node.js pour Mobile Apps utilise le [package mssql Node.js] pour établir et utiliser une connexion à SQL Server Express et à SQL Database. Ce package nécessite l’activation de connexions TCP sur votre instance SQL Server Express.

> [!TIP]
> Le pilote memory ne fournit pas un ensemble complet de fonctionnalités à des fins de test. Si vous souhaitez tester localement votre serveur principal, nous vous recommandons d’utiliser un magasin de données SQL Server Express et le pilote mssql.
>
>

1. Téléchargez et installez [Microsoft SQL Server 2014 Express]. Veillez à bien installer SQL Server 2014 Express avec l’édition Tools. À moins que vous ayez explicitement besoin d’une prise en charge 64 bits, la version 32 bits consomme moins de mémoire lors de son exécution.
2. Exécutez le Gestionnaire de configuration de SQL Server 2014 :

   a. Développez le nœud **Configuration du réseau SQL Server** dans le menu de l’arborescence.

   b. Sélectionnez **Protocols for SQLEXPRESS** (Protocoles pour SQLEXPRESS).

   c. Cliquez avec le bouton droit sur **TCP/IP**, puis sélectionnez **Enable**. Sélectionnez **OK** dans la boîte de dialogue contextuelle.

   d. Cliquez avec le bouton droit sur **TCP/IP**, puis sélectionnez **Properties**.

   e. Sélectionnez l’onglet **Adresses IP**.

   f. Recherchez le nœud **IPAll** . Dans le champ **TCP Port**, entrez **1433**.

      ![Configurer SQL Server Express pour TCP/IP][3]

   g. Sélectionnez **OK**. Sélectionnez **OK** dans la boîte de dialogue contextuelle.

   h. Sélectionnez **SQL Server Services** dans le menu de l’arborescence.

   i. Cliquez avec le bouton droit sur **SQL Server (SQLEXPRESS)**, puis sélectionnez **Redémarrer**.

   j. Fermez le Gestionnaire de configuration de SQL Server 2014.
3. Exécutez l’instance SQL Server 2014 Management Studio et connectez-vous à votre instance locale de SQL Server Express :

   1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur votre instance et sélectionnez **Propriétés**.
   2. Sélectionnez la page **Security** .
   3. Assurez-vous que le **mode d’authentification SQL Server et Windows** est bien sélectionné.
   4. Sélectionnez **OK**.

      ![Configurer l’authentification SQL Server Express][4]
   5. Développez **Sécurité** > **Connexions** dans l’Explorateur d’objets.
   6. Cliquez avec le bouton droit sur **Connexions** et sélectionnez **Nouvelle connexion**.
   7. Entrez un nom de connexion. Sélectionnez **Authentification SQL Server**. Entrez un mot de passe, puis saisissez le même mot de passe dans le champ **Confirmer le mot de passe**. Le mot de passe doit répondre aux exigences de complexité de Windows.
   8. Sélectionnez **OK**.

      ![Ajouter un utilisateur à SQL Server Express][5]
   9. Cliquez avec le bouton droit sur votre nouveau compte de connexion et sélectionnez **Propriétés**.
   10. Sélectionnez la page **Rôles de serveurs**.
   11. Sélectionnez la case à cocher pour le rôle serveur **dbcreator**.
   12. Sélectionnez **OK**.
   13. Fermez SQL Server 2015 Management Studio.

Veillez à enregistrer les nom d’utilisateur et mot de passe que vous avez sélectionnés. Vous devrez peut-être affecter des rôles serveur ou autorisations supplémentaires selon les besoins de votre base de données.

L’application Node.js lit la variable d’environnement `SQLCONNSTR_MS_TableConnectionString` pour la chaîne de connexion de cette base de données. Vous pouvez définir cette variable dans votre environnement. Par exemple, vous pouvez utiliser PowerShell pour définir cette variable d’environnement :

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Accédez à la base de données via une connexion TCP/IP. Fournissez un nom d’utilisateur et un mot de passe pour la connexion.

### <a name="howto-config-localdev"></a>Configurer votre projet pour un développement local
Mobile Apps lit un fichier JavaScript appelé *azureMobile.js* à partir du système de fichiers local. N’utilisez pas ce fichier pour configurer le Kit de développement logiciel (SDK) Mobile Apps en production. Utilisez plutôt **Paramètres de l’application** dans le [portail Azure]. 

Le fichier azureMobile.js doit exporter un objet de configuration. Les paramètres les plus courants sont les suivants :

* Paramètres de base de données
* Paramètres de journalisation des diagnostics
* Paramètres CORS de remplacement

Cet exemple de fichier azureMobile.js implémente les paramètres de base de données précédents :

    module.exports = {
        cors: {
            origins: [ 'localhost' ]
        },
        data: {
            provider: 'mssql',
            server: '127.0.0.1',
            database: 'mytestdatabase',
            user: 'azuremobile',
            password: 'T3stPa55word'
        },
        logging: {
            level: 'verbose'
        }
    };

Nous vous recommandons d’ajouter azureMobile.js à votre fichier .gitignore (ou un autre fichier ignore de contrôle du code source) pour éviter que les mots de passe soient stockés dans le cloud. Veillez à toujours configurer les paramètres de production dans **Paramètres de l’application** du [portail Azure].

### <a name="howto-appsettings"></a>Configurer des paramètres d’application pour votre application mobile
La plupart des paramètres du fichier azureMobile.js ont un paramètre équivalent dans le [portail Azure]. Utilisez la liste suivante pour configurer votre application dans **Paramètres de l’application** :

| Paramètre d’application | Paramètre azureMobile.js | Description | Valeurs valides |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |Nom |Le nom de l’application |chaîne |
| **MS_MobileLoggingLevel** |logging.level |Niveau minimal de journal pour les messages à consigner |error, warning, info, verbose, debug, silly |
| **MS_DebugMode** |debug |Active ou désactive le mode de débogage |true, false |
| **MS_TableSchema** |data.schema |Nom de schéma par défaut pour les tables SQL |string (valeur par défaut : dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Active ou désactive le mode de débogage |true, false |
| **MS_DisableVersionHeader** |version (sur Undefined) |Désactive l'en-tête X-ZUMO-Server-Version |true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Désactive la vérification de la version de l’API client |true, false |

Pour définir un paramètre d’application :

1. Connectez-vous au [portail Azure].
2. Sélectionnez **Toutes les ressources** ou **App Services**, puis sélectionnez le nom de votre application mobile.
3. Le volet **Paramètres** s’ouvre par défaut. Si ce n’est pas le cas, sélectionnez **Paramètres**.
4. Dans le menu **GÉNÉRAL**, sélectionnez **Paramètres de l’application**.
5. Accédez à la section **Paramètres de l’application**.
6. Si votre paramètre d’application existe déjà, sélectionnez la valeur correspondante pour la modifier.
   Si votre paramètre d’application n’existe pas, saisissez-le dans la zone **Clé** et inscrivez la valeur dans la zone **Valeur**.
8. Sélectionnez **Enregistrer**.

La modification de la plupart des paramètres requiert le redémarrage du service.

### <a name="howto-use-sqlazure"></a>Utiliser SQL Database comme magasin de données de production
<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

L’utilisation de la base de données SQL Azure en tant que datastore est identique pour tous les types d’applications Azure App Service. Si vous ne l’avez pas déjà fait, suivez ces étapes pour créer un serveur principal Mobile Apps :

1. Connectez-vous au [portail Azure].
2. Dans le coin supérieur gauche de la fenêtre, sélectionnez le bouton **+ NOUVEAU** > **Web + Mobile**>**Application mobile**, puis indiquez un nom pour votre serveur principal Mobile Apps.
3. Dans la zone **Groupe de ressources** , entrez le même nom que votre application.
4. Le plan App Service par défaut est sélectionné. Si vous souhaitez modifier votre plan App Service :

   a. Sélectionnez **Plan App Service** > **+Créer**. 
   
   b. Indiquez le nom du nouveau plan App Service et sélectionnez un emplacement approprié. 
   
   c. Sélectionnez un niveau tarifaire approprié pour le service. Sélectionnez **Afficher tout** pour afficher davantage d’options de tarification, telles que **Gratuit** et **Partagé**. 
   
   d. Cliquez sur le bouton **Sélectionner**. 
   
   e. Retournez dans le volet **Plan App Service**, puis sélectionnez **OK**.
5. Sélectionnez **Créer**. 

La configuration d’un serveur principal Mobile Apps peut prendre quelques minutes. Une fois le serveur principal Mobile Apps configuré, le portail ouvre le volet **Paramètres** correspondant au serveur principal Mobile Apps.

Vous pouvez choisir de connecter une base de données SQL existante à votre serveur principal d’application mobile ou de créer une base de données SQL. Dans cette section, nous créons une base de données SQL.

> [!NOTE]
> Si vous avez déjà une base de données dans le même emplacement que le serveur principal Mobile Apps, vous pouvez sélectionner **Utiliser une base de données existante**, puis cette base de données. Nous vous déconseillons d’utiliser une base de données dans un autre emplacement en raison de latences plus importantes.
>
>

1. Dans le nouveau serveur principal Mobile Apps, sélectionnez **Paramètres** > **Application mobile** > **Données** > **+Ajouter**.
2. Dans le volet **Ajouter une connexion de données**, sélectionnez **Base de données SQL - Configurer les paramètres requis** > **Créer une base de données**. Entrez le nom de la nouvelle base de données dans le champ **Nom**.
3. Sélectionnez **Serveur**. Dans le volet **Nouveau serveur**, entrez un nom de serveur unique dans le champ **Nom du serveur**, et indiquez un nom de connexion d’administration serveur et un mot de passe appropriés. Vérifiez que l’option **Autoriser les services Azure à accéder au serveur** est sélectionnée. Sélectionnez **OK**.

   ![Création d’une base de données SQL Azure][6]
4. Dans le volet **Nouvelle base de données**, sélectionnez **OK**.
5. Dans le volet **Ajouter une connexion de données**, sélectionnez **Chaîne de connexion**, entrez le nom de connexion et le mot de passe que vous avez indiqués lors de la création de la base de données. Si vous utilisez une base de données existante, indiquez les informations d’identification de connexion à celle-ci. Sélectionnez **OK**.
6. Dans le volet **Ajouter une connexion de données**, sélectionnez **OK** pour créer la base de données.

<!--- END OF ALTERNATE INCLUDE -->

La création de la base de données prend quelques minutes. Utilisez la zone **Notifications** pour surveiller la progression du déploiement. Attendez la fin du déploiement de la base de données avant de continuer. Une fois le déploiement effectué, une chaîne de connexion est créée pour l’instance SQL Database dans les paramètres d’application de votre serveur principal Mobile Apps. Vous pouvez voir ce paramètre d’application dans **Paramètres** > **Paramètres de l’application** > **Chaînes de connexion**.

### <a name="howto-tables-auth"></a>Exiger une authentification pour l’accès aux tables
Si vous souhaitez utiliser l’authentification App Service avec le point de terminaison `tables`, vous devez d’abord configurer l’authentification App Service dans le [portail Azure]. Pour plus d’informations, consultez le guide de configuration correspondant au fournisseur d’identité que vous souhaitez utiliser :

* [Configurer votre application App Service pour utiliser une connexion Azure Active Directory]
* [Comment configurer votre application App Service de manière à utiliser la connexion via Facebook]
* [Comment configurer votre application App Service de manière à utiliser la connexion via Google]
* [Comment configurer votre application App Service pour utiliser une connexion par compte Microsoft]
* [Comment configurer votre application App Service de manière à utiliser la connexion via Twitter]

Chaque table possède une propriété d’accès que vous pouvez utiliser pour contrôler l’accès à la table. L’exemple suivant illustre une table définie de façon statique avec l’authentification requise.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

La propriété d’accès peut prendre trois valeurs :

* *anonymous* indique que l’application cliente est autorisée à lire les données sans authentification.
* *authenticated* indique que l’application cliente doit envoyer un jeton d’authentification valide avec la requête.
* *disabled* indique que cette table est actuellement désactivée.

Si la propriété d’accès n’est pas définie, l’accès non authentifié est autorisé.

### <a name="howto-tables-getidentity"></a>Utiliser des revendications d’authentification avec vos tables
Vous pouvez configurer différentes revendications qui sont demandées lors de la configuration de l'authentification. Ces revendications ne sont normalement pas disponibles via l’objet `context.user` . Toutefois, vous pouvez les récupérer à l’aide de la méthode `context.user.getIdentity()`. La méthode `getIdentity()` renvoie une promesse qui correspond à un objet. L’objet est indexé par la méthode d’authentification (`facebook`, `google`, `twitter`, `microsoftaccount` ou `aad`).

Par exemple, si vous configurez l’authentification de compte Microsoft et demandez la revendication des adresses de messagerie, vous pouvez ajouter l’adresse de messagerie à l’enregistrement avec le contrôleur de table suivant :

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition.
    var table = azureMobileApps.table();

    table.columns = {
        "emailAddress": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;
    table.access = 'authenticated';

    /**
    * Limit the context query to those records with the authenticated user email address
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function queryContextForEmail(context) {
        return context.user.getIdentity().then((data) => {
            context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
            return context.execute();
        });
    }

    /**
    * Adds the email address from the claims to the context item - used for
    * insert operations
    * @param {Context} context the operation context
    * @returns {Promise} context execution Promise
    */
    function addEmailToContext(context) {
        return context.user.getIdentity().then((data) => {
            context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
            return context.execute();
        });
    }

    // Configure specific code when the client does a request.
    // READ: only return records that belong to the authenticated user.
    table.read(queryContextForEmail);

    // CREATE: add or overwrite the userId based on the authenticated user.
    table.insert(addEmailToContext);

    // UPDATE: only allow updating of records that belong to the authenticated user.
    table.update(queryContextForEmail);

    // DELETE: only allow deletion of records that belong to the authenticated user.
    table.delete(queryContextForEmail);

    module.exports = table;

Pour voir les revendications disponibles, utilisez un navigateur web pour afficher le point de terminaison `/.auth/me` de votre site.

### <a name="howto-tables-disabled"></a>Désactiver l’accès à des opérations de table spécifiques
En plus d’apparaître sur la table, la propriété d’accès peut être utilisée pour contrôler des opérations spécifiques. Il existe quatre opérations :

* `read` est l’opération GET RESTful sur la table.
* `insert` est l’opération POST RESTful sur la table.
* `update` est l’opération PATCH RESTful sur la table.
* `delete` est l’opération DELETE RESTful sur la table.

Vous pouvez, par exemple, souhaiter fournir une table non authentifiée en lecture seule :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-only table. Only allow READ operations.
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Ajuster la requête utilisée avec les opérations de table
On attend souvent des opérations de table qu’elles soient capables de fournir une vue restreinte des données. Par exemple, vous pouvez fournir une table marquée avec l’ID de l’utilisateur authentifié, vous permettant uniquement de lire ou de mettre à jour vos propres enregistrements. La définition de table suivante offre cette fonctionnalité :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table.
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table.
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved.
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user.
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Les opérations qui exécutent normalement une requête ont une propriété de requête que vous pouvez ajuster avec une clause `where`. La propriété de requête est un objet [QueryJS] utilisé pour convertir une requête OData en un élément que le serveur principal pourra traiter. Pour les cas d’égalité simple (comme dans l’exemple précédent), vous pouvez utiliser un mappage. Vous pouvez également ajouter des clauses SQL spécifiques :

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Configurer une suppression réversible sur une table
Une suppression réversible ne supprime pas réellement les enregistrements. Elle les marque comme étant supprimés dans la base de données en définissant la colonne supprimée sur true. Le Kit de développement logiciel (SDK) Mobile Apps supprime automatiquement de manière réversible des résultats les enregistrements, sauf si le Kit de développement logiciel (SDK) utilise `IncludeDeleted()`. Pour configurer une table pour une suppression réversible, définissez la propriété `softDelete` dans le fichier de définition de table :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Turn on soft delete.
    table.softDelete = true;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

Vous devez établir un mécanisme pour supprimer les enregistrements : à partir d’une application cliente, via un WebJob, une fonction Azure ou une API personnalisée.

### <a name="howto-tables-seeding"></a>Alimenter votre base de données
Lorsque vous créez une application, vous souhaiterez sans doute alimenter une table avec des données. Pour ce faire, vous pouvez utiliser le fichier JavaScript de définition de table, comme indiqué ci-dessous :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table.
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off the dynamic schema.
    table.dynamicSchema = false;

    // Require authentication to access the table.
    table.access = 'authenticated';

    module.exports = table;

L’alimentation en données se produit uniquement lorsque vous avez utilisé le Kit de développement logiciel (SDK) Mobile Apps pour créer la table. Si la table existe déjà dans la base de données, aucune donnée ne sera injectée dans la table. Si le schéma dynamique est activé, alors le schéma est déduit des données alimentées.

Nous vous recommandons d’appeler explicitement la méthode `tables.initialize()` pour créer la table au début de l’exécution du service.

### <a name="Swagger"></a>Activer la prise en charge de Swagger
Mobile Apps prend nativement en charge l’interface [Swagger]. Pour activer la prise en charge de Swagger, commencez par installer l’interface utilisateur Swagger en tant que dépendance :

    npm install --save swagger-ui

Vous pouvez ensuite activer la prise en charge de Swagger dans le constructeur Mobile Apps :

    var mobile = azureMobileApps({ swagger: true });

Vous préférerez probablement activer la prise en charge de Swagger uniquement dans les éditions de développement. Pour ce faire, vous pouvez utiliser le paramètre d’application `NODE_ENV` :

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

Le point de terminaison `swagger` se trouve à l’adresse http://*VotreSite*.azurewebsites.net/swagger. Vous pouvez accéder à l’interface utilisateur Swagger via le point de terminaison `/swagger/ui`. Si vous décidez de demander une authentification sur l’ensemble de votre application, Swagger génère une erreur. Pour obtenir de meilleurs résultats, autorisez les requêtes non authentifiées via les paramètres d’authentification et d’autorisation d’Azure App Service. Vous pouvez ensuite contrôler l’authentification en utilisant la propriété `table.access`.

Vous pouvez également ajouter l’option Swagger à votre fichier azureMobile.js si vous voulez activer la prise en charge de Swagger uniquement dans le cadre d’un développement en local.

## <a name="a-namepushpush-notifications"></a><a name="push">Notifications Push
Mobile Apps s’intègre à Azure Notification Hubs pour vous permettre d’envoyer des notifications Push ciblées à des millions d’appareils utilisant toutes les plateformes les plus populaires. Notification Hubs vous permet d’envoyer des notifications Push aux appareils iOS, Android et Windows. Pour plus d’informations sur ce que Notification Hubs vous permet de faire, consultez [Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Envoyer des notifications Push
Le code suivant vous montre comment utiliser l’objet `push` pour envoyer une notification Push de diffusion à des appareils iOS inscrits :

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured.
    if (context.push) {
        // Send a push notification by using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

En créant un modèle d’inscription Push à partir du client, vous pouvez simplement envoyer un modèle de message Push à des appareils sur toutes les plateformes prises en charge. Le code suivant montre comment envoyer un modèle de notification :

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured.
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


### <a name="push-user"></a>Envoyer des notifications Push à un utilisateur authentifié à l’aide de balises
Quand un utilisateur authentifié s’inscrit aux notifications Push, une balise avec l’ID d’utilisateur est automatiquement ajoutée à l’inscription. Grâce à cette balise, vous pouvez envoyer des notifications Push à tous les appareils inscrits par un utilisateur spécifique. Le code suivant permet d’obtenir le SID de l’utilisateur qui émet la requête et d’envoyer un modèle de notification Push à chaque inscription d’appareil pour cet utilisateur :

    // Only do the push if configured.
    if (context.push) {
        // Send a notification to the current user.
        context.push.send(context.user.id, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

Quand vous vous inscrivez à des notifications Push à partir d’un client authentifié, assurez-vous au préalable que l’authentification est bien terminée.

## <a name="CustomAPI"></a> API personnalisées
### <a name="howto-customapi-basic"></a>Définir une API personnalisée
Outre l’API d’accès aux données via le point de terminaison `/tables`, Mobile Apps peut fournir une couverture d’API personnalisée. Les API personnalisées sont définies de manière similaire aux définitions de table et sont accessibles à toutes les mêmes fonctionnalités, y compris l’authentification.

Si vous souhaitez utiliser l’authentification App Service avec une API personnalisée, vous devez d’abord configurer l’authentification App Service dans le [portail Azure]. Pour plus d’informations, consultez le guide de configuration correspondant au fournisseur d’identité que vous souhaitez utiliser :

* [Configurer votre application App Service pour utiliser une connexion Azure Active Directory]
* [Comment configurer votre application App Service de manière à utiliser la connexion via Facebook]
* [Comment configurer votre application App Service de manière à utiliser la connexion via Google]
* [Comment configurer votre application App Service pour utiliser une connexion par compte Microsoft]
* [Comment configurer votre application App Service de manière à utiliser la connexion via Twitter]

La définition des API personnalisées est largement similaire à celle des API de tables :

1. Créez un répertoire `api`.
2. Créez un fichier JavaScript de définition d’API dans le répertoire `api`.
3. Utilisez la méthode import pour importer le répertoire `api`.

Voici le prototype de définition d’API dérivé de l’exemple d’application de base utilisé précédemment :

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the custom API.
    mobile.api.import('./api');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Prenons un exemple d’API qui renvoie la date du serveur à l’aide de la méthode `Date.now()`. Voici le fichier api/date.js :

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Chaque paramètre correspond à l’un des verbes RESTful standard : GET, POST, PATCH ou DELETE. La méthode est une fonction [ExpressJS middleware] standard qui envoie la sortie requise.

### <a name="howto-customapi-auth"></a>Exiger une authentification pour l’accès à une API personnalisée
Le Kit de développement logiciel (SDK) Mobile Apps implémente l’authentification de la même façon pour le point de terminaison `tables` et pour les API personnalisées. Pour ajouter l’authentification à l’API développée dans la section précédente, ajoutez une propriété `access` :

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };
    // All methods must be authenticated.
    api.access = 'authenticated';

    module.exports = api;

Vous pouvez également spécifier l’authentification sur des opérations spécifiques :

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        }
    };
    // The GET methods must be authenticated.
    api.get.access = 'authenticated';

    module.exports = api;

Pour les API personnalisées qui requièrent une authentification, vous devez utiliser le même jeton que celui utilisé pour le point de terminaison `tables`.

### <a name="howto-customapi-auth"></a>Gérer des chargements de fichiers volumineux
Le Kit de développement logiciel (SDK) Mobile Apps utilise [body-parser middleware](https://github.com/expressjs/body-parser) pour accepter et décoder le contenu du corps de votre demande. Vous pouvez préconfigurer body-parser pour accepter les chargements de fichiers plus volumineux :

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling.
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the custom API.
    mobile.api.import('./api');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);

Le fichier est codé en base 64 avant la transmission. Cet encodage augmente la taille du chargement réel (ainsi que la taille dont vous devez tenir compte).

### <a name="howto-customapi-sql"></a>Exécuter des instructions SQL personnalisées
Le Kit de développement logiciel (SDK) Mobile Apps autorise l’accès à l’ensemble du contexte via l’objet de requête. Vous pouvez facilement exécuter des instructions SQL paramétrées pour le fournisseur de données défini :

    var api = {
        get: function (request, response, next) {
            // Check for parameters. If not there, pass on to a later API call.
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query. Anything that the mssql
            // driver can handle is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query. The context for Mobile Apps is available through
            // request.azureMobile. The data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Débogage, Tables facile et API simples
### <a name="howto-diagnostic-logs"></a>Déboguer, diagnostiquer et résoudre des problèmes sur Mobile Apps
Azure App Service fournit plusieurs techniques de débogage et de résolution des problèmes pour les applications Node.js.
Consultez les articles suivants pour prendre en main la résolution des problèmes de votre serveur principal Node.js dans Mobile Apps :

* [Surveillance des applications dans Azure App Service]
* [Activer la journalisation des diagnostics pour les applications web dans Azure App Service]
* [Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio]

Les applications Node.js ont accès à un large éventail d’outils de journaux de diagnostic. En interne, le Kit de développement logiciel (SDK) Node.js dans Azure Mobile Apps utilise [Winston] pour la journalisation des diagnostics. La journalisation est activée automatiquement lorsque vous activez le mode de débogage ou définissez le paramètre d’application `MS_DebugMode` sur true dans le [portail Azure]. Les journaux générés s’affichent dans les journaux de diagnostic sur le [portail Azure].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Utiliser l’outil Tables faciles dans le portail Azure
Vous pouvez faire appel à l’outil Tables faciles pour créer et utiliser des tables directement dans le portail. Vous pouvez télécharger le jeu de données vers Tables faciles au format CSV. Notez que vous ne pouvez pas utiliser de noms de propriétés (dans votre jeu de données CSV) qui sont en conflit avec des noms de propriétés système du serveur principal Mobile Apps. Les noms de propriétés système sont :
* createdAt
* updatedAt
* deleted
* version

Vous pouvez même modifier les opérations de table à l’aide de l’éditeur App Service. Lorsque vous cliquez sur **Tables faciles** dans vos paramètres de site principal, vous pouvez ajouter, modifier ou supprimer une table. Vous pouvez également voir les données de la table.

![Utilisation de l’outil Easy Tables](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Les commandes suivantes sont disponibles dans la barre de commandes d’une table :

* **Modifier les autorisations** : modifiez l’autorisation pour les opérations de lecture, d’insertion, de mise à jour et de suppression sur la table.
 Vous avez la possibilité d’autoriser l’accès anonyme, d’exiger une authentification ou de désactiver tous les accès à l’opération.
* **Modifier le script** : le fichier de script de la table est ouvert dans l’éditeur App Service.
* **Gérer un schéma** : ajoutez ou supprimez des colonnes ou modifiez l’index de la table.
* **Effacer la table** : tronquez une table existante en supprimant toutes les lignes de données tout en conservant le schéma à l’identique.
* **Supprimer des lignes** : supprimez des lignes de données spécifiques.
* **Afficher les journaux de diffusion en continu** : connectez-vous au service de journaux de diffusion en continu de votre site.

### <a name="work-easy-apis"></a>Utiliser l’outil API faciles dans le portail Azure
Vous pouvez faire appel à l’outil API faciles pour créer et utiliser des API personnalisées directement dans le portail. Vous pouvez modifier des scripts API à l’aide de l’éditeur App Service.

Lorsque vous sélectionnez **API faciles** dans vos paramètres de site principal, vous pouvez ajouter, modifier ou supprimer un point de terminaison d’API personnalisée.

![Utilisation de l’outil Easy APIs](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

Dans le portail, vous pouvez modifier les autorisations d’accès pour une action HTTP, modifier le fichier de script d’API dans l’éditeur App Service ou afficher les journaux de diffusion en continu.

### <a name="online-editor"></a>Modifier le code dans l’éditeur App Service
Le portail Azure vous permet de modifier les fichiers de script de votre serveur principal Node.js dans l’éditeur App Service sans avoir à télécharger le projet sur votre ordinateur local. Pour modifier les fichiers de script dans l’éditeur en ligne :

1. Dans le volet de votre serveur principal Mobile Apps, sélectionnez **All settings** (Tous les paramètres) > **Tables faciles** ou **API faciles**. Sélectionnez une table ou une API, puis **Modifier le script**. Le fichier de script s’ouvre dans l’éditeur App Service.

   ![Éditeur App Service](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
2. Apportez vos modifications au fichier de code dans l’éditeur en ligne. Les modifications sont enregistrées automatiquement au fil de la saisie.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Démarrage rapide de client Android]: app-service-mobile-android-get-started.md
[Démarrage rapide de client Apache Cordova]: app-service-mobile-cordova-get-started.md
[Démarrage rapide de client iOS]: app-service-mobile-ios-get-started.md
[Démarrage rapide de client Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Démarrage rapide de client Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Démarrage rapide de client Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Démarrage rapide de client Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[la synchronisation des données hors connexion]: app-service-mobile-offline-data-sync.md
[Configurer votre application App Service pour utiliser une connexion Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Comment configurer votre application App Service de manière à utiliser la connexion via Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Comment configurer votre application App Service de manière à utiliser la connexion via Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Comment configurer votre application App Service pour utiliser une connexion par compte Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Comment configurer votre application App Service de manière à utiliser la connexion via Twitter]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[ guide Déploiement Git local vers Azure App Service]: ../app-service/app-service-deploy-local-git.md
[Surveillance des applications dans Azure App Service]: ../app-service/web-sites-monitor.md
[Activer la journalisation des diagnostics pour les applications web dans Azure App Service]: ../app-service/web-sites-enable-diagnostic-log.md
[Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
[spécifier la version de Node]: ../nodejs-specify-node-version-azure-apps.md
[utiliser les modules Node]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: http://expressjs.com/
[Swagger]: http://swagger.io/

[portail Azure]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[exemple basicapp sur GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[exemple todo sur GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[répertoire d’exemples sur GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 pour Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[package mssql Node.js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
