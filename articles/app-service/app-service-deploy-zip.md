---
title: Déployer votre application sur Azure App Service avec un fichier ZIP ou WAR | Microsoft Docs
description: Découvrez comment déployer votre application sur Azure App Service avec un fichier ZIP (ou un fichier WAR pour les développeurs Java).
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: cephalin;sisirap
ms.openlocfilehash: 6ecbf111bad96bce310109ac1a3e8f3bb846be6c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Déployer votre application sur Azure App Service avec un fichier ZIP ou WAR

Cet article montre comment utiliser un fichier ZIP ou WAR pour déployer votre application web sur [Azure App Service](app-service-web-overview.md). 

Ce déploiement de fichier ZIP utilise le même service Kudu que celui qui pilote les déploiements continus basés sur l’intégration. Kudu prend en charge les fonctionnalités suivantes pour le déploiement de fichier ZIP : 

- Suppression des fichiers laissés par un déploiement précédent
- Option pour activer le processus de génération par défaut, qui inclut la restauration de package
- [Personnalisation du déploiement](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), notamment exécution de scripts de déploiement  
- Journaux de déploiement 

Pour plus d’informations, consultez la [documentation Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Le fichier [WAR](https://wikipedia.org/wiki/WAR_(file_format)) est déployé sur App Service pour exécuter votre application web Java. Consultez [Déployer un fichier WAR](#deploy-war-file).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Prérequis


Pour accomplir les étapes décrites dans cet article :

* [Créez une application App Service](/azure/app-service/), ou utilisez une application créée pour un autre didacticiel.

## <a name="create-a-project-zip-file"></a>Créer un fichier ZIP de projet

>[!NOTE]
> Si vous avez téléchargé les fichiers dans un fichier ZIP, extrayez tout d’abord les fichiers. Par exemple, si vous avez téléchargé un fichier ZIP à partir de GitHub, vous ne pouvez pas déployer ce fichier tel quel. GitHub ajoute des répertoires imbriqués supplémentaires qui ne fonctionnent pas avec App Service. 
>

Dans une fenêtre de terminal locale, accédez au répertoire racine de votre projet d’application. 

Ce répertoire doit contenir le fichier d’entrée à votre application web, tel que _index.html_, _index.php_ et _app.js_. Il peut également contenir des fichiers de gestion de package comme _project.json_, _composer.json_, _package.json_, _bower.json_ et _requirements.txt_.

Créez une archive ZIP contenant tous les éléments de votre projet. La commande suivante utilise l’outil par défaut dans votre terminal :

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]

## <a name="deploy-zip-file-with-azure-cli"></a>Déployer le fichier ZIP avec Azure CLI

Vérifiez que votre version d’Azure CLI est égale ou supérieure à 2.0.21. Pour vérifier votre version, exécutez la commande `az --version` dans la fenêtre de terminal.

Déployez le fichier ZIP chargé sur votre application web à l’aide de la commande [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip).  

L’exemple suivant déploie le fichier ZIP que vous avez chargé. Quand vous utilisez une installation locale d’Azure CLI, spécifiez le chemin de votre fichier ZIP local pour `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResourceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Cette commande déploie les fichiers et répertoires du fichier ZIP vers votre dossier d’applications App Service par défaut (`\home\site\wwwroot`), puis redémarre l’application. Si un processus de génération personnalisé supplémentaire est configuré, il est également exécuté. Pour plus d’informations, consultez la [documentation Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Déployer un fichier WAR

Pour déployer un fichier WAR sur App Service, envoyez une requête POST à https://<app_name>.scm.azurewebsites.net/api/wardeploy. La requête POST doit contenir le fichier .war dans le corps du message. Les informations d’identification de déploiement pour votre application sont fournies dans la demande avec l’authentification de base HTTP. 

Pour l’authentification HTTP BASIC, vous avez besoin de vos informations d’identification de déploiement App Service. Pour découvrir comment définir les informations d’identification de votre déploiement, consultez [Définir et réinitialiser les informations d’identification de niveau utilisateur](app-service-deployment-credentials.md#userscope).

### <a name="with-curl"></a>With cURL

L’exemple suivant utilise l’outil cURL pour déployer un fichier .war. Remplacez les espaces réservés `<username>`, `<war_file_path>` et `<app_name>`. Quand vous y êtes invité par cURL, tapez le mot de passe.

```bash
curl -X POST -u <username> --data-binary @"<war_file_path>" https://<app_name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Avec PowerShell

L’exemple suivant utilise [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) pour envoyer une requête qui contient le fichier .war. Remplacez les espaces réservés `<deployment_user>`, `<deployment_password>`, `<zip_file_path>` et `<app_name>`.

```PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<war_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/wardeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

## <a name="next-steps"></a>Étapes suivantes

Pour des scénarios de déploiement plus avancés, consultez [Déploiement Git local vers Azure App Service](app-service-deploy-local-git.md). Le déploiement GIT vers Azure autorise le contrôle de version, la restauration du package, MSBuild et bien plus encore.

## <a name="more-resources"></a>Autres ressources

* [Kudu : Deploying from a zip file (Déploiement à partir d’un fichier zip)](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Informations d’identification du déploiement d’Azure App Service](app-service-deploy-ftp.md)
