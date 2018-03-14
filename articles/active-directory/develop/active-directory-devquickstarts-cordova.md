---
title: "Bien démarrer avec Azure Cordova | Microsoft Docs"
description: "Création d’une application Cordova qui s’intègre avec Azure AD pour la connexion et appelle les API protégées par Azure AD en utilisant OAuth."
services: active-directory
documentationcenter: 
author: vibronet
manager: mtillman
editor: 
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: vittorib
ms.custom: aaddev
ms.openlocfilehash: d6f1d545265f0965a03afb23e5791cdd5e5e379c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-ad-cordova-getting-started"></a>Bien démarrer avec Azure AD Cordova
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Apache Cordova permet de développer des applications HTML5/JavaScript exécutables sur des appareils mobiles en tant qu’applications natives à part entière. Avec Azure Active Directory (Azure AD), vous pouvez ajouter des fonctionnalités d’authentification d’entreprise à vos applications Cordova.

Un plug-in Cordova encapsule des kits de développement logiciels (SDK) natifs Azure AD sur iOS, Android, Windows Store et Windows Phone. En utilisant ce plug-in, vous pouvez améliorer votre application pour qu’elle prenne en charge la connexion avec les comptes Windows Server Active Directory de vos utilisateurs, qu’elle puisse accéder aux API Office 365 et Azure, et même qu’elle contribue à protéger les appels à votre API web personnalisée.

Dans ce didacticiel, nous allons utiliser le plug-in Apache Cordova pour la bibliothèque d’authentification Active Directory (ADAL) afin d’améliorer une application simple en ajoutant les fonctionnalités suivantes :

* Avec quelques lignes de code seulement, authentifier un utilisateur et obtenir un jeton.
* Utiliser ce jeton pour appeler l’API Graph afin d’interroger ce répertoire et d’afficher les résultats.  
* Utiliser le cache de jetons ADAL pour limiter les invites d’authentification de l’utilisateur.

Pour apporter ces améliorations, vous devez :

1. inscrivez une application auprès d’Azure AD ;
2. ajouter du code pour que votre application demande des jetons ;
3. ajouter du code pour utiliser le jeton pour l’interrogation de l’API Graph et afficher les résultats ;
4. créer le projet de déploiement Cordova avec toutes les plateformes à cibler, ajouter le plug-in Cordova ADAL, puis tester la solution dans des émulateurs.

## <a name="prerequisites"></a>Prérequis
Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un client Azure AD où vous avez un compte disposant de droits de développement d’application.
* Un environnement de développement configuré pour exécuter Apache Cordova.  

Si vous disposez déjà de ces éléments, passez directement à l’étape 1.

Si vous n’avez pas de client Azure AD, suivez la [procédure permettant d’en obtenir un](active-directory-howto-tenant.md).

