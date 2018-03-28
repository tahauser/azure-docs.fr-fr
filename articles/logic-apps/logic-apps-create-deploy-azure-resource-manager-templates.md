---
title: Créer des applications logiques à l’aide des modèles Azure Resource Manager | Microsoft Docs
description: Créer et déployer des flux de travail d’application logique à l’aide des modèles Azure Resource Manager
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9e696f6e4614052456cf2b55123d98d61b8b3b9c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Créer et déployer des applications logiques à l’aide des modèles Azure Resource Manager

Azure Logic Apps fournit des modèles Azure Resource Manager que vous pouvez utiliser pour créer des applications logiques destinées à automatiser les flux de travail, mais également pour définir les ressources et les paramètres utilisés pour le déploiement. Vous pouvez utiliser ce modèle pour vos propres scénarios professionnels, ou le personnaliser selon vos besoins. En savoir plus sur le [modèle Resource Manager pour les applications logiques](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) et sur la [structure et la syntaxe du modèle Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Définir l’application logique

Cet exemple de définition d’application logique est lancé une fois par heure et exécute une commande ping sur l’emplacement spécifié dans le paramètre `testUri`.
Le modèle utilise les valeurs du paramètre pour le nom de l’application logique (```logicAppName```) et l’emplacement du test ping (```testUri```). En savoir plus sur la [définition de ces paramètres dans votre modèle](#define-parameters). Le modèle définit également l’emplacement de l’application logique sur le même emplacement que le groupe de ressources. 

``` json
{
    "type": "Microsoft.Logic/workflows",
    "apiVersion": "2016-06-01",
    "name": "[parameters('logicAppName')]",
    "location": "[resourceGroup().location]",
    "tags": {
        "displayName": "LogicApp"
    },
    "properties": {
        "definition": {
            "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "testURI": {
                "type": "string",
                "defaultValue": "[parameters('testUri')]"
                }
            },
            "triggers": {
                "Recurrence": {
                    "type": "Recurrence",
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 1
                    }
                }
            },
            "actions": {
                "Http": {
                    "type": "Http",
                    "inputs": {
                        "method": "GET",
                        "uri": "@parameters('testUri')"
                    },
                    "runAfter": {}
                }
            },
            "outputs": {}
        },
        "parameters": {}
    }
}
``` 

<a name="define-parameters"></a>

### <a name="define-parameters"></a>Définir les paramètres

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Voici une description des paramètres du modèle :

| Paramètre | Description | Exemple de définition JSON | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Définit le nom de l’application logique créée par ce modèle. | "logicAppName": { "type": "string", "metadata": { "description": "myExampleLogicAppName" } } |
| `testUri` | Définit l’emplacement sur lequel exécuter le test ping. | "testUri": { "type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

En savoir plus sur [la définition et les propriétés de l’API REST de flux de travail d’applications logiques](https://docs.microsoft.com/rest/api/logic/workflows) et sur la [création de définitions d’applications logiques avec JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Déployer automatiquement des applications logiques

Pour créer et déployer automatiquement une application logique dans Azure, choisissez **Déployer sur Azure** ici :

[![Déploiement sur Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Vous êtes alors connecté au portail Azure où vous pouvez indiquer les détails de votre application logique et apporter des modifications au modèle ou aux paramètres. Par exemple, le portail Azure vous invite à indiquer les détails suivants :

* Nom de l’abonnement Azure
* Groupe de ressources que vous souhaitez utiliser
* Emplacement de l’application logique
* Nom de votre application logique
* URI de test
* Acceptation des termes et conditions spécifiés

## <a name="deploy-logic-apps-with-commands"></a>Déployer des applications logiques à l’aide de commandes

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Azure CLI

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Obtenir de l’aide

* Si vous avez des questions, consultez le [forum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pour voter pour des idées de fonctionnalités ou pour en soumettre, visitez le [site de commentaires des utilisateurs Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Surveiller les applications logiques](../logic-apps/logic-apps-monitor-your-logic-apps.md)