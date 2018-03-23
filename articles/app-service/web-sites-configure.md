---
title: Configurer des applications web dans Azure App Service
description: Configuration d’une application web dans Azure App Service
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: cephalin
ms.openlocfilehash: 58c27c0872978c3a6a4c47be37e6fa6078309286
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="configure-web-apps-in-azure-app-service"></a>Configurer des applications web dans Azure App Service

Cet article explique comment configurer une application web à l’aide du [portail Azure].

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="application-settings"></a>Paramètres de l’application
1. Sur le [portail Azure], ouvrez le panneau de l’application Web.
3. Cliquez sur **Paramètres de l’application**.

![Paramètres de l’application][configure01]

Le panneau **Paramètres de l’application** regroupe différents paramètres sous plusieurs catégories.

### <a name="general-settings"></a>Paramètres généraux :
**Versions d'infrastructure**. Définissez ces options si votre application utilise l'une de ces infrastructures : 

* **.NET Framework**: définissez la version du .NET Framework. 
* **PHP** : définissez la version du langage PHP ou choisissez **INACTIF** pour désactiver PHP. 
* **Java** : sélectionnez la version du langage Java ou choisissez **INACTIF** pour désactiver Java. Utilisez l’option **Conteneur Web** pour choisir entre les versions Tomcat et Jetty.
* **Python** : sélectionnez la version du langage Python ou choisissez **INACTIF** pour désactiver Python.

Pour des raisons techniques, l’activation de Java pour votre application désactive les options .NET, PHP et Python.

<a name="platform"></a>
**Plateforme**. Indique si votre application web s’exécute dans un environnement 32 bits ou 64 bits. L'environnement 64 bits demande le mode De base ou Standard. Les modes Gratuit et Partagé s'exécutent uniquement dans un environnement 32 bits.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

