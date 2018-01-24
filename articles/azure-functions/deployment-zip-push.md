---
title: "Déploiement Push de fichier Zip pour Azure Functions | Microsoft Docs"
description: "Utilisez les fonctionnalités de déploiement de fichier Zip du service de déploiement Kudu pour publier vos Azure Functions."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 12/06/2017
ms.author: glenga
ms.openlocfilehash: faddb73522200f60f18294dc43e8d235943f8bbb
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="zip-push-deployment-for-azure-functions"></a>Déploiement Push de fichier Zip pour Azure Functions 
Cet article décrit comment déployer vos fichiers projet d’application de fonction sur Azure à partir d’un fichier .zip (compressé). Vous allez découvrir comment effectuer un déploiement Push, à la fois à l’aide d’Azure CLI et des API REST. 

Azure Functions offre la gamme complète d’options d’intégration et de déploiement continu fournies par Azure App Service. Pour plus d’informations, consultez [Déploiement continu pour Azure Functions](functions-continuous-deployment.md). 

Pour accélérer l’itération au cours du développement, il est souvent plus facile de déployer les fichiers projet d’application de fonction directement à partir d’un fichier .zip compressé. Ce déploiement de fichier .zip utilise le même service Kudu que celui qui pilote les déploiements continus basés sur l’intégration, notamment :

+ Suppression des fichiers conservés suite aux déploiements antérieurs
+ Personnalisation du déploiement, notamment exécution de scripts de déploiement
+ Journaux de déploiement
+ La fonction de synchronisation se déclenche dans une application de fonction [Plan Consommation](functions-scale.md).

Pour plus d’informations, consultez la [référence sur le déploiement Push de fichiers .zip](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file). 

## <a name="deployment-zip-file-requirements"></a>Spécifications du déploiement de fichier .zip
Le fichier .zip que vous utilisez pour le déploiement Push doit contenir tous les fichiers projet dans votre application de fonction, notamment le code de votre fonction. 

>[!IMPORTANT]
> Quand vous utilisez le déploiement Push de fichier .zip, tous les fichiers d’un déploiement existant qui ne se trouvent pas dans le fichier .zip sont supprimés de votre application de fonction.  

### <a name="function-app-folder-structure"></a>Structure de dossiers d’application de fonction

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

### <a name="download-your-function-app-project"></a>Télécharger votre projet d’application de fonction

Quand vous développez sur un ordinateur local, il est facile de créer un fichier .zip du dossier de projet d’application de fonction sur votre ordinateur de développement. 

Toutefois, vous aurez peut-être créé vos fonctions à l’aide de l’éditeur dans le portail Azure. Pour télécharger votre projet d’application de fonction à partir du portail 

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre application de fonction.

2. Sous l’onglet **Vue d’ensemble**, sélectionnez **Télécharger le contenu de l’application**. Sélectionnez vos options de téléchargement, puis **Télécharger**.     

    ![Télécharger le projet d’application de fonction](./media/deployment-zip-push/download-project.png)

Le fichier .zip téléchargé est au format correct pour être republié sur votre application de fonction en utilisant le déploiement Push de fichier .zip.

Vous pouvez également télécharger un fichier .zip à partir d’un dépôt GitHub. N’oubliez pas que quand vous téléchargez un dépôt GitHub en tant que fichier .zip, GitHub ajoute un niveau de dossier supplémentaire pour la branche. Ce niveau de dossier supplémentaire signifie que vous ne pouvez pas déployer le fichier .zip directement tel que vous l’avez téléchargé à partir de GitHub. Si vous utilisez un dépôt GitHub pour tenir à jour votre application de fonction, vous devez utiliser [l’intégration continue](functions-continuous-deployment.md) pour la déployer.  

## <a name="cli"></a>Déployer à l’aide d’Azure CLI

Vous pouvez utiliser Azure CLI pour déclencher un déploiement Push. Effectuez le déploiement Push d’un fichier .zip sur votre application de fonction à l’aide de la commande [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip). Pour exécuter cette commande, vous devez utiliser Azure CLI version 2.0.21 ou ultérieure. Pour vérifier votre version d’Azure CLI, utilisez la commande `az --version`.

Dans la commande suivante, remplacez l’espace réservé `<zip_file_path>` par le chemin vers l’emplacement de votre fichier .zip. Remplacez également `<app_name>` par le nom unique de votre application de fonction. 

```azurecli-interactive
az functionapp deployment source config-zip  -g myResourceGroup -n \
<app_name> --src <zip_file_path>
```
Cette commande déploie les fichiers projet à partir du fichier .zip téléchargé sur votre application de fonction dans Azure. Ensuite, il redémarre l’application. Pour afficher la liste des déploiements pour cette application de fonction, vous devez utiliser les API REST.

Quand vous utilisez Azure CLI sur votre ordinateur local, `<zip_file_path>` est le chemin du fichier .zip sur votre ordinateur. Vous pouvez également exécuter Azure CLI dans [Azure Cloud Shell](../cloud-shell/overview.md). Quand vous utilisez Cloud Shell, vous devez d’abord charger votre fichier .zip de déploiement sur le compte Azure Files associé à votre Cloud Shell. Dans ce cas, `<zip_file_path>` est l’emplacement de stockage utilisé par votre compte Cloud Shell. Pour plus d’informations, consultez [Conserver des fichiers dans Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).


[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déploiement continu pour Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
