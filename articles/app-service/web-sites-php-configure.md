---
title: Configurer PHP dans Azure App Service Web Apps | Microsoft Docs
description: "Découvrez comment configurer l’installation PHP par défaut ou ajouter une installation PHP personnalisée pour Web Apps dans Azure App Service."
services: app-service
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 0467707a46709674d3f5de3346ad242af5c9dcb8
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Configurer PHP dans Azure App Service Web Apps
## <a name="introduction"></a>Introduction
Ce guide vous explique comment configurer le runtime PHP intégré pour Web Apps dans [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), fournir un runtime PHP personnalisé et activer des extensions. Pour utiliser App Service, souscrivez à la [version d’évaluation gratuite]. Pour tirer le meilleur parti de ce guide, commencez par créer une application web PHP dans App Service.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Modification de la version intégrée de PHP
Par défaut, PHP 5.6 est installé et immédiatement utilisable lorsque vous créez une application web App Service. Pour afficher la révision de version disponible, sa configuration par défaut et les extensions activées, la méthode idéale consiste à déployer un script qui appelle la fonction [phpinfo()] .

Les versions PHP 5.6 et PHP 7.0 sont également disponibles, mais ne sont pas activées par défaut. Pour mettre à jour la version de PHP, procédez au choix comme suit :

### <a name="azure-portal"></a>Portail Azure
1. Accédez à votre application web dans le [portail Azure](https://portal.azure.com) , puis cliquez sur le bouton **Paramètres** .
   
    ![Paramètres d’application web][settings-button]
2. Dans le panneau **Paramètres**, sélectionnez **Paramètres de l’application**, puis choisissez la nouvelle version de PHP.
   
    ![Paramètres de l’application][application-settings]
3. Cliquez sur le bouton **Enregistrer** en haut du panneau **Paramètres de l’application web**.
   
    ![Enregistrer les paramètres de configuration][save-button]

### <a name="azure-powershell-windows"></a>Azure PowerShell (Windows)
1. Ouvrez Azure PowerShell, et connectez-vous à votre compte.
   
        PS C:\> Login-AzureRmAccount
2. Configurez la version de PHP pour l’application web.
   
        PS C:\> Set-AzureWebsite -PhpVersion {5.5 | 5.6 | 7.0} -Name {app-name}
3. La version de PHP est maintenant définie. Vous pouvez confirmer les paramètres suivants :
   
        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-command-line-interface-linux-mac-windows"></a>Interface en ligne de commande Azure (Linux, Mac et Windows)
L’interface de ligne de commande Azure nécessite l’installation de **Node.js** sur votre ordinateur.

1. Ouvrez Terminal et connectez-vous à votre compte.
   
        azure login
2. Configurez la version de PHP pour l’application web.
   
        azure site set --php-version {5.5 | 5.6 | 7.0} {app-name}

3. La version de PHP est maintenant définie. Vous pouvez confirmer les paramètres suivants :
   
        azure site show {app-name}

> [!NOTE] 
> Les commandes [Azure CLI 2.0](https://github.com/Azure/azure-cli) équivalentes à celle ci-dessus sont :
>
>

    az login
    az appservice web config update --php-version {5.5 | 5.6 | 7.0} -g {resource-group-name} -n {app-name}
    az appservice web config show -g {resource-group-name} -n {app-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Modification des configurations PHP intégrées
Quel que soit le runtime PHP intégré, vous pouvez modifier toute option de configuration en procédant comme indiqué ci-dessous. (Pour plus d’informations sur les directives de php.ini, consultez la page [Liste des directives du php.ini].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Modification des paramètres de configuration PHP\_INI\_USER, PHP\_INI\_PERDIR et PHP\_INI\_ALL
1. Ajoutez un fichier [.user.ini] à votre répertoire racine.
2. Ajoutez des paramètres de configuration au fichier `.user.ini` en utilisant la même syntaxe que pour le fichier `php.ini`. Par exemple, si vous souhaitez activer le paramètre `display_errors` et régler le paramètre `upload_max_filesize` sur 10M, votre fichier `.user.ini` doit contenir le texte suivant :
   
        ; Example Settings
        display_errors=On
        upload_max_filesize=10M
   
        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
3. Déployez votre application web.
4. Redémarrez l’application web. (Le redémarrage est nécessaire, car la fréquence à laquelle PHP lit les fichiers `.user.ini` est régie par le paramètre `user_ini.cache_ttl`, un paramètre système réglé sur 300 secondes (5 minutes) par défaut. Le redémarrage de l’application web force PHP à lire les nouveaux paramètres dans le fichier `.user.ini` .)

Au lieu d’un fichier `.user.ini`, vous pouvez utiliser la fonction [ini_set()] dans des scripts pour définir les options de configuration qui ne sont pas des directives de niveau système.

### <a name="changing-phpinisystem-configuration-settings"></a>Modification des paramètres de configuration PHP\_INI\_SYSTEM
1. Ajoutez un paramètre d’application à votre application web avec la clé `PHP_INI_SCAN_DIR` et valeur `d:\home\site\ini`.
2. Créez un fichier `settings.ini` à l’aide de la Console Kudu (http://&lt;nom-site&gt;.scm.azurewebsite.net) dans le répertoire `d:\home\site\ini`.
3. Ajoutez des paramètres de configuration au fichier `settings.ini` en utilisant la même syntaxe que pour un fichier php.ini. Par exemple, si vous souhaitiez pointer le paramètre `curl.cainfo` vers un fichier `*.crt` et régler le paramètre « wincache.maxfilesize » à 512 ko, votre fichier `settings.ini` doit contenir le texte :
   
        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
4. Redémarrez votre application Web pour charger les modifications.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Activation d’extensions dans le runtime PHP par défaut
Comme indiqué dans la section précédente, la méthode idéale pour afficher la version PHP par défaut, sa configuration par défaut et les extensions activées consiste à déployer un script qui appelle [phpinfo()]. Pour activer des extensions supplémentaires, procédez comme suit :

### <a name="configure-via-ini-settings"></a>Configuration par des paramètres ini
1. Ajoutez un répertoire `ext` dans le répertoire `d:\home\site`.
2. Placez les fichiers `.dll` dans le répertoire `ext` (par exemple, `php_xdebug.dll`). Assurez-vous que les extensions sont compatibles avec la version par défaut de PHP ainsi qu'avec VC9 et NTS (Non-Thread Safe).
3. Ajoutez un paramètre d’application à votre application web avec la clé `PHP_INI_SCAN_DIR` et valeur `d:\home\site\ini`.
4. Dans `d:\home\site\ini`, créez un fichier `ini` appelé `extensions.ini`.
5. Ajoutez des paramètres de configuration au fichier `extensions.ini` en utilisant la même syntaxe que pour un fichier php.ini. Par exemple, si vous souhaitez activer les extensions MongoDB et XDebug, votre fichier `extensions.ini` doit contenir le texte :
   
        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
6. Redémarrez votre application Web pour charger les modifications.

### <a name="configure-via-app-setting"></a>Configuration par un paramètre d’application
1. Ajoutez un répertoire `bin` au répertoire racine.
2. Placez les fichiers `.dll` dans le répertoire `bin` (par exemple, `php_xdebug.dll`). Assurez-vous que les extensions sont compatibles avec la version par défaut de PHP ainsi qu'avec VC9 et NTS (Non-Thread Safe).
3. Déployez votre application web.
4. Accédez à votre application web dans le portail Azure, puis cliquez sur le bouton **Paramètres** .
   
    ![Paramètres d’application web][settings-button]
5. Dans le panneau **Paramètres**, sélectionnez **Paramètres de l’application**, puis accédez à la section **Paramètres de l’application**.
6. Dans la section **Paramètres de l’application**, créez une clé **PHP_EXTENSIONS**. La valeur de cette clé est un chemin d’accès relatif à la racine du site web : **bin\your-ext-file**.
   
    ![Activer une extension dans les paramètres d'application][php-extensions]
7. Cliquez sur le bouton **Enregistrer** en haut du panneau **Paramètres de l’application web**.
   
    ![Enregistrer les paramètres de configuration][save-button]

Les extensions Zend sont également prises en charge à l’aide d’une clé **PHP_ZENDEXTENSIONS**. Pour activer plusieurs extensions, insérez une liste de fichiers `.dll` séparés par des virgules pour la valeur de paramètre d’application.

## <a name="how-to-use-a-custom-php-runtime"></a>Utilisation d’un runtime PHP personnalisé
Au lieu du runtime PHP par défaut, App Service Web Apps peut utiliser un runtime PHP que vous fournissez pour exécuter des scripts PHP. Le runtime en question peut être configuré par un fichier `php.ini` que vous avez également déclaré. Pour utiliser un runtime PHP personnalisé avec Web Apps, suivez la procédure ci-après.

1. Obtenez une version de PHP pour Windows, compatible avec NTS (Non-Thread-Safe), VC9 ou VC11. Les versions récentes de PHP pour Windows sont disponibles à l’adresse suivante : [http://windows.php.net/download/]. Les versions précédentes se trouvent dans l’archive disponible ici : [http://windows.php.net/downloads/releases/archives/].
2. Modifiez le fichier `php.ini` de votre runtime. Notez que tout paramètre de configuration correspondant à une directive de niveau système uniquement est ignoré par Web Apps. Pour plus d'informations sur les directives de niveau système uniquement, consultez la page [Liste des directives du php.ini].
3. Vous pouvez éventuellement ajouter des extensions à votre runtime PHP et les activer dans le fichier `php.ini` .
4. Ajoutez un répertoire `bin` à votre répertoire racine, puis placez-y le répertoire contenant votre runtime PHP (par exemple, `bin\php`).
5. Déployez votre application web.
6. Accédez à votre application web dans le portail Azure, puis cliquez sur le bouton **Paramètres** .
   
    ![Paramètres d’application web][settings-button]
7. Dans le panneau **Paramètres**, sélectionnez **Paramètres de l’application**, puis accédez à la section **Mappages de gestionnaires**. Ajoutez `*.php` au champ Extension, puis ajoutez le chemin d’accès à l’exécutable `php-cgi.exe`. Si vous placez votre runtime PHP dans le répertoire `bin` situé à la racine de votre application, le chemin d’accès sera `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
   
    ![Indiquer le gestionnaire dans les mappages][handler-mappings]
8. Cliquez sur le bouton **Enregistrer** en haut du panneau **Paramètres de l’application web**.
   
    ![Enregistrer les paramètres de configuration][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Activation de l’automatisation du Compositeur dans Azure
Par défaut, App Service ne fait rien avec composer.json, si vous en avez un dans votre projet PHP. Si vous utilisez le [déploiement Git](app-service-deploy-local-git.md), vous pouvez activer le traitement de composer.json pendant `git push` en activant l’extension du Compositeur.

> [!NOTE]
> Vous pouvez [voter pour la prise en charge haut de gamme de Compositeur dans App Service ici](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
> 
> 

1. Dans le panneau de l’application web PHP (dans le [portail Azure](https://portal.azure.com)), cliquez sur **Outils** > **Extensions**.
   
    ![Panneau de paramètres du portail Azure pour activer l’automatisation du Compositeur dans Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Cliquez sur **Ajouter** puis sur **Compositeur**.
   
    ![Ajouter l’extension du Compositeur pour activer l’automatisation du Compositeur dans Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Cliquez sur **OK** pour accepter les conditions juridiques. Cliquez à nouveau sur **OK** pour ajouter l’extension.
   
    Le panneau **Extensions installées** affiche désormais l’extension du Compositeur.  
    ![Accepter les conditions juridiques pour activer l’automatisation de Compositeur dans Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Maintenant, exécutez `git add`, `git commit` et `git push` comme dans la section précédente. Vous verrez alors que Compositeur installe des interdépendances définies dans composer.json.
   
    ![Déploiement Git avec automatisation du Compositeur dans Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez le [Centre pour développeurs PHP](/develop/php/).

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](https://azure.microsoft.com/try/app-service/), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

[version d’évaluation gratuite]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Liste des directives du php.ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png