**Web Sockets**. Sélectionnez la valeur **ACTIF** pour activer le protocole WebSocket. Par exemple, si votre application web utilise [ASP.NET SignalR] ou [socket.io](https://socket.io/).

<a name="alwayson"></a>
**Toujours actif**. Par défaut, les applications web sont déchargées si elles sont inactives pendant un certain temps. Cela permet au système d’économiser des ressources. En mode De base ou Standard, vous pouvez activer l’option **Toujours actif** pour garder l’application chargée en permanence. Si votre application exécute des WebJobs en continu ou après déclenchement par une expression CRON, activez l’option **Toujours actif**. Sinon, ils risquent de ne pas s’exécuter de façon fiable.

**Version de pipeline gérée**. Définit le [mode pipeline]d'IIS. Laissez la valeur par défaut, Intégré, sauf si vous avez une application web qui demande une version plus ancienne d’IIS.

**Basculement automatique**. Si vous activez le basculement automatique pour un emplacement de déploiement, App Service fera basculer l’application web en production automatiquement lorsque vous enverrez une mise à jour sur cet emplacement. Pour plus d’informations, consultez [Déployer vers des emplacements intermédiaires pour les applications Web dans Azure App Service](web-sites-staged-publishing.md).

### <a name="debugging"></a>Débogage
**Débogage à distance**. Active le débogage distant. Quand cette option est activée, vous pouvez utiliser le débogueur distant de Visual Studio pour vous connecter directement à votre application web. Le débogage à distance reste activé pendant 48 heures. 

### <a name="app-settings"></a>Paramètres de l’application
Cette section contient des paires nom/valeur qui seront chargées par votre application web au démarrage. 

* Dans le cas des applications .NET, ces paramètres sont inclus dans les `AppSettings` de votre configuration .NET au moment de l’exécution, en remplacement des paramètres existants. 
* Les applications PHP, Python, Java et Node peuvent accéder à ces paramètres sous forme de variables d'environnement au moment de l'exécution. Pour chaque paramètre d'application, deux variables d'environnement sont créées : l'une avec le nom spécifié par l'entrée du paramètre d'application, et l'autre avec le préfixe APPSETTING_. Elles contiennent toutes les deux la même valeur.

### <a name="connection-strings"></a>Chaînes de connexion
Chaînes de connexion des ressources liées. 

Pour les applications .NET, ces chaînes de connexion sont incluses dans les paramètres des `connectionStrings` de votre configuration .NET au moment de l’exécution, en remplacement des entrées existantes où la clé est identique à celle du nom de la base de données liée. 

Pour les applications PHP, Python, Java et Node, ces paramètres sont disponibles en tant que variables d'environnement au moment de l'exécution, avec le type de connexion comme préfixe. Les préfixes de la variable d'environnement sont les suivants : 

* SQL Server : `SQLCONNSTR_`
* MySQL : `MYSQLCONNSTR_`
* Base de données SQL : `SQLAZURECONNSTR_`
* Personnalisé : `CUSTOMCONNSTR_`

Par exemple, si une chaîne de connexion MySql se nomme `connectionstring1`, elle est accessible par le biais de la variable d’environnement `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Documents par défaut
Le document par défaut est la page web qui s’affiche à l’URL racine pour un site web.  Le premier fichier correspondant dans la liste est utilisé. 

Les applications web peuvent utiliser des modules qui effectuent un routage en fonction de l’URL, au lieu de proposer du contenu statique. Dans ce cas, il n’existe pas de document par défaut.    

### <a name="handler-mappings"></a>Mappages de gestionnaires
Utilisez cette zone pour ajouter des processeurs de script personnalisés et gérer les requêtes portant sur des extensions de fichiers spécifiques. 

* **Extension**. Extension de fichier à gérer, par exemple *.php ou handler.fcgi. 
* **Chemin d'accès du processeur de script**. Chemin d'accès absolu du processeur de script. Les requêtes de fichiers qui correspondent à l'extension de fichier sont traitées par le processeur de script. Utilisez le chemin `D:\home\site\wwwroot` pour faire référence au répertoire racine de votre application Web.
* **Arguments supplémentaires**. Arguments de ligne de commande facultatifs pour le processeur de script. 

### <a name="virtual-applications-and-directories"></a>Applications et répertoires virtuels
Pour configurer des applications et des répertoires virtuels, spécifiez chaque répertoire virtuel et son chemin d’accès physique correspondant par rapport à la racine du site web. Vous pouvez éventuellement cocher la case **Application** pour marquer un répertoire virtuel comme application.

## <a name="enabling-diagnostic-logs"></a>Activation des journaux de diagnostic
Pour activer les journaux de diagnostic, procédez comme suit :

1. Dans le panneau de votre application Web, cliquez sur **Tous les paramètres**.
2. Cliquez sur **Journaux de diagnostic**. 

Options liées à l'écriture des journaux de diagnostic à partir d'une application web qui prend en charge la journalisation : 

* **Journalisation des applications**. Enregistre des journaux d’application dans le système de fichiers. La journalisation s’étend sur une période de 12 heures. 

**Niveau**. Lorsque la journalisation des applications est activée, cette option indique la quantité d’informations qui sera enregistrée (Erreur, Avertissement, Information ou Détaillé).

**Journalisation du serveur Web**. Les journaux sont enregistrés au format de fichier journal étendu W3C. 

**Messages d’erreur détaillés**. Enregistre des messages d’erreur détaillés dans des fichiers .htm. Les fichiers sont enregistrés sous /LogFiles/DetailedErrors. 

**Suivi des demandes ayant échoué**. Enregistre les demandes ayant échoué dans des fichiers XML. Les fichiers sont enregistrés sous /LogFiles/W3SVC*xxx*, où xxx est un identificateur unique. Ce dossier contient un fichier XSL et un ou plusieurs fichiers XML. Veillez à télécharger le fichier XSL, car il fournit des fonctionnalités de mise en forme et de filtrage du contenu des fichiers XML.

Pour afficher les fichiers journaux, vous devez créer des informations d’identification FTP, comme suit :

1. Dans le panneau de votre application Web, cliquez sur **Tous les paramètres**.
2. Cliquez sur **Informations d’identification du déploiement**.
3. Entrez un nom d'utilisateur et un mot de passe.
4. Cliquez sur **Enregistrer**.

![Définir les informations d’identification de déploiement][configure03]

Le nom d’utilisateur FTP complet est « app\username », où *app* représente le nom de votre application web. Le nom d’utilisateur est répertorié dans le panneau Application Web, sous **Essentials**.

![Informations d’identification de déploiement FTP][configure02]

## <a name="other-configuration-tasks"></a>Autres tâches de configuration
### <a name="ssl"></a>SSL
En mode De base ou Standard, vous pouvez télécharger des certificats SSL pour un domaine personnalisé. Pour plus d’informations, consultez [Activer le protocole HTTPS pour une application web](app-service-web-tutorial-custom-ssl.md). 

Pour afficher vos certificats téléchargés, cliquez sur **Tous les paramètres** > **Domaines personnalisés et SSL**.

### <a name="domain-names"></a>Noms de domaine
Ajouter des noms de domaine personnalisés pour votre application web. Pour plus d’informations, consultez [Configurer un nom de domaine personnalisé pour une application web dans Azure App Service](app-service-web-tutorial-custom-domain.md).

Pour afficher vos noms de domaine, cliquez sur **Tous les paramètres** > **Domaines personnalisés et SSL**.

### <a name="deployments"></a>Déploiements
* Configurer un déploiement continu Consultez [Utilisation de Git pour déployer des applications web dans Azure App Service](app-service-deploy-local-git.md).
* Emplacements de déploiement Consultez [Deploy to Staging Environments for Web Apps in Azure App Service (Procéder à des déploiements sur des environnements intermédiaires pour les applications web dans Azure App Service)].

Pour afficher vos emplacements de déploiement, cliquez sur **Tous les paramètres** > **Emplacements de déploiement**.

### <a name="monitoring"></a>Surveillance
En mode De base ou Standard, vous pouvez tester la disponibilité des points de terminaison HTTP ou HTTPS à partir de trois emplacements géo-distribués au maximum. Un test de surveillance échoue si le code de réponse HTTP est une erreur (4xx ou 5xx) ou si la réponse prend plus de 30 secondes. Un point de terminaison est considéré comme disponible s'il passe avec succès les différents tests de surveillance à partir de tous les emplacements spécifiés. 

Pour plus d’informations, consultez [Surveillance de l’état d’un point de terminaison Web].

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service], où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* [Configuration d’un nom de domaine personnalisé dans Azure App Service]
* [Activer le protocole HTTPS pour une application dans Azure App Service]
* [Faire évoluer une application Web dans Azure App Service]
* [Surveiller les applications Web dans Azure App Service]

<!-- URL List -->

[ASP.NET SignalR]: http://www.asp.net/signalr
[portail Azure]: https://portal.azure.com/
[Configuration d’un nom de domaine personnalisé dans Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Deploy to Staging Environments for Web Apps in Azure App Service (Procéder à des déploiements sur des environnements intermédiaires pour les applications web dans Azure App Service)]: ./web-sites-staged-publishing.md
[Activer le protocole HTTPS pour une application dans Azure App Service]: ./app-service-web-tutorial-custom-ssl.md
[Surveillance de l’état d’un point de terminaison Web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Surveiller les applications Web dans Azure App Service]: ./web-sites-monitor.md
[mode pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Faire évoluer une application Web dans Azure App Service]: ./web-sites-scale.md
[Essayer App Service]: https://azure.microsoft.com/try/app-service/

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
