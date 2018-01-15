---
title: "Utiliser le Kit de développement logiciel (SDK) de serveur principal Node.js pour Azure Mobile Apps | Microsoft Docs"
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
ms.openlocfilehash: 336da28bea7de313bced97e447fc6b7b1fb1390d
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="how-to-use-the-azure-mobile-apps-nodejs-sdk"></a>Comment utiliser le Kit de développement logiciel Node.js dans Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Cet article fournit des informations détaillées ainsi que des exemples sur l’utilisation d’un serveur principal Node.js dans Azure App Service Mobile Apps.

## <a name="Introduction"></a>Introduction
Azure App Service Mobile Apps offre la possibilité d’ajouter à une application Web une API Web d’accès aux données optimisé pour les plateformes mobiles.  Le Kit de développement logiciel (SDK) Azure App Service Mobile Apps est fourni pour les applications web ASP.NET et Node.js.  Le Kit offre les opérations suivantes :

* Opérations de table (Read, Insert, Update, Delete) pour l’accès aux données
* Opérations d’API personnalisées

Les deux types d’opérations permettent une authentification entre tous les fournisseurs d’identité autorisés par Azure App Service, y compris les fournisseurs d’identité sociaux tels que Facebook, Twitter, Google et Microsoft, ainsi qu’Azure Active Directory pour les identités d’entreprise.

Vous trouverez des exemples de chaque cas de figure dans le [répertoire d’exemples sur GitHub].

## <a name="supported-platforms"></a>Plateformes prises en charge
Le kit de développement logiciel Node dans Azure Mobile Apps prend en charge la version actuelle de LTS de Node ainsi que les versions ultérieures.  Au moment de la rédaction, la dernière version LTS de Node est 4.5.0.  Les autres versions de Node peuvent fonctionner, mais elles ne sont pas prises en charge.

Le kit de développement logiciel Node dans Azure Mobile Apps prend en charge deux pilotes de base de données : le pilote node-mssql prend en charge SQL Azure et les instances SQL Server locales.  Le pilote sqlite3 prend en charge les bases de données SQLite sur une seule instance.

### <a name="howto-cmdline-basicapp"></a>Procédure : créer un serveur principal Node.js de base à l’aide de la ligne de commande
Chaque serveur principal Node.js Azure App Service Mobile Apps démarre en tant qu’application ExpressJS.  ExpressJS est l’infrastructure de service web la plus populaire pour le serveur principal Node.js.  Vous pouvez créer une application [Express] de base de la manière suivante :

1. Dans une commande ou une fenêtre PowerShell, créez un répertoire pour votre projet.

        mkdir basicapp
2. Exécutez npm init pour initialiser la structure du package.

        cd basicapp
        npm init

    La commande npm init pose une série de questions pour initialiser le projet.  Consultez l’exemple pour voir le résultat obtenu :

    ![La sortie npm init][0]
3. Installez les bibliothèques express et azure-mobile-apps à partir du référentiel npm.

        npm install --save express azure-mobile-apps
4. Créez un fichier app.js pour implémenter le serveur mobile de base.

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Define a TodoItem table
        mobile.tables.add('TodoItem');

        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);

Cette application crée une WebAPI optimisée pour les applications mobiles avec un seul point de terminaison (`/tables/TodoItem`) qui fournit un accès non authentifié à un datastore SQL sous-jacent à l’aide d’un schéma dynamique.  Cette API est adaptée pour les démarrages rapides de bibliothèques client suivants :

* [Android Client QuickStart]
* [Apache Cordova Client QuickStart]
* [iOS Client QuickStart]
* [Windows Store Client QuickStart]
* [Xamarin.iOS Client QuickStart]
* [Xamarin.Android Client QuickStart]
* [Xamarin.Forms Client QuickStart]

Vous trouverez le code pour cette application de base dans l’ [exemple basicapp sur GitHub].

### <a name="howto-vs2015-basicapp"></a>Procédure : créer un serveur principal Node avec Visual Studio 2015
Visual Studio 2015 requiert une extension pour développer les applications Node.js dans l’IDE.  Pour commencer, installez le fichier [Node.js Tools 1.1 pour Visual Studio].  Une fois le programme Node.js Tools pour Visual Studio installé, créez une application Express 4.x :

1. Ouvrez la boîte de dialogue **Nouveau projet** (dans le menu **Fichier** > **Nouveau** > **Projet...**).
2. Développez **Modèles** > **JavaScript** > **Node.js**.
3. Sélectionnez **Basic Azure Node.js Express 4 Application**.
4. Renseignez le nom du projet.  Cliquez sur *OK*.

    ![Visual Studio 2015 Nouveau projet][1]
5. Cliquez avec le bouton droit de la souris sur le nœud **npm** et sélectionnez **Installer de nouveaux packages npm...**.
6. Vous devrez peut-être actualiser le catalogue npm lors de la création de votre première application Node.js.  Cliquez sur **Actualiser** si nécessaire.
7. Dans la zone de recherche, entrez *azure-mobile-apps* .  Cliquez sur le package **azure-mobile-apps 2.0.0**, puis cliquez sur **Installer le package**.

    ![Installer de nouveaux packages npm][2]
8. Cliquez sur **Fermer**.
9. Ouvrez le fichier *app.js* pour ajouter la prise en charge du SDK Azure Mobile Apps.  À la ligne 6 des instructions require de la bibliothèque, ajoutez le code suivant :

        var bodyParser = require('body-parser');
        var azureMobileApps = require('azure-mobile-apps');

    Environ à la ligne 27, après les autres instructions app.use, ajoutez le code suivant :

        app.use('/users', users);

        // Azure Mobile Apps Initialization
        var mobile = azureMobileApps();
        mobile.tables.add('TodoItem');
        app.use(mobile);

    Enregistrez le fichier .
