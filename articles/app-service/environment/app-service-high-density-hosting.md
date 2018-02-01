---
title: "Hébergement haute densité sur Azure App Service | Microsoft Docs"
description: "Hébergement haute densité sur Azure App Service"
author: btardif
manager: erikre
editor: 
services: app-service\web
documentationcenter: 
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 22/01/2018
ms.author: byvinyal
ms.openlocfilehash: 2ffffd3cc9f5c59f74f71d6d7d31c5ea615d11f4
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="high-density-hosting-on-azure-app-service"></a>Hébergement haute densité sur Azure App Service
Lorsque vous utilisez App Service, votre application est découplée à partir de la capacité allouée par deux concepts :

* **L’Application :** représente l’application et sa configuration lors de l’exécution. Par exemple, elle inclut la version de .NET que le runtime doit charger, les paramètres de l’application, etc.
* **Le plan App Service :** définit les caractéristiques de capacité, l’ensemble des fonctionnalités disponibles et la localité de l’application. Par exemple, les caractéristiques peuvent être : machines puissantes (quatre cœurs), quatre instances, fonctionnalités Premium pour les États-Unis de l’Est.

Une application est toujours liée à un plan App Service, mais un Plan App Service peut fournir de la capacité à une ou plusieurs applications.

Par conséquent, la plateforme fournit la flexibilité nécessaire pour isoler une application unique, ou partager les ressources entre plusieurs applications avec un plan App Service commun.

Toutefois, lorsque plusieurs applications partagent un plan App Service, une instance de cette application sera exécutée sur chaque instance de ce plan App Service.

## <a name="per-app-scaling"></a>Mise à l’échelle par application
*La mise à l’échelle par application* est une fonction qui peut être activée au niveau du plan App Service et activée par chaque application.

La mise à l’échelle par application permet de mettre à l’échelle une application indépendamment du plan App Service qui l’héberge. De cette manière, un plan App Service peut passer à 10 instances, mais une application peut être définie de manière à n’en utiliser que cinq.

   >[!NOTE]
   >La mise à l’échelle par application est uniquement disponible pour les plans App Service avec des références SKU **Standard**, **Premium**, **Premium V2** et **Isolé**.
   >

### <a name="per-app-scaling-using-powershell"></a>Mise à l’échelle par application à l’aide de PowerShell

Créez un plan configuré comme plan de *mise à l’échelle par application* en passant l’attribut ```-perSiteScaling $true``` dans l’applet de commande ```New-AzureRmAppServicePlan```.

```
New-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Si vous souhaitez mettre à jour un plan App Service existant pour utiliser cette fonctionnalité : 

- Obtenez le plan cible ```Get-AzureRmAppServicePlan```
- Modifiez la propriété localement ```$newASP.PerSiteScaling = $true```
- Validez les modifications vers Azure ```Set-AzureRmAppServicePlan``` 

```
# Get the new App Service Plan and modify the "PerSiteScaling" property.
$newASP = Get-AzureRmAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan
$newASP

#Modify the local copy to use "PerSiteScaling" property.
$newASP.PerSiteScaling = $true
$newASP
    
#Post updated app service plan back to azure
Set-AzureRmAppServicePlan $newASP
```

Au niveau de l’application, configurez le nombre d’instances que l’application peut utiliser dans le plan App Service.

Dans l’exemple ci-dessous, l’application est limitée à deux instances quel que soit le nombre d’instances mis à l’échelle pour le plan App Service sous-jacent.

```
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzureRmWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzureRmWebApp $newapp
```

> [!IMPORTANT]
> $newapp.SiteConfig.NumberOfWorkers est différent de $newapp.MaxNumberOfWorkers. La mise à l’échelle par application utilise $newapp. SiteConfig.NumberOfWorkers pour déterminer les caractéristiques de mise à l’échelle de l’application.

### <a name="per-app-scaling-using-azure-resource-manager"></a>Mise à l’échelle par application avec Azure Resource Manager

Le *modèle Azure Resource Manager* ci-dessous crée les éléments suivants :

- Un plan App Service réparti sur 10 instances
- Une application configurée pour une mise à l’échelle vers un maximum de cinq instances.

Le plan App Service définit la propriété **PerSiteScaling** sur true ```"perSiteScaling": true```. L’application définit le **nombre de Workers** à utiliser sur 5 ```"properties": { "numberOfWorkers": "5" }```.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Configuration recommandée pour l’hébergement haute densité
La fonctionnalité de mise à l’échelle par application est activée dans les régions Azure et les environnements App Service globaux. Toutefois, la stratégie recommandée consiste à utiliser des environnements App Service pour tirer parti de leurs fonctionnalités avancées et des pools de capacité supérieure.  

Suivez ces étapes pour configurer un hébergement haute densité pour vos applications :

1. Configurez l’environnement App Service et choisissez un pool de Workers consacré au scénario d’hébergement haute densité.
1. Créez un seul plan App Service et mettez-le à l’échelle pour utiliser toute la capacité disponible sur le pool de Workers.
1. Définissez l’indicateur PerSiteScaling sur true dans le plan App Service.
1. Les nouvelles applications sont créées et attribuées à ce plan App Service avec la propriété **numberOfWorkers** définie sur **1**. Cela donne la densité la plus élevée possible sur ce pool de Workers.
1. Le nombre de Workers peut être configuré indépendamment par application pour accorder des ressources supplémentaires en fonction des besoins. Par exemple : 
    - Une application à haut niveau d’utilisation peut définir **numberOfWorkers** sur **3** pour accroître la capacité de traitement de cette application. 
    - Les applications à faible niveau d’utilisation définiraient **numberOfWorkers** sur **1**.

## <a name="next-steps"></a>Étapes suivantes

- [Présentation détaillée des plans Azure App Service](../azure-web-sites-web-hosting-plans-in-depth-overview.md)
- [Présentation de l'environnement App Service](app-service-app-service-environment-intro.md)
