---
title: "Déployer votre application sur Azure App Service avec un fichier ZIP | Microsoft Docs"
description: "Découvrez comment déployer votre application sur Azure App Service avec un fichier ZIP."
services: app-service
documentationcenter: 
author: cephalin
manager: cfowler
editor: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: cephalin;sisirap
ms.openlocfilehash: a0e4df0ef0a1c873f1efcac1d8dbfe3cada18218
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-file"></a>Déployer votre application sur Azure App Service avec un fichier ZIP

Cet article montre comment utiliser un fichier ZIP pour déployer votre application web sur [Azure App Service](app-service-web-overview.md). 

Ce déploiement de fichier ZIP utilise le même service Kudu que celui qui pilote les déploiements continus basés sur l’intégration. Kudu prend en charge les fonctionnalités suivantes pour le déploiement de fichier ZIP : 

- Suppression des fichiers laissés par un déploiement précédent
- Option pour activer le processus de génération par défaut, qui inclut la restauration de package
- [Personnalisation du déploiement](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings), notamment exécution de scripts de déploiement  
- Journaux de déploiement 

Pour plus d’informations, consultez la [documentation Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

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

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="upload-zip-file-to-cloud-shell"></a>Charger le fichier ZIP vers Cloud Shell

Si vous choisissez plutôt d’exécuter Azure CLI à partir de votre terminal local, ignorez cette étape.

Suivez les étapes ci-après pour charger votre fichier ZIP vers Cloud Shell. 

[!INCLUDE [app-service-web-upload-zip.md](../../includes/app-service-web-upload-zip-no-h.md)]

Pour plus d’informations, consultez [Conserver des fichiers dans Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

## <a name="deploy-zip-file"></a>Déployer un fichier Zip

Déployez le fichier ZIP chargé sur votre application web à l’aide de la commande [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az_webapp_deployment_source_config_zip). Si vous choisissez de ne pas utiliser Cloud Shell, vérifiez que votre version d’Azure CLI est 2.0.21 ou version ultérieure. Pour vérifier votre version, exécutez la commande `az --version` dans la fenêtre de terminal locale. 

L’exemple suivant déploie le fichier ZIP que vous avez chargé. Quand vous utilisez une installation locale d’Azure CLI, spécifiez le chemin de votre fichier ZIP local pour `--src`.   

```azurecli-interactive
az webapp deployment source config-zip --resource-group myResouceGroup --name <app_name> --src clouddrive/<filename>.zip
```

Cette commande déploie les fichiers et répertoires du fichier ZIP vers votre dossier d’applications App Service par défaut (`\home\site\wwwroot`), puis redémarre l’application. Si un processus de génération personnalisé supplémentaire est configuré, il est également exécuté. Pour plus d’informations, consultez la [documentation Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Pour afficher la liste des déploiements pour cette application, vous devez utiliser les API REST (voir la section suivante). 

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="next-steps"></a>Étapes suivantes

Pour des scénarios de déploiement plus avancés, consultez [Déploiement Git local vers Azure App Service](app-service-deploy-local-git.md). Le déploiement GIT vers Azure autorise le contrôle de version, la restauration du package, MSBuild et bien plus encore.

## <a name="more-resources"></a>Autres ressources

* [Kudu : Deploying from a zip file (Déploiement à partir d’un fichier zip)](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Informations d’identification du déploiement d’Azure App Service](app-service-deploy-ftp.md)