10. Exécutez l’application en local (l’API est disponible sur http://localhost:3000) ou publiez-la dans Azure.

### <a name="create-node-backend-portal"></a>Comment : créer un serveur principal Node.js à l’aide du portail Azure
Vous pouvez créer un serveur principal d'application mobile dans le [portail Azure]. Vous pouvez suivre la procédure ci-dessous, ou créer simultanément un client et un serveur en suivant le didacticiel [Créer une application mobile](app-service-mobile-ios-get-started.md) . Ce didacticiel contient une version simplifiée de ces instructions et convient mieux aux projets de preuve de concept.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Dans le panneau *Prise en main*, sous **Créer table API de table**, sélectionnez **Node.js** en tant que **langue de backend**.
Cochez la case « **Je reconnais que cette opération va remplacer tout le contenu du site.** », puis cliquez sur **Créer une table TodoItem**.

### <a name="download-quickstart"></a>Procédure : télécharger le projet de code quickstart du serveur principal Node.js à l’aide de Git
Lorsque vous créez un serveur principal Node.js Mobile App à l’aide du panneau **Démarrage rapide** du portail, un projet Node.js est créé et déployé sur votre site. Vous pouvez ajouter des tables et des API et modifier les fichiers de code pour le serveur principal Node.js dans le portail. Vous pouvez également utiliser l’un des divers outils de déploiement pour télécharger le projet de serveur principal afin de pouvoir ajouter ou modifier des tables et des API, avant de publier à nouveau le projet. Pour plus d’informations, consultez le [Guide de déploiement d’Azure App Service]. La procédure suivante utilise un référentiel Git pour télécharger le code de projet quickstart.

1. Si vous ne l’avez pas déjà fait, installez Git. La procédure requise pour installer Git diffère selon les systèmes d'exploitation. Consultez la rubrique [Installation de Git](http://git-scm.com/book/en/Getting-Started-Installing-Git) pour accéder aux distributions et consignes d'installation propres aux différents systèmes d'exploitation.
2. Suivez les étapes de la rubrique [Activer le référentiel de l’application App Service](../app-service/app-service-deploy-local-git.md#Step3) pour activer le référentiel Git pour votre backend, en prenant note du nom d’utilisateur et du mot de passe utilisés pour le déploiement.
3. Dans le panneau de votre serveur principal Mobile App, prenez note du paramètre **URL de clone Git** .
4. Exécutez la commande `git clone` à l’aide de l’URL de clone Git, en saisissant si besoin votre mot de passe, comme dans l’exemple suivant :

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git
5. Accédez au répertoire local (/todolist dans l’exemple précédent). Vous remarquerez que les fichiers de projet ont été téléchargés. Recherchez le fichier `todoitem.json` dans le répertoire `/tables`.  Ce fichier définit les autorisations sur la table.  Recherchez également le fichier `todoitem.js` dans le même répertoire, qui définit les scripts de cette opération CRUD pour la table.
6. Après avoir apporté vos modifications aux fichiers de projet, exécutez les commandes suivantes pour ajouter, valider, puis charger les modifications sur le site :

        $ git commit -m "updated the table script"
        $ git push origin master

    Lorsque vous ajoutez de nouveaux fichiers au projet, vous devez tout d’abord exécuter la commande `git add .`.

Le site est republié chaque fois qu’un nouvel ensemble de validations est transmis au site.

### <a name="howto-publish-to-azure"></a>Procédure : publier votre serveur principal Node.js dans Azure
Microsoft Azure propose plusieurs mécanismes permettant de publier votre serveur principal Node.js Azure App Service Mobile Apps sur le service Azure.  En fonction du contrôle de la source, vous pouvez notamment utiliser les outils de déploiement intégrés à Visual Studio, les outils de ligne de commande et les options de déploiement continu.  Pour plus d’informations sur ce sujet, reportez-vous au [Guide de déploiement d’Azure App Service].

Avant le déploiement, vous devriez prendre connaissance des recommandations suivantes pour l’application Node.js avec Azure App Service :

* Comment [spécifier la version de Node]
* Comment [utiliser les modules Node]

### <a name="howto-enable-homepage"></a>Procédure : activation d’une page d’accueil pour votre application
De nombreuses applications regroupent à la fois des applications web et des applications mobiles ; l’infrastructure ExpressJS vous permet de combiner ces deux facettes.  Il peut cependant être préférable, parfois, d’implémenter uniquement une interface mobile.  Il est utile de fournir une page d’accueil pour obtenir la garantie que le service d’application fonctionne correctement.  Vous pouvez soit fournir votre propre page d’accueil, soit activer une page d’accueil temporaire.  Pour activer une page d’accueil temporaire, utilisez ce qui suit pour instancier Azure Mobile Apps :

    var mobile = azureMobileApps({ homePage: true });

Si vous voulez que cette option soit disponible uniquement pour un développement en local, vous pouvez ajouter ce paramètre à votre fichier `azureMobile.js` .

## <a name="TableOperations"></a>Opérations de table
Le Kit de développement logiciel Node.js Server azure-mobile-apps fournit des mécanismes permettant d’exposer les tables de données stockées dans la base de données SQL Azure sous la forme d’une WebAPI.  Cinq opérations sont fournies.

| Opération | DESCRIPTION |
| --- | --- |
| GET /tables/*tablename* |Extraire tous les enregistrements de la table |
| GET /tables/*tablename*/:id |Extraire un enregistrement spécifique de la table |
| POST /tables/*tablename* |Créer un enregistrement dans la table |
| PATCH /tables/*tablename*/:id |Mettre à jour un enregistrement dans la table |
| DELETE /tables/*tablename*/:id |Supprimer un enregistrement de la table |

Cette WebAPI prend en charge [OData] et étend le schéma de table pour prendre en charge [la synchronisation des données hors connexion].

### <a name="howto-dynamicschema"></a>Procédure : définir des tables à l’aide d’un schéma dynamique
Avant de pouvoir utiliser une table, vous devez la définir.  Les tables peuvent être définies avec un schéma statique (dans lequel le développeur définit les colonnes du schéma) ou dynamique (dans lequel le SDK contrôle le schéma en fonction des demandes entrantes). En outre, le développeur peut contrôler des aspects spécifiques de la WebAPI en ajoutant du code Javascript à la définition.

Il est recommandé de définir chaque table dans un fichier Javascript du répertoire de tables, puis d’utiliser la méthode tables.import() pour importer les tables.  En étendant l’application de base, le fichier app.js sera ajusté comme suit :

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define the database schema that is exposed
    mobile.tables.import('./tables');

    // Provide initialization of any tables that are statically defined
    mobile.tables.initialize().then(function () {
        // Add the mobile API so it is accessible as a Web API
        app.use(mobile);

        // Start listening on HTTP
        app.listen(process.env.PORT || 3000);
    });

Définissez la table dans ./tables/TodoItem.js :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Additional configuration for the table goes here

    module.exports = table;

Par défaut, les tables utilisent le schéma dynamique.  Pour désactiver globalement le schéma dynamique, définissez le paramètre d’application **MS_DynamicSchema** sur False dans le portail Azure.

Vous en trouverez un exemple complet dans l’ [exemple todo sur GitHub].

### <a name="howto-staticschema"></a>Procédure : définir des tables à l’aide d’un schéma statique
Vous pouvez définir explicitement les colonnes à exposer via la WebAPI.  Le SDK Node/js azure-mobile-apps ajoute automatiquement à la liste que vous fournissez toutes les colonnes supplémentaires requises pour la synchronisation des données hors connexion.  Par exemple, les applications clientes QuickStart requièrent une table à deux colonnes : une colonne texte (une chaîne) et une colonne complète (une valeur booléenne).  
Vous pouvez définir ces colonnes dans le fichier JavaScript de définition de table (situé dans le répertoire de tables) comme suit :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    module.exports = table;

Si vous définissez des tables de manière statique, vous devez également appeler la méthode tables.initialize() pour créer le schéma de base de données au démarrage.  La méthode tables.initialize() renvoie un objet [Promise] qui permet de s’assurer que le service web ne traite pas les requêtes avant l’initialisation de la base de données.

### <a name="howto-sqlexpress-setup"></a>Procédure : utiliser SQL Express comme datastore de développement sur votre ordinateur local
Le SDK Node Azure Mobile Apps propose trois options de distribution standard des données :

* Utilisez le pilote **memory** pour fournir un exemple de datastore non persistant
* Utilisez le pilote **mssql** pour fournir un datastore SQL Express utilisé pour le développement
* Utilisez le pilote **mssql** pour fournir un datastore de base de données SQL Azure utilisé pour la production

Le SDK Node.js Azure Mobile Apps utilise le [package mssql Node] pour établir et utiliser une connexion à SQL Express et à la base de données SQL.  Ce package nécessite l’activation de connexions TCP sur votre instance SQL Express.

> [!TIP]
> Le pilote memory ne fournit pas un ensemble complet de fonctionnalités à des fins de test.  Si vous souhaitez tester localement votre serveur principal, nous vous recommandons d’utiliser un datastore SQL Express et d’utiliser le pilote mssql.
>
>

1. Téléchargez et installez [Microsoft SQL Server 2014 Express].  Veillez à bien installer SQL Server 2014 Express avec l’édition Tools.  À moins que vous ayez explicitement besoin d’une prise en charge 64 bits, la version 32 bits consomme moins de mémoire lors de son exécution.
2. Exécutez le Gestionnaire de configuration de SQL Server 2014.

   1. Développez le nœud **SQL Server Network Configuration** dans le menu de l’arborescence de gauche.
   2. Cliquez sur **Protocols for SQLEXPRESS**.
   3. Cliquez avec le bouton droit sur **TCP/IP**, puis sélectionnez **Enable**.  Cliquez sur **OK** dans la boîte de dialogue contextuelle.
   4. Cliquez avec le bouton droit sur **TCP/IP**, puis sélectionnez **Properties**.
   5. Cliquez sur l'onglet **IP Addresses** .
   6. Recherchez le nœud **IPAll** .  Dans le champ **TCP Port**, entrez **1433**.

          ![Configure SQL Express for TCP/IP][3]
   7. Cliquez sur **OK**.  Cliquez sur **OK** dans la boîte de dialogue contextuelle.
   8. Cliquez sur **SQL Server Services** dans le menu de l’arborescence de gauche.
   9. Cliquez avec le bouton droit sur **SQL Server (SQLEXPRESS)**, puis sélectionnez **Restart**.
   10. Fermez le Gestionnaire de configuration de SQL Server 2014.
3. Exécutez l’instance SQL Server 2014 Management Studio et connectez-vous à votre instance locale de SQL Express.

   1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur votre instance et sélectionnez **Properties**
   2. Sélectionnez la page **Security** .
   3. Assurez-vous que le **mode d’authentification SQL Server et Windows** est bien sélectionné.
   4. Cliquez sur **OK**

          ![Configure SQL Express Authentication][4]
   5. Développez **Security** > **Logins** dans l’Explorateur d’objets.
   6. Cliquez avec le bouton droit sur **Connexions** et sélectionnez **Nouvelle connexion...**.
   7. Entrez un nom de connexion.  Sélectionnez **Authentification SQL Server**.  Entrez un mot de passe, puis saisissez le même mot de passe dans le champ **Confirmer le mot de passe**.  Le mot de passe doit répondre aux exigences de complexité de Windows.
   8. Cliquez sur **OK**

          ![Add a new user to SQL Express][5]
   9. Cliquez avec le bouton droit sur votre nouveau compte de connexion et sélectionnez **Properties**
   10. Sélectionnez la page **Server Roles** .
   11. Cochez la case en regard du rôle de serveur **dbcreator** .
   12. Cliquez sur **OK**
   13. Fermez SQL Server 2015 Management Studio.

Veillez à enregistrer les nom d’utilisateur et mot de passe que vous avez sélectionnés.  Vous devrez peut-être affecter des rôles serveur ou autorisations supplémentaires selon les besoins spécifiques de votre base de données.

L’application Node.js lit la variable d’environnement **SQLCONNSTR_MS_TableConnectionString** pour la chaîne de connexion de cette base de données.  Vous pouvez définir cette variable dans votre environnement.  Par exemple, vous pouvez utiliser PowerShell pour définir cette variable d’environnement :

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Accédez à la base de données via une connexion TCP/IP et fournissez un nom d’utilisateur et un mot de passe pour vous connecter.

### <a name="howto-config-localdev"></a>Procédure : configurer votre projet pour un développement local
Azure Mobile Apps lit un fichier JavaScript appelé *azureMobile.js* à partir du système de fichiers local.  N’utilisez pas ce fichier pour configurer le SDK Azure Mobile Apps en production, mais plutôt les paramètres d’application du [portail Azure] .  Le fichier *azureMobile.js* doit exporter un objet de configuration.  Les paramètres les plus courants sont les suivants :

* Paramètres de base de données
* Paramètres de journalisation des diagnostics
* Paramètres CORS de remplacement

Vous trouverez ci-dessous un exemple de fichier *azureMobile.js* implémentant ces paramètres de base de données :

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

Nous vous recommandons d’ajouter *azureMobile.js* à votre fichier *.gitignore* (ou un autre fichier ignore de contrôle du code source) pour éviter que les mots de passe soient stockés dans le cloud.  Veillez à toujours configurer les paramètres de production dans les paramètres d’application du [portail Azure].

### <a name="howto-appsettings"></a>Procédure : configuration des paramètres d’application pour votre application mobile
La plupart des paramètres du fichier *azureMobile.js* ont un paramètre équivalent dans le [portail Azure].  Utilisez la liste suivante pour configurer votre application dans les paramètres d’application :

| Paramètre d'application | *azureMobile.js* | DESCRIPTION | Valeurs valides |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |Nom |Nom de l’application |chaîne |
| **MS_MobileLoggingLevel** |logging.level |Niveau minimal de journal pour les messages à consigner |error, warning, info, verbose, debug, silly |
| **MS_DebugMode** |debug |Activer ou désactiver le mode débogage |true, false |
| **MS_TableSchema** |data.schema |Nom de schéma par défaut pour les tables SQL |string (valeur par défaut : dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Activer ou désactiver le mode débogage |true, false |
| **MS_DisableVersionHeader** |version (sur Undefined) |Désactive l'en-tête X-ZUMO-Server-Version |true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Désactive la vérification de la version de l’API client |true, false |

Pour définir un paramètre d’application :

1. Connectez-vous au [portail Azure].
2. Sélectionnez **Toutes les ressources** ou **App Services**, puis cliquez sur le nom de votre application mobile.
3. Le panneau Paramètres s’ouvre par défaut. Si ce n’est pas le cas, cliquez sur **Paramètres**.
4. Cliquez sur **Paramètres de l’application** dans le menu GÉNÉRAL.
5. Accédez à la section Paramètres de l’application.
6. Si votre paramètre d’application existe déjà, cliquez sur la valeur correspondante pour la modifier.
7. Si votre paramètre d’application n’existe pas, saisissez-le dans la zone Clé et la valeur dans la zone Valeur.
8. Une fois que vous avez terminé, cliquez sur **Enregistrer**.

La modification de la plupart des paramètres requiert le redémarrage du service.

### <a name="howto-use-sqlazure"></a>Procédure : utiliser la base de données SQL comme datastore de production
<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

L’utilisation de la base de données SQL Azure en tant que datastore est identique pour tous les types d’applications Azure App Service. Si vous ne l’avez pas déjà fait, suivez ces étapes pour créer un serveur principal d’application mobile.

1. Connectez-vous au [portail Azure].
2. Dans le coin supérieur gauche de la fenêtre, cliquez sur le bouton **+ NOUVEAU** > **Web + Mobile**>**Application mobile**, puis indiquez un nom pour votre serveur principal d’application mobile.
3. Dans la zone **Groupe de ressources** , entrez le même nom que votre application.
4. Le plan App Service par défaut est sélectionné.  Pour modifier votre plan App Service, cliquez sur le plan App Service > **+ Créer nouveau**.  Indiquez le nom du nouveau plan App Service et sélectionnez un emplacement approprié.  Cliquez sur Niveau de tarification et sélectionnez un niveau de tarification approprié pour le service. Sélectionnez **Afficher tout** pour afficher davantage d’options de tarification, telles que **Gratuit** et **Partagé**.  Une fois que vous avez sélectionné le niveau de tarification, cliquez sur le bouton **Sélectionner** .  Retournez dans le panneau **Plan App Service**, puis cliquez sur **OK**.
5. Cliquez sur **Créer**. La configuration d’un serveur principal d’application mobile peut prendre quelques minutes.  Une fois le serveur principal d’application mobile configuré, le portail ouvre le panneau **Paramètres** correspondant au serveur principal d’application mobile.

Une fois le serveur principal d’application mobile créé, vous pouvez choisir de connecter une base de données SQL existante à votre serveur principal d’application mobile ou de créer une nouvelle base de données SQL.  Dans cette section, nous créons une base de données SQL.

> [!NOTE]
> Si vous avez déjà une base de données dans le même emplacement que le serveur principal d’application mobile, vous pouvez choisir **Utiliser une base de données** et sélectionner la base de données. Il est déconseillé d’utiliser une base de données dans un autre emplacement en raison de latences plus importantes.
>
>

1. Dans le nouveau serveur principal d’application mobile, cliquez sur **Paramètres** > **Application mobile** > **Données** > **+Ajouter**.
2. Dans le panneau **Ajouter une connexion de données**, cliquez sur **Base de données SQL - Configurer les paramètres requis** > **Créer une base de données**.  Entrez le nom de la nouvelle base de données dans le champ **Nom** .
3. Cliquez sur **Serveur**.  Dans le panneau **Nouveau serveur**, entrez un nom de serveur unique dans le champ **Nom du serveur**, et indiquez un **nom de connexion d’administration serveur** et un **mot de passe** appropriés.  Vérifiez que l'option **Autoriser les services Azure à accéder au serveur** est sélectionnée.  Cliquez sur **OK**.

    ![Création d’une base de données SQL Azure][6]
4. Dans le panneau **Nouvelle base de données**, cliquez sur **OK**.
5. Dans le panneau **Ajouter une connexion de données**, sélectionnez **Chaîne de connexion**, entrez le nom de connexion et le mot de passe que vous avez indiqués lors de la création de la base de données.  Si vous utilisez une base de données existante, indiquez les informations d’identification de connexion à celle-ci.  Cliquez ensuite sur **OK**.
6. Dans le panneau **Ajouter une connexion de données**, cliquez sur **OK** pour créer la base de données.

<!--- END OF ALTERNATE INCLUDE -->

La création de la base de données prend quelques minutes.  Utilisez la zone **Notifications** pour surveiller la progression du déploiement.  Attendez la fin du déploiement avant de continuer.  Une fois le déploiement effectué, une chaîne de connexion est créée pour l’instance de base de données SQL dans les paramètres d’application de votre serveur principal d’application mobile.  Vous pouvez voir ce paramètre d’application dans **Paramètres** > **Paramètres de l’application** > **Chaînes de connexion**.

### <a name="howto-tables-auth"></a>Procédure : exiger une authentification pour l’accès aux tables
Si vous souhaitez utiliser l’authentification App Service avec le point de terminaison des tables, vous devez d’abord configurer l’authentification App Service dans le [portail Azure] .  Pour plus d’informations sur la configuration de l’authentification dans Azure App Service, consultez le Guide de configuration du fournisseur d’identité que vous souhaitez utiliser :

* [Comment configurer votre application pour utiliser la connexion Azure Active Directory]
* [Comment configurer votre application pour utiliser une connexion Facebook]
* [Comment configurer votre application pour utiliser une connexion Google]
* [Comment configurer votre application pour utiliser une connexion par compte Microsoft]
* [Comment configurer votre application pour utiliser une connexion Twitter]

Chaque table possède une propriété d’accès que vous pouvez utiliser pour contrôler l’accès à la table.  L’exemple suivant illustre une table définie de façon statique avec l’authentification requise.

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

La propriété d’accès peut prendre trois valeurs :

* *anonymous* indique que l’application cliente est autorisée à lire les données sans authentification
* *authenticated* indique que l’application cliente doit envoyer un jeton d’authentification valide avec la requête
* *disabled* indique que cette table est actuellement désactivée

Si la propriété d’accès n’est pas définie, l’accès non authentifié est autorisé.

### <a name="howto-tables-getidentity"></a>Procédure : utilisation des revendications d’authentification avec vos tables
Vous pouvez configurer différentes revendications qui sont demandées lors de la configuration de l'authentification.  Ces revendications ne sont normalement pas disponibles via l’objet `context.user` .  Toutefois, elles peuvent être récupérées à l'aide de la méthode `context.user.getIdentity()` .  La méthode `getIdentity()` renvoie une promesse qui correspond à un objet.  L'objet est indexé par la méthode d'authentification (facebook, google, twitter, microsoftaccount ou aad).

Par exemple, si vous configurez l'authentification de compte Microsoft et demandez la revendication des adresses de messagerie, vous pouvez ajouter l'adresse de messagerie à l'enregistrement avec le contrôleur de table suivant :

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
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

    // Configure specific code when the client does a request
    // READ - only return records belonging to the authenticated user
    table.read(queryContextForEmail);

    // CREATE - add or overwrite the userId based on the authenticated user
    table.insert(addEmailToContext);

    // UPDATE - only allow updating of record belong to the authenticated user
    table.update(queryContextForEmail);

    // DELETE - only allow deletion of records belong to the authenticated uer
    table.delete(queryContextForEmail);

    module.exports = table;

Pour voir les revendications disponibles, utilisez un navigateur web pour afficher le point de terminaison `/.auth/me` de votre site.

### <a name="howto-tables-disabled"></a>Procédure : désactiver l’accès à des opérations de table spécifiques
En plus d’apparaître sur la table, la propriété d’accès peut être utilisée pour contrôler des opérations spécifiques.  Il existe quatre opérations :

* *read* : opération GET RESTful sur la table
* *insert* : opération POST RESTful sur la table
* *update* : opération PATCH RESTful sur la table
* *delete* : opération DELETE RESTful sur la table

Vous pouvez, par exemple, souhaiter fournir une table non authentifiée en lecture seule :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Read-Only table - only allow READ operations
    table.read.access = 'anonymous';
    table.insert.access = 'disabled';
    table.update.access = 'disabled';
    table.delete.access = 'disabled';

    module.exports = table;

### <a name="howto-tables-query"></a>Procédure : ajuster la requête utilisée avec les opérations de table
On attend souvent des opérations de table qu’elles soient capables de fournir une vue restreinte des données.  Par exemple, vous pouvez fournir une table marquée avec l’ID de l’utilisateur authentifié, vous permettant uniquement de lire ou de mettre à jour vos propres enregistrements.  La définition de table suivante offre cette fonctionnalité :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define a static schema for the table
    table.columns = {
        "userId": "string",
        "text": "string",
        "complete": "boolean"
    };
    table.dynamicSchema = false;

    // Require authentication for this table
    table.access = 'authenticated';

    // Ensure that only records for the authenticated user are retrieved
    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

    // When adding records, add or overwrite the userId with the authenticated user
    table.insert(function (context) {
        context.item.userId = context.user.id;
        return context.execute();
    });

    module.exports = table;

Les opérations qui exécutent normalement une requête ont une propriété de requête que vous pouvez ajuster avec une clause where. La propriété de requête est un objet [QueryJS] utilisé pour convertir une requête OData en données que le serveur principal pourra traiter.  Pour les cas d’égalité simple (comme dans l’exemple précédent), vous pouvez utiliser un mappage. Vous pouvez également ajouter des clauses SQL spécifiques :

    context.query.where('myfield eq ?', 'value');

### <a name="howto-tables-softdelete"></a>Procédure : configurer une suppression réversible sur une table
La suppression réversible (Soft Delete) ne supprime pas réellement les enregistrements.  Elle les marque comme étant supprimés dans la base de données en définissant la colonne supprimée sur true.  Le SDK Azure Mobile Apps supprime automatiquement des résultats les enregistrements ainsi supprimés, sauf si le SDK Mobile Client utilise IncludeDeleted().  Pour configurer une table pour une suppression réversible, définissez la propriété `softDelete` dans le fichier de définition de table :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Turn on Soft Delete
    table.softDelete = true;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

Vous devez établir un mécanisme pour purger les enregistrements, à partir d’une application cliente, via un WebJob, via une fonction Azure ou via une API personnalisée.

### <a name="howto-tables-seeding"></a>Procédure : alimenter votre base de données
Lorsque vous créez une nouvelle application, vous souhaiterez sans doute alimenter une table avec des données.  Pour ce faire, vous pouvez utiliser le fichier JavaScript de définition de table, comme indiqué ci-dessous :

    var azureMobileApps = require('azure-mobile-apps');

    var table = azureMobileApps.table();

    // Define the columns within the table
    table.columns = {
        "text": "string",
        "complete": "boolean"
    };
    table.seed = [
        { text: 'Example 1', complete: false },
        { text: 'Example 2', complete: true }
    ];

    // Turn off dynamic schema
    table.dynamicSchema = false;

    // Require authentication to access the table
    table.access = 'authenticated';

    module.exports = table;

L’amorçage de données n’est possible que lorsque la table est créée à l’aide du SDK Azure Mobile Apps.  Si la table existe déjà dans la base de données, aucune donnée ne sera injectée dans la table.  Si le schéma dynamique est activé, alors le schéma est déduit des données amorcées.

Nous vous recommandons d’appeler explicitement la méthode `tables.initialize()` pour créer la table au début de l’exécution du service.

### <a name="Swagger"></a>Procédure : activation de la prise en charge de Swagger
Azure App Service Mobile Apps prend nativement en charge l’interface [Swagger] .  Pour activer la prise en charge de Swagger, commencez par installer l’interface utilisateur Swagger en tant que dépendance :

    npm install --save swagger-ui

Une fois l’installation effectuée, vous pouvez activer la prise en charge de Swagger dans le constructeur Azure Mobile Apps :

    var mobile = azureMobileApps({ swagger: true });

Vous préférerez probablement activer la prise en charge de Swagger uniquement dans les éditions de développement.  Pour ce faire, vous pouvez utiliser le paramètre d’application `NODE_ENV` :

    var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });

Le point de terminaison swagger se trouve à l’adresse http://*VotreSite*.azurewebsites.net/swagger.  Vous pouvez accéder à l’interface utilisateur Swagger via le point de terminaison `/swagger/ui`.  Si vous décidez de demander une authentification sur l’ensemble de votre application, Swagger génère une erreur.  Pour obtenir de meilleurs résultats, autorisez les demandes non authentifiées via les paramètres d’authentification et d’autorisation d’Azure App Service. Vous pouvez ensuite contrôler l’authentification en utilisant la propriété `table.access`.

Vous pouvez également ajouter l’option Swagger à votre fichier `azureMobile.js` si vous voulez activer la prise en charge de Swagger uniquement dans le cadre d’un développement en local.

## <a name="a-namepushpush-notifications"></a><a name="push">Notifications Push
Mobile Apps s’intègre à Azure Notification Hubs pour vous permettre d’envoyer des notifications Push ciblées à des millions d’appareils utilisant toutes les plateformes les plus populaires. Notification Hubs vous permet d’envoyer des notifications Push aux appareils iOS, Android et Windows. Pour plus d'informations sur ce que Notification Hubs vous permet de faire, consultez [Vue d'ensemble de Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### </a><a name="send-push"></a>Procédure : envoi de notifications Push
Le code suivant vous montre comment utiliser l’objet Push pour envoyer une notification Push de diffusion à des appareils iOS inscrits :

    // Create an APNS payload.
    var payload = '{"aps": {"alert": "This is an APNS payload."}}';

    // Only do the push if configured
    if (context.push) {
        // Send a push notification using APNS.
        context.push.apns.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }

En créant un modèle d’inscription Push à partir du client, vous pouvez simplement envoyer un modèle de message Push à des appareils sur toutes les plateformes prises en charge. Le code suivant montre comment envoyer un modèle de notification :

    // Define the template payload.
    var payload = '{"messageParam": "This is a template payload."}';

    // Only do the push if configured
    if (context.push) {
        // Send a template notification.
        context.push.send(null, payload, function (error) {
            if (error) {
                // Do something or log the error.
            }
        });
    }


### <a name="push-user"></a>Procédure : envoi de notifications Push à un utilisateur authentifié à l’aide de balises
Lorsqu’un utilisateur authentifié s’inscrit aux notifications Push, une balise avec l’ID d’utilisateur est automatiquement ajoutée à l’inscription. Grâce à cette balise, vous pouvez envoyer des notifications Push à tous les appareils inscrits par un utilisateur spécifique. Le code suivant permet d’obtenir le SID de l’utilisateur qui émet la demande et d’envoyer un modèle de notification Push à chaque inscription d’appareil pour cet utilisateur :

    // Only do the push if configured
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
### <a name="howto-customapi-basic"></a>Procédure : définition d'une API personnalisée
Outre l’API d’accès aux données via le point de terminaison /tables, Azure Mobile Apps peut fournir une couverture d’API personnalisée.  Les API personnalisées sont définies de manière similaire aux définitions de table et sont accessibles à toutes les mêmes fonctionnalités, y compris l’authentification.

Si vous souhaitez utiliser l’authentification App Service avec une API personnalisée, vous devez d’abord configurer l’authentification App Service dans le [portail Azure] .  Pour plus d’informations sur la configuration de l’authentification dans Azure App Service, consultez le Guide de configuration du fournisseur d’identité que vous souhaitez utiliser :

* [Comment configurer votre application pour utiliser la connexion Azure Active Directory]
* [Comment configurer votre application pour utiliser une connexion Facebook]
* [Comment configurer votre application pour utiliser une connexion Google]
* [Comment configurer votre application pour utiliser une connexion par compte Microsoft]
* [Comment configurer votre application pour utiliser une connexion Twitter]

La définition des API personnalisées est largement similaire à celle des API de tables.

1. Créez un répertoire **api** .
2. Créez un fichier JavaScript de définition d’API dans le répertoire **api** .
3. Utilisez la méthode import pour importer le répertoire **api** .

Voici le prototype de définition d’API dérivé de l’exemple d’application de base utilisé précédemment.

    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Prenons un exemple d’API qui renvoie la date du serveur à l’aide de la méthode *Date.now()* .  Voici le fichier api/date.js :

    var api = {
        get: function (req, res, next) {
            var date = { currentTime: Date.now() };
            res.status(200).type('application/json').send(date);
        });
    };

    module.exports = api;

Chaque paramètre correspond à l’un des verbes RESTful standard : GET, POST, PATCH ou DELETE.  La méthode est une fonction [ExpressJS Middleware] standard qui envoie la sortie requise.

### <a name="howto-customapi-auth"></a>Procédure : exiger une authentification pour l’accès à une API personnalisée
Le SDK Azure Mobile Apps implémente l’authentification de la même façon pour le point de terminaison des tables et pour les API personnalisées.  Pour ajouter l’authentification à l’API développée dans la section précédente, ajoutez une propriété **access** :

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

Pour les API personnalisées qui requièrent une authentification, vous devez utiliser le même jeton que celui utilisé pour le point de terminaison des tables.

### <a name="howto-customapi-auth"></a>Procédure : gestion des téléchargements de fichiers volumineux
Le Kit de développement logiciel (SDK) Azure Mobile Apps utilise l’ [intergiciel corps-parser](https://github.com/expressjs/body-parser) pour accepter et décoder le contenu du corps de votre demande.  Vous pouvez préconfigurer corps-parser pour accepter les téléchargements de fichiers plus volumineux :

    var express = require('express'),
        bodyParser = require('body-parser'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Set up large body content handling
    app.use(bodyParser.json({ limit: '50mb' }));
    app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

    // Import the Custom API
    mobile.api.import('./api');

    // Add the mobile API so it is accessible as a Web API
    app.use(mobile);

    // Start listening on HTTP
    app.listen(process.env.PORT || 3000);

Le fichier est codé en base 64 avant la transmission.  Cela augmente la taille du téléchargement réel (et par conséquent la taille dont vous devez tenir compte).

### <a name="howto-customapi-sql"></a>Procédure : exécution d’instructions SQL personnalisées
Le kit de développement logiciel (SDK) Azure Mobile Apps permet d’accéder à l’ensemble du contexte via l’objet de la demande, ce qui vous permet d’exécuter facilement des instructions SQL paramétrées pour le fournisseur de données défini :

    var api = {
        get: function (request, response, next) {
            // Check for parameters - if not there, pass on to a later API call
            if (typeof request.params.completed === 'undefined')
                return next();

            // Define the query - anything that can be handled by the mssql
            // driver is allowed.
            var query = {
                sql: 'UPDATE TodoItem SET complete=@completed',
                parameters: [{
                    completed: request.params.completed
                }]
            };

            // Execute the query.  The context for Azure Mobile Apps is available through
            // request.azureMobile - the data object contains the configured data provider.
            request.azureMobile.data.execute(query)
            .then(function (results) {
                response.json(results);
            });
        }
    };

    api.get.access = 'authenticated';
    module.exports = api;

## <a name="Debugging"></a>Débogage, Tables facile et API simples
### <a name="howto-diagnostic-logs"></a>Procédure : débogage, diagnostic et résolution des problèmes sur Azure Mobile Apps
Azure App Service fournit plusieurs techniques de débogage et de résolution des problèmes pour les applications Node.js.
Consultez les articles suivants pour prendre en main le dépannage de votre serveur principal mobile Node.js :

* [Surveiller les applications web dans Microsoft Azure App Service]
* [Activer la journalisation des diagnostics pour les applications web dans Azure App Service]
* [Dépanner un service Azure App dans Visual Studio]

Les applications Node.js ont accès à un large éventail d’outils de journaux de diagnostic.  En interne, le SDK Node.js Azure Mobile Apps utilise [Winston] pour la journalisation des diagnostics.  La journalisation est activée automatiquement si vous activez le mode débogage ou définissez le paramètre d’application **MS_DebugMode** sur true dans le [portail Azure]. Les journaux générés s’affichent dans les journaux de diagnostic sur le [portail Azure].

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Procédure : utilisation de l’outil Tables faciles dans le Portail Azure
L’outil Easy Tables du portail vous permet de créer et utiliser des tables directement dans le portail. Vous pouvez télécharger le jeu de données vers Tables faciles au format CSV. Notez que vous ne pouvez pas utiliser de noms de propriétés (dans ce jeu de données CSV) qui sont en conflit avec des noms de propriétés système d’Azure Mobile Apps Backend. Les noms de propriétés système sont :
* createdAt
* updatedAt
* deleted
* version

Vous pouvez même modifier les opérations de table à l’aide de l’éditeur App Service. Lorsque vous cliquez sur **Easy Tables** dans vos paramètres de site principal, vous pouvez ajouter, modifier ou supprimer une table. Vous pouvez également voir les données de la table.

![Utilisation de l’outil Easy Tables](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Les commandes suivantes sont disponibles dans la barre de commandes d’une table :

* **Modifier les autorisations** : modifier l’autorisation pour les opérations de lecture, d’insertion, de mise à jour et de suppression sur la table.
  Vous avez la possibilité d’autoriser l’accès anonyme, d’exiger une authentification ou de désactiver tous les accès à l’opération.
* **Modifier le script** : le fichier de script de la table est ouvert dans l’éditeur App Service.
* **Gérer un schéma** : ajouter ou supprimer des colonnes ou modifier l’index de la table.
* **Effacer la table** : tronque une table existante en supprimant toutes les lignes de données tout en conservant le schéma à l’identique.
* **Supprimer des lignes** : supprimer des lignes de données spécifiques.
* **Afficher les journaux de diffusion en continu** : permet de vous connecter au service de journaux de diffusion en continu de votre site.

### <a name="work-easy-apis"></a>Procédure : utiliser l’outil Easy APIs dans le portail Azure
L’outil Easy APIs du portail vous permet de créer et utiliser des API personnalisées directement dans le portail. Vous pouvez modifier des scripts API à l’aide de l’éditeur App Service.

Lorsque vous cliquez sur **Easy APIs** dans vos paramètres de site principal, vous pouvez ajouter modifier ou supprimer un point de terminaison API personnalisé.

![Utilisation de l’outil Easy APIs](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

Dans le portail, vous pouvez modifier les autorisations d’accès pour une action HTTP donnée, modifier le fichier de script d’API dans l’éditeur App Service ou afficher les journaux de diffusion en continu.

### <a name="online-editor"></a>Comment : modifier le code dans l’éditeur App Service
Le portail Azure vous permet de modifier les fichiers de script de votre serveur principal Node.js dans l’éditeur App Service sans avoir à télécharger le projet sur votre ordinateur local. Pour modifier les fichiers de script dans l’éditeur en ligne :

1. Dans le panneau de votre serveur principal d’application mobile, cliquez sur **Tous les paramètres** > **Tables faciles** ou **API faciles**, cliquez sur une table ou une API, puis cliquez sur **Modifier le script**. Le fichier de script est ouvert dans l’éditeur App Service.

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
[Android Client QuickStart]: app-service-mobile-android-get-started.md
[Apache Cordova Client QuickStart]: app-service-mobile-cordova-get-started.md
[iOS Client QuickStart]: app-service-mobile-ios-get-started.md
[Xamarin.iOS Client QuickStart]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android Client QuickStart]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms Client QuickStart]: app-service-mobile-xamarin-forms-get-started.md
[Windows Store Client QuickStart]: app-service-mobile-windows-store-dotnet-get-started.md
[la synchronisation des données hors connexion]: app-service-mobile-offline-data-sync.md
[Comment configurer votre application pour utiliser la connexion Azure Active Directory]: ../app-service/app-service-mobile-how-to-configure-active-directory-authentication.md
[Comment configurer votre application pour utiliser une connexion Facebook]: ../app-service/app-service-mobile-how-to-configure-facebook-authentication.md
[Comment configurer votre application pour utiliser une connexion Google]: ../app-service/app-service-mobile-how-to-configure-google-authentication.md
[Comment configurer votre application pour utiliser une connexion par compte Microsoft]: ../app-service/app-service-mobile-how-to-configure-microsoft-authentication.md
[Comment configurer votre application pour utiliser une connexion Twitter]: ../app-service/app-service-mobile-how-to-configure-twitter-authentication.md
[Guide de déploiement d’Azure App Service]: ../app-service/app-service-deploy-local-git.md
[Surveiller les applications web dans Microsoft Azure App Service]: ../app-service/web-sites-monitor.md
[Activer la journalisation des diagnostics pour les applications web dans Azure App Service]: ../app-service/web-sites-enable-diagnostic-log.md
[Dépanner un service Azure App dans Visual Studio]: ../app-service/web-sites-dotnet-troubleshoot-visual-studio.md
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
[package mssql Node]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