Si Apache Cordova n’est pas configuré sur votre ordinateur, installez les éléments suivants :

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.JS](https://nodejs.org/download/)
* [Cordova CLI](https://cordova.apache.org/) (peut être installé facilement via le Gestionnaire de package NPM : `npm install -g cordova`)

Les installations précédentes doivent en principe fonctionner aussi bien sur PC que sur Mac.

Chaque plateforme cible présente une configuration requise différente :

* Pour générer et exécuter une application pour une tablette/un PC Windows ou Windows Phone :
  * Installez [Visual Studio 2013 pour Windows Update 2 ou version ultérieure](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express ou autre version) ou [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* Pour générer et exécuter une application pour iOS :

  * Installez Xcode 6.x ou version ultérieure. Téléchargez-le à partir du [site Apple Developer](http://developer.apple.com/downloads) ou du [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Installez [ios-sim](https://www.npmjs.org/package/ios-sim). Vous pouvez l’utiliser pour démarrer des applications iOS dans iOS Simulator à partir de la ligne de commande. (Vous pouvez l’installer facilement via le terminal : `npm install -g ios-sim`.)
* Pour générer et exécuter une application pour Android :

  * Installez le [Kit de développement Java (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou ultérieur. Assurez-vous que la variable d’environnement `JAVA_HOME` est correctement définie en fonction du chemin d’installation du JDK (par exemple, C:\Program Files\Java\jdk1.7.0_75).
  * Installez le [Kit de développement logiciel (SDK) Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) et ajoutez l’emplacement `<android-sdk-location>\tools` (par exemple, C:\tools\Android\android-sdk\tools) pour votre variable d’environnement `PATH`.
  * Ouvrez Android SDK Manager (par exemple, via le terminal : `android`) et installez :
    * *Android 5.0.1 (API 21)*
    * *Android SDK Build Tools* version 19.1.0 ou ultérieure
    * *Android Support Repository* (modules complémentaires)

  Le Kit de développement logiciel (SDK) Android ne fournit pas d’instance d’émulateur par défaut. Si vous souhaitez exécuter l’application Android sur un émulateur, créez une instance en exécutant `android avd` à partir du terminal, puis en sélectionnant **Create** (Créer). Nous recommandons un niveau d’API de 19 ou plus. Pour plus d’informations sur les options de création et l’émulateur Android, consultez [AVD Manager](http://developer.android.com/tools/help/avd-manager.html) sur le site Android.

## <a name="step-1-register-an-application-with-azure-ad"></a>Étape 1 : Inscrire une application auprès d’Azure AD
Cette étape est facultative. Ce didacticiel fournit des valeurs préconfigurées que vous pouvez utiliser pour voir l’exemple à l’œuvre sans effectuer d’approvisionnement dans votre propre client. Toutefois, nous vous recommandons d’effectuer cette étape et de vous familiariser avec le processus, car vous devrez y faire appel lorsque vous créerez vos propres applications.

Azure AD émet uniquement des jetons pour les applications connues. Avant de pouvoir utiliser Azure AD à partir de votre application, vous devez créer une entrée pour elle dans votre client. Pour inscrire une nouvelle application dans votre client :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans la barre supérieure, cliquez sur votre compte. Dans la liste **Répertoire**, choisissez le locataire Azure AD auprès duquel vous voulez inscrire votre application.
3. Cliquez sur **Tous les services** dans le volet de gauche, puis sélectionnez **Azure Active Directory**.
4. Cliquez sur **Inscriptions des applications**, puis sélectionnez **Ajouter**.
5. Suivez les invites à l’écran et créez une **application cliente native**. (Bien que les applications Cordova soient basées sur le langage HTML, nous créons ici une application cliente native. L’option **Application cliente native** doit être sélectionnée, sinon l’application ne fonctionnera pas.)
  * **Nom** décrit votre application pour les utilisateurs.
  * **URI de redirection** correspond à l’URI utilisé pour renvoyer les jetons à votre application. Entrez **http://MyDirectorySearcherApp**.

Une fois l’inscription terminée, Azure AD affecte un ID d’application unique à votre application. Vous aurez besoin de cette valeur dans les sections suivantes. Vous le trouverez dans l’onglet Application de la nouvelle application.

Pour exécuter `DirSearchClient Sample`, autorisez la nouvelle application à interroger l’API Azure AD Graph :

1. Dans la page **Paramètres**, sélectionnez **Autorisations requises**, puis **Ajouter**.  
2. Pour l’application Azure Active Directory, sélectionnez l’API **Microsoft Graph**, puis ajoutez l’autorisation **Access the directory as the signed-in user** (Accéder au répertoire en tant qu’utilisateur connecté) sous **Autorisations déléguées**.  Cela permet à votre application d’interroger l’API Graph concernant les utilisateurs.

## <a name="step-2-clone-the-sample-app-repository"></a>Étape 2 : Cloner l’exemple de référentiel d’application
À partir de votre environnement ou de la ligne de commande, tapez la commande suivante :

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>Étape 3 : Créer l’application Cordova
Il existe plusieurs moyens de créer des applications Cordova. Dans ce didacticiel, nous allons utiliser l’interface de ligne de commande (CLI) Cordova.

1. À partir de votre environnement ou de la ligne de commande, tapez la commande suivante :

        cordova create DirSearchClient

   Cette commande permet de créer la structure de dossiers et la structure du projet Cordova.

2. Accédez au nouveau dossier DirSearchClient :

        cd .\DirSearchClient

3. Copiez le contenu du projet de démarrage dans le sous-dossier www en utilisant un gestionnaire de fichiers ou la commande suivante dans votre interpréteur de commandes :

  * Windows : `xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac : `cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Ajoutez le plug-in d’autorisation (whitelist). Celui-ci est nécessaire pour appeler l’API Graph.

        cordova plugin add cordova-plugin-whitelist

5. Ajoutez toutes les plateformes que vous souhaitez prendre en charge. Pour avoir un exemple fonctionnel, vous devez exécuter au moins une des commandes ci-dessous. Notez que vous ne pouvez pas émuler iOS sur Windows ou Windows sur un Mac.

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Ajoutez la bibliothèque ADAL pour le plug-in Cordova dans votre projet :

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>Étape 4 : Ajouter le code pour authentifier les utilisateurs et obtenir des jetons d’Azure AD
L’application que vous développez dans ce didacticiel fournira une fonctionnalité simple de recherche dans l’annuaire. L’utilisateur pourra taper l’alias de n’importe quel utilisateur de l’annuaire pour visualiser certains attributs de base. Le projet de démarrage contient la définition de l’interface utilisateur de base de l’application (dans www/index.html) et la génération de modèles automatique qui associe les cycles d’événements des applications, les liaisons d’interface utilisateur et la logique d’affichage des résultats (dans www/js/index.js). Vous n’avez plus qu’à ajouter la logique d’implémentation des tâches d’identité.

La première chose à faire dans votre code est d’introduire les valeurs de protocole utilisées par Azure AD pour identifier votre application et les ressources que vous ciblez. Ces valeurs sont ensuite utilisées pour créer les demandes de jeton. Insérez l’extrait de code suivant en haut du fichier index.js :

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Les valeurs `redirectUri` et `clientId` doivent correspondre à celles décrivant votre application dans Azure AD. Vous pouvez les trouver dans l’onglet **Configurer** du portail Azure, comme décrit à l’étape 1 plus haut dans ce didacticiel.

> [!NOTE]
> Si vous avez choisi de ne pas inscrire une nouvelle application dans votre propre client, vous pouvez simplement coller les valeurs préconfigurées sans les modifier. Vous pourrez alors voir l’exemple exécuté. Cependant, notez que vous devez toujours créer votre propre entrée pour vos applications de production.

Ensuite, ajoutez le code de demande de jeton. Insérez l’extrait de code suivant entre les définitions `search` et `renderData` :

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Examinons cette fonction en la décomposant en deux parties principales.
Cet exemple est conçu pour fonctionner avec n’importe quel client. Il utilise le point de terminaison « /common », ce qui permet à l’utilisateur d’entrer n’importe quel compte au moment de l’authentification et de rediriger la demande au client auquel elle appartient.

La première partie de la méthode inspecte le cache de la bibliothèque ADAL pour voir si un jeton y est déjà stocké. Si c’est le cas, elle utilise les clients d’où le jeton provient pour réinitialiser la bibliothèque ADAL. Cela est nécessaire pour éviter les invites supplémentaires, car l’utilisation de « /common » génère toujours une invite demandant à l’utilisateur d’entrer un nouveau compte.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
La deuxième partie de la méthode effectue la demande de jeton appropriée. La méthode `acquireTokenSilentAsync` demande à la bibliothèque ADAL de renvoyer un jeton pour la ressource spécifiée sans afficher d’expérience utilisateur. Cela peut se produire si le cache a déjà un jeton d’accès stocké approprié ou si un jeton d’actualisation peut être utilisé pour obtenir un nouveau jeton d’accès sans afficher d’invite. Si cette tentative échoue, nous revenons à `acquireTokenAsync`, qui affichera une invite pour demander à l’utilisateur de s’authentifier.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Maintenant que nous avons le jeton, nous pouvons enfin appeler l’API Graph et effectuer la requête de recherche souhaitée. Insérez l’extrait de code suivant en dessous de la définition `authenticate` :

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Les fichiers de départ fournissaient une expérience utilisateur simple pour la saisie de l’alias d’un utilisateur dans une zone de texte. Cette méthode utilise cette valeur pour construire une requête, l’associer au jeton d’accès, l’envoyer à Microsoft Graph et analyser les résultats. La méthode `renderData`, déjà présente dans le fichier de départ, s’occupe de visualiser les résultats.

## <a name="step-5-run-the-app"></a>Étape 5 : Exécuter l’application
Votre application est maintenant prête à être exécutée. Son fonctionnement est simple : lorsque l’application démarre, entrez l’alias de l’utilisateur que vous souhaitez rechercher, puis cliquez sur le bouton. Vous êtes invité à vous authentifier. Une fois l’authentification et la recherche réussies, les attributs de l’utilisateur recherché s’affichent.

Les exécutions suivantes effectuent la recherche sans afficher d’invite, grâce à la présence dans le cache du jeton obtenu précédemment.

Les étapes concrètes pour l’exécution de l’application varient selon la plateforme.

### <a name="windows-10"></a>Windows 10
   Tablette/PC : `cordova run windows --archs=x64 -- --appx=uap`

   Mobile (nécessite un appareil Windows 10 Mobile connecté à un PC) : `cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Lors de la première exécution, vous pouvez être invité à vous connecter pour obtenir une licence de développeur. Pour plus d’informations, consultez [Obtenir une licence de développeur](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Tablette/PC Windows 8.1
   `cordova run windows`

   > [!NOTE]
   > Lors de la première exécution, vous pouvez être invité à vous connecter pour obtenir une licence de développeur. Pour plus d’informations, consultez [Obtenir une licence de développeur](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8.1
   Pour une exécution sur un appareil connecté : `cordova run windows --device -- --phone`

   Pour une exécution sur l’émulateur par défaut : `cordova emulate windows -- --phone`

   Utilisez `cordova run windows --list -- --phone` pour voir toutes les cibles disponibles et `cordova run windows --target=<target_name> -- --phone` pour exécuter l’application sur un appareil ou un émulateur spécifique (par exemple, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

### <a name="android"></a>Android
   Pour une exécution sur un appareil connecté : `cordova run android --device`

   Pour une exécution sur l’émulateur par défaut : `cordova emulate android`

   Assurez-vous que vous avez créé une instance d’émulateur à l’aide d’AVD Manager, comme expliqué plus haut dans la section Composants requis.

   Utilisez `cordova run android --list` pour voir toutes les cibles disponibles et `cordova run android --target=<target_name>` pour exécuter l’application sur un appareil ou un émulateur spécifique (par exemple, `cordova run android --target="Nexus4_emulator"`).

### <a name="ios"></a>iOS
   Pour une exécution sur un appareil connecté : `cordova run ios --device`

   Pour une exécution sur l’émulateur par défaut : `cordova emulate ios`

   > [!NOTE]
   > Pour une exécution sur l’émulateur, assurez-vous que vous avez installé le package `ios-sim`. Pour plus d’informations, consultez la section Composants requis.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>étapes suivantes
Pour référence, l’exemple terminé (sans vos valeurs de configuration) est disponible dans [GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).

Vous pouvez maintenant passer à des scénarios plus avancés (et plus intéressants). Par exemple : [Sécurisation d’une API web Node.js avec Azure AD](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
