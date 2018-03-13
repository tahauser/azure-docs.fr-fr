---
title: "Automatiser le déploiement de ressources pour une application de fonction dans Azure Functions | Microsoft Docs"
description: "Découvrez comment créer un modèle Azure Resource Manager qui déploie votre application de fonction."
services: Functions
documtationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: Azure Functions, fonctions, architecture sans serveur, infrastructure sous forme code, azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 6f31ba7b43c70f52bdd67d27512a322ec6258608
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatiser le déploiement de ressources pour votre application de fonction dans Azure Functions

Vous pouvez utiliser un modèle Azure Resource Manager pour déployer une application de fonction. Cet article présente les ressources et paramètres nécessaires pour effectuer cette opération. Vous devrez peut-être déployer des ressources supplémentaires, selon les [déclencheurs et liaisons](functions-triggers-bindings.md) présents dans votre application de fonction.

Pour en savoir plus sur la création de modèles, voir [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Pour des exemples de modèles, consultez :
- [Function app on Consumption plan] (Application de fonction dans le plan Consommation)
- [Function app on Azure App Service plan] (Application de fonction dans le plan Azure App Service)

## <a name="required-resources"></a>Ressources nécessaires

Une application de fonction nécessite les ressources suivantes :

* Un compte de [stockage Azure](../storage/index.yml)
* Un plan d’hébergement (plan Consommation ou plan App Service)
* Une application de fonction 

### <a name="storage-account"></a>Compte de stockage

Un compte de stockage Azure est nécessaire pour une application de fonction. Vous avez besoin d’un compte à usage général qui prend en charge les objets blob, les tables, les files d’attente et les fichiers. Pour plus d’informations, consultez [Configuration requise du compte de stockage Azure Functions](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

En outre, les propriétés `AzureWebJobsStorage` et `AzureWebJobsDashboard` doivent être spécifiées comme paramètres d’application dans la configuration du site. Le runtime d’Azure Functions utilise la chaîne de connexion `AzureWebJobsStorage` pour créer des files d’attente internes. La chaîne de connexion `AzureWebJobsDashboard` est utilisée pour la connexion au stockage de table Azure et l’alimentation de l’onglet **Surveiller** du portail.

Ces propriétés sont spécifiées dans la collection `appSettings` de l’objet `siteConfig` :

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
```    

### <a name="hosting-plan"></a>Plan d’hébergement

La définition du plan d’hébergement varie, selon que vous utilisez un plan Consommation ou un plan App Service. Consultez [Déployer une application de fonction dans le plan Consommation](#consumption) et [Déployer une application de fonction dans le plan App Service](#app-service-plan).

### <a name="function-app"></a>Conteneur de fonctions

La ressource d’application de fonction est définie à l’aide d’une ressource de type **Microsoft.Web/Site** et de genre **functionapp** :

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Déployer une application de fonction dans le plan Consommation

Vous pouvez exécuter une application de fonction dans deux modes différents : le plan Consommation et le plan App Service. Le plan Consommation alloue automatiquement la puissance de calcul pendant l’exécution du code, augmente la taille des instances quand c’est nécessaire pour gérer la charge, puis descend en puissance quand le code n’est pas en cours d’exécution. Vous n’avez donc pas à payer pour des machines virtuelles inactives ni à disposer d’une capacité de réserve à l’avance. Pour en savoir plus sur les plans d’hébergement, consultez [Plans Consommation et App Service d’Azure Functions](functions-scale.md).

Pour un exemple de modèle Azure Resource Manager, consultez [Function app on Consumption plan] (Application de fonction dans le plan Consommation).

### <a name="create-a-consumption-plan"></a>Créer un plan Consommation

Un plan Consommation est un type spécial de ressource « serverfarm ». Vous le spécifiez en utilisant la valeur `Dynamic` pour les propriétés `computeMode` et `sku` :

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

### <a name="create-a-function-app"></a>Créer une application de fonction

En outre, un plan Consommation nécessite deux paramètres supplémentaires dans la configuration du site : `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` et `WEBSITE_CONTENTSHARE`. Ces propriétés configurent le compte de stockage et le chemin de fichier où le code de l’application de fonction et la configuration sont stockés.

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Déployer une application de fonction dans le plan App Service

Dans le plan App Service, vos applications de fonction sont exécutées sur des machines virtuelles dédiées sur des références de base, Standard et Premium, à l’instar des applications web. Pour plus d’informations sur le fonctionnement du plan App Service, consultez l’article [Présentation détaillée des plans d’Azure App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Pour un exemple de modèle Azure Resource Manager, consultez [Function app on Azure App Service plan] (Application de fonction dans le plan Azure App Service).

### <a name="create-an-app-service-plan"></a>Créer un plan App Service

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Créer une application de fonction 

Une fois que vous avez sélectionné une option de mise à l’échelle, créez une application de fonction. L’application est le conteneur regroupant toutes vos fonctions.

Une application de fonction dispose de nombreuses ressources enfant que vous pouvez utiliser dans votre développement, notamment les paramètres de l’application et les options de contrôle de code source. Vous pouvez également choisir de supprimer la ressource enfant **sourcecontrols** et utiliser une autre [option de déploiement](functions-continuous-deployment.md) à la place.

> [!IMPORTANT]
> Il est important de comprendre comment les ressources sont déployées dans Azure pour déployer correctement votre application en utilisant Azure Resource Manager. Dans l’exemple suivant, les configurations de niveau supérieur sont appliquées à l’aide de **siteConfig**. Il est important de définir ces configurations à un niveau supérieur, car elles fournissent des informations au moteur de déploiement et au runtime Functions. Des informations de niveau supérieur sont requises avant que la ressource enfant **sourcecontrols/web** soit appliquée. Bien qu’il soit possible de configurer ces paramètres dans la ressource **configuration/appSettings** au niveau enfant, dans certains cas, votre application de fonction doit être déployée *avant* que la ressource **configuration/appSettings** soit appliquée. Par exemple, quand vous utilisez des fonctions avec [Logic Apps](../logic-apps/index.yml), vos fonctions sont une dépendance d’une autre ressource.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Dans ce modèle, la valeur du paramètre d’application [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), qui définit le répertoire de base dans lequel le moteur de déploiement de fonctions (Kudu) recherche le code pouvant être déployé, est utilisée. Dans notre référentiel, nos fonctions se trouvent dans un sous-dossier du dossier **src**. Par conséquent, dans l’exemple précédent, nous définissons la valeur de paramètres d’application sur `src`. Si vos fonctions sont à la racine de votre référentiel, ou si vous ne déployez pas de contrôle de code source, vous pouvez supprimer cette valeur de paramètres d’application.

## <a name="deploy-your-template"></a>Déployer votre modèle

Vous pouvez utiliser une des méthodes suivantes pour déployer votre modèle :

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portail Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Bouton Déployer dans Azure

Remplacez ```<url-encoded-path-to-azuredeploy-json>``` par une version [d’URL encodée](https://www.bing.com/search?q=url+encode) du chemin brut de votre fichier `azuredeploy.json` dans GitHub.

Voici un exemple qui utilise markdown :

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Voici un exemple qui utilise HTML :

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le développement et la configuration d’Azure Functions.

* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)
* [Guide pratique pour configurer les paramètres d’application de fonction Azure](functions-how-to-use-azure-function-app-settings.md)
* [Créer votre première fonction Azure](functions-create-first-azure-function.md)

<!-- LINKS -->

[Function app on Consumption plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json (Application de fonction dans le plan Consommation)
[Function app on Azure App Service plan]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json (Application de fonction dans le plan Azure App Service)
