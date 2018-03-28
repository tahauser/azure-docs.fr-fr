---
title: Aide au déploiement d’applications web Azure avec des modèles | Microsoft Docs
description: Recommandations sur la création de modèles Azure Resource Manager pour déployer des applications web.
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: dc816bb6e95d2800d79124dfac60b55e88eaa500
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Aide au déploiement d’applications web avec des modèles Azure Resource Manager

Cet article propose des recommandations sur la création de modèles Azure Resource Manager dans le but de déployer des solutions Azure App Service. Ces recommandations peuvent vous aider à éviter les problèmes courants.

## <a name="define-dependencies"></a>Définir des dépendances

La définition de dépendances pour des applications web implique de comprendre comment les ressources interagissent au sein d’une application web. Si vous ne spécifiez pas les dépendances dans le bon ordre, vous risquez de provoquer des erreurs de déploiement ou de créer une condition de concurrence qui entrave le déploiement.

> [!WARNING]
> Si vous intégrez une extension de site MSDeploy à votre modèle, vous devez définir toutes les ressources de configuration comme dépendantes de la ressource MSDeploy. Les modifications de configuration entraînent le redémarrage asynchrone du site. En rendant les ressources de configuration dépendantes de MSDeploy, vous permettez à MSDeploy de se terminer avant que le site ne redémarre. Sans ces dépendances, le site risquerait de redémarrer pendant le processus de déploiement de MSDeploy. Vous trouverez un exemple de modèle sur la page [Modèle WordPress avec une dépendance Web Deploy](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

L’image suivante montre l’ordre de dépendance de différentes ressources App Service :

![Dépendances des applications web](media/web-sites-rm-template-guidance/web-dependencies.png)

Déployez des ressources dans l’ordre suivant :

**Niveau 1**
* Plan App Service.
* Toutes les autres ressources associées, comme les bases de données ou les comptes de stockage.

**Niveau 2**
* Application web (dépend du plan App Service).
* Instance Azure Application Insights ciblant la batterie de serveurs (dépend du plan App Service).

**Niveau 3**
* Contrôle de code source (dépend de l’application web).
* Extension de site MSDeploy (dépend de l’application web).
* Instance Application Insights ciblant la batterie de serveurs (dépend de l’application web).

**Niveau 4**
* App Service Certificate (dépend du contrôle de code source ou de MSDeploy si l’un ou l’autre est présent ; sinon, dépend de l’application web).
* Paramètres de configuration, comme les chaînes de connexion, les valeurs web.config et les paramètres d’application (dépend du contrôle de code source ou de MSDeploy si l’un ou l’autre est présent ; sinon, dépend de l’application web).

**Niveau 5**
* Liaisons du nom d’hôte (dépend du certificat s’il est présent ; sinon, dépend d’une ressource de niveau supérieur).
* Extensions de site (dépend des paramètres de configuration s’ils sont présents ; sinon, dépend d’une ressource de niveau supérieur).

En règle générale, votre solution inclut uniquement une partie de ces ressources et niveaux. Pour les niveaux manquants, mappez les ressources de niveau inférieur sur le niveau immédiatement supérieur.

L’exemple suivant montre une partie d’un modèle. La valeur de configuration de la chaîne de connexion dépend de l’extension MSDeploy. L’extension MSDeploy dépend de l’application web et de la base de données.

```json
{
    "name": "[parameters('name')]",
    "type": "Microsoft.Web/sites",
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'))]",
            "[concat('SuccessBricks.ClearDB/databases/', parameters('databaseName'))]"
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

## <a name="find-information-about-msdeploy-errors"></a>Rechercher des informations sur les erreurs MSDeploy

Si votre modèle Resource Manager utilise MSDeploy, les messages d’erreur de déploiement peuvent être difficiles à comprendre. Pour obtenir plus d’informations suite à un échec de déploiement, essayez les étapes suivantes :

1. Accédez au site de la [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
2. Accédez au dossier à D:\home\LogFiles\SiteExtensions\MSDeploy.
3. Recherchez les fichiers appManagerStatus.xml et appManagerLog.xml. Le premier fichier enregistre l’état. Le second fichier enregistre des informations sur l’erreur. Si vous ne comprenez pas l’erreur, vous pouvez l’inclure dans votre demande d’aide sur le forum.

## <a name="choose-a-unique-web-app-name"></a>Choisir un nom unique pour l’application web

Le nom de votre application web doit être globalement unique. Vous pouvez utiliser une convention d’affectation de noms qui favorise les noms uniques ou la [fonction uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) pour générer un nom unique.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour suivre un tutoriel sur le déploiement d’applications web avec un modèle, consultez [Provisionner et déployer des microservices de manière prévisible dans Azure](app-service-deploy-complex-application-predictably.md).