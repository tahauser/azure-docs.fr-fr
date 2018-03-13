---
title: "Aide sur le déploiement d’applications web Azure avec des modèles | Microsoft Docs"
description: "Recommandations sur la création de modèles Azure Resource Manager pour déployer des applications web."
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
ms.openlocfilehash: eff0b0e6258217fa8fbf7f15606f98d70fecd7c5
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="guidance-on-deploying-web-apps-with-azure-resource-manager-templates"></a>Aide sur le déploiement d’applications web avec des modèles Azure Resource Manager

Cet article fournit des recommandations sur la création de modèles Azure Resource Manager pour déployer des solutions App Service. Ces recommandations peuvent vous aider à éviter les problèmes courants.

## <a name="define-dependencies"></a>Définir des dépendances

La définition de dépendances pour Web Apps implique de comprendre comment les ressources interagissent au sein d’une application web. Si vous spécifiez des dépendances dans le mauvais ordre, vous risquez d’entraîner des erreurs de déploiement ou de créer une condition de concurrence qui entrave le déploiement.

> [!WARNING]
> Si vous incluez une extension de site MS Deploy dans votre modèle, vous devez définir toutes les ressources de configuration comme dépendantes de la ressource MS Deploy. Les modifications de configuration entraînent le redémarrage asynchrone du site. En rendant les ressources de configuration dépendantes de MS Deploy, vous permettez à MS Deploy de se terminer avant que le site ne redémarre. Sans ces dépendances, le site peut redémarrer pendant le processus de déploiement de MS Deploy. Pour obtenir un exemple de modèle, consultez [Modèle Wordpress avec une dépendance Web Deploy](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json).

L’image suivante montre l’ordre de dépendance de différentes ressources App Service.

![Dépendances des applications web](media/web-sites-rm-template-guidance/web-dependencies.png)

Déployez des ressources dans l’ordre suivant :

**Niveau 1**
* Plan App Service
* Toutes les autres ressources associées comme les bases de données ou les comptes de stockage

**Niveau 2**
* Application web (dépend du plan App Service)
* Application Insights ciblant la batterie de serveurs (dépend du plan App Service)

**Niveau 3**
* Contrôle de code source (dépend de l’application web)
* Extension de site MSDeploy (dépend de l’application web)
* Application Insights ciblant la batterie de serveurs (dépend de l’application web)

**Niveau 4**
* App Service Certificate (dépend du contrôle de code source ou de MSDeploy si l’un ou l’autre est présent ; sinon, dépend de l’application web)
* Paramètres de configuration comme les chaînes de connexion, les valeurs de configuration web, les paramètres d’application (dépend du contrôle de code source ou de MSDeploy si l’un ou l’autre est présent ; sinon, dépend de l’application web)

**Niveau 5**
* Liaisons de nom d’hôte (dépend du certificat, s’il est présent ; sinon, d’une ressource de niveau supérieur)
* Extensions de site (dépend des paramètres de configuration s’ils sont présents ; sinon, d’une ressource de niveau supérieur)

En règle générale, votre solution inclut uniquement une partie de ces ressources et niveaux. Pour les niveaux manquants, mappez les ressources inférieures sur le niveau supérieur suivant.

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
2. Accédez au dossier `D:\home\LogFiles\SiteExtensions\MSDeploy`.
3. Recherchez les fichiers *appManagerStatus.xml* et *appManagerLog.xml*. Le premier fichier enregistre l’état. Le second fichier enregistre des informations sur l’erreur. Si l’erreur n’est pas claire pour vous, vous pouvez l’inclure dans votre demande d’aide sur le forum.

## <a name="unique-web-app-name"></a>Nom d’application web unique

Le nom de votre application web doit être globalement unique. Vous pouvez utiliser une convention d’affectation de noms qui favorise les noms uniques ou la [fonction uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) pour vous aider à générer un nom unique.

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