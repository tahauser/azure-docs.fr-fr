---
title: Comment cibler des versions du runtime Azure Functions
description: "Azure Functions prend en charge plusieurs versions du runtime. Découvrez comment spécifier la version du runtime d’une application de fonction hébergée dans Azure."
services: functions
documentationcenter: 
author: ggailey777
manager: cfowler
editor: 
ms.service: functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: glenga
ms.openlocfilehash: 6fc84642050f4b7acfa2e3c5b4518135d6a97171
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Comment cibler des versions du runtime Azure Functions

Une application de fonction s’exécute sur une version spécifique du runtime Azure Functions. Il existe deux versions majeures : [1.x et 2.x](functions-versions.md). Cet article explique comment configurer une application de fonction dans Azure pour qu’elle s’exécute sur la version de votre choix. Pour plus d’informations sur la façon de configurer un environnement de développement local pour une version spécifique, consultez [Coder et tester Azure Functions localement](functions-run-local.md).

>[!IMPORTANT]   
> Le runtime d’Azure Functions 2.0 est en préversion ; pour le moment, toutes les fonctionnalités d’Azure Functions ne sont pas prises en charge. Pour plus d’informations, consultez [Vue d’ensemble des versions du runtime Azure Functions](functions-versions.md).

## <a name="automatic-and-manual-version-updates"></a>Mises à jour de versions automatiques et manuelles

Azure Functions vous permet de cibler une version spécifique du runtime en utilisant le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` dans une application de fonction. L’application de fonction est conservée sur la version majeure spécifiée jusqu’à ce que vous décidiez de passer à une nouvelle version.

Si vous spécifiez uniquement la version majeure (« ~ 1 » pour 1.x ou « bêta » pour 2.x), l’application de fonction est automatiquement mise à jour vers les nouvelles versions mineures du runtime quand elles deviennent disponibles. Les nouvelles versions mineures n’introduisent pas de changements importants. Si vous spécifiez une version mineure (par exemple, « 1.0.11360 »), l’application de fonction est maintenue sur cette version jusqu’à ce que vous la changiez explicitement. 

Quand une nouvelle version est disponible publiquement, une invite dans le portail vous donne la possibilité de basculer vers cette version. Après le passage à une nouvelle version, vous avez toujours la possibilité d’utiliser le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` pour revenir à une version précédente.

Un changement de version du runtime provoque un redémarrage de l’application de fonction.

Les valeurs que vous pouvez définir dans le paramètre d’application `FUNCTIONS_EXTENSION_VERSION` pour activer les mises à jour automatiques sont actuellement « ~1 » pour le runtime 1.x et « bêta » pour le runtime 2.x.

## <a name="view-the-current-runtime-version"></a>Afficher la version actuelle du runtime

Appliquez la procédure suivante pour afficher la version du runtime utilisée actuellement par une application de fonction. 

1. Dans le [portail Azure](https://portal.azure.com), accédez à l’application de fonction et, sous **Fonctionnalités configurées**, choisissez **Paramètres de l’application de fonction**. 

    ![Sélectionnez Paramètres de l’application de fonction](./media/functions-versions/add-update-app-setting.png)

2. Dans l’onglet **Paramètres de l’application de fonction**, trouvez la **Version du runtime**. Prenez note de la version spécifique du runtime ainsi que de la version majeure souhaitée. Dans l’exemple ci-dessous, le paramètre d’application FUNCTIONS\_EXTENSION\_VERSION a la valeur `~1`.
 
   ![Sélectionnez Paramètres de l’application de fonction](./media/functions-versions/function-app-view-version.png)

## <a name="target-a-version-using-the-portal"></a>Cibler une version à l’aide du portail

Quand vous avez besoin de cibler une version autre que la version majeure actuelle ou la version 2.0, définissez le paramètre d’application `FUNCTIONS_EXTENSION_VERSION`.

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre application de fonction, et sous **Fonctionnalités configurées**, choisissez **Paramètres d’application**.

    ![Sélectionnez Paramètres de l’application de fonction](./media/functions-versions/add-update-app-setting1a.png)

2. Sous l’onglet **Paramètres d’application**, recherchez le paramètre `FUNCTIONS_EXTENSION_VERSION` puis remplacez la valeur par une version valide du runtime 1.x ou `beta` pour la version 2.0. Un tilde accompagné d’une version principale désigne l’utilisation de la version la plus récente de cette version principale (par exemple, « ~ 1 »). 

    ![Définir le paramètre de l’application de fonction](./media/functions-versions/add-update-app-setting2.png)

3. Cliquez sur **Enregistrer** pour enregistrer la mise à jour du paramètre d’application. 

## <a name="target-a-version-using-azure-cli"></a>Cibler une version à l’aide d’Azure CLI

 Vous pouvez aussi définir l’élément `FUNCTIONS_EXTENSION_VERSION` à partir d’Azure CLI. À l’aide d’Azure CLI, mettez à jour ce paramètre d’application dans l’application de fonction, à l’aide de la commande [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#set).

```azurecli-interactive
az functionapp config appsettings set --name <function_app> \
--resource-group <my_resource_group> \
--settings FUNCTIONS_EXTENSION_VERSION=<version>
```
Dans ce code, remplacez `<function_app>` par le nom de votre application de fonction. Remplacez également `<my_resource_group>` par le nom du groupe de ressources de votre application de fonction. Remplacez `<version>` par une version valide du runtime 1.x ou `beta` pour la version 2.0. 

Vous pouvez exécuter cette commande à partir de [Azure Cloud Shell](../cloud-shell/overview.md) en choisissant **Essayer** dans l’exemple de code qui précède. Vous pouvez également utiliser [Azure CLI en local](/cli/azure/install-azure-cli) pour exécuter cette commande après avoir lancé la commande [az login](/cli/azure/reference-index#az_login) pour vous connecter.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Cibler le runtime 2.0 dans votre environnement de développement local](functions-run-local.md)

> [!div class="nextstepaction"]
> [Consulter les notes de publication pour les versions du runtime](https://github.com/Azure/azure-webjobs-sdk-script/releases)