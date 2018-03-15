---
title: Vues dans des solutions de gestion dans Operations Management Suite (OMS) | Microsoft Docs
description: "Les solutions de gestion dans Operations Management Suite (OMS) comprennent généralement une ou plusieurs vues pour visualiser des données.  Cet article décrit comment exporter une vue créée par le Concepteur de vues et l’inclure dans une solution de gestion. "
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: c103ee748446c4819b7925af04d90c22225a21a3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2018
---
# <a name="views-in-operations-management-suite-oms-management-solutions-preview"></a>Vues dans des solutions de gestion dans Operations Management Suite (OMS) (préversion)
> [!NOTE]
> Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans OMS qui sont actuellement en préversion. Tout schéma décrit ci-dessous est susceptible d’être modifié.    
>
>

[Les solutions de gestion dans Operations Management Suite (OMS)](operations-management-suite-solutions.md) comprennent généralement une ou plusieurs vues pour visualiser des données.  Cet article décrit comment exporter une vue créée par le [Concepteur de vues](../log-analytics/log-analytics-view-designer.md) et l’inclure dans une solution de gestion.  

> [!NOTE]
> Les exemples dans cet article utilisent des paramètres et des variables qui sont nécessaires ou courants pour les solutions de gestion. Ils sont décrits dans la rubrique [Création de solutions de gestion dans Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md)
>
>

## <a name="prerequisites"></a>Prérequis
Cet article suppose que vous êtes déjà familiarisé avec la [création d’une solution de gestion](operations-management-suite-solutions-creating.md) et la structure d’un fichier solution.

## <a name="overview"></a>Vue d’ensemble
Pour inclure une vue dans une solution de gestion, vous créez une **ressource** lui correspondant dans le [fichier solution](operations-management-suite-solutions-creating.md).  Cependant, le fichier JSON qui décrit la configuration détaillée de la vue est généralement complexe. Un auteur de solution classique ne serait pas en mesure de le créer manuellement.  La méthode la plus courante consiste à créer la vue en utilisant le [Concepteur de vues](../log-analytics/log-analytics-view-designer.md), à l’exporter, puis à ajouter ensuite sa configuration détaillée à la solution.

Voici les étapes de base pour ajouter une vue à une solution.  Chaque étape est décrite en détail dans les sections ci-dessous.

1. Exportation de la vue dans un fichier.
2. Création de la ressource de la vue dans la solution.
3. Ajout des détails de la vue.

## <a name="export-the-view-to-a-file"></a>Exportation de la vue dans un fichier.
Suivez les instructions du [Concepteur de vues de Log Analytics](../log-analytics/log-analytics-view-designer.md) pour exporter un affichage dans un fichier.  Le fichier exporté sera au format JSON avec les mêmes [éléments que le fichier solution](operations-management-suite-solutions-solution-file.md).  

L’élément **resources** du fichier de vue aura une ressource de type **Microsoft.OperationalInsights/workspaces** qui représente l’espace de travail OMS.  Cet élément aura un sous-élément de type **views** qui représente la vue et qui contient sa configuration détaillée.  Vous copiez les détails de cet élément, puis vous les copiez dans votre solution.

## <a name="create-the-view-resource-in-the-solution"></a>Création de la ressource de la vue dans la solution
Ajoutez la ressource de la vue suivante à l’élément **resources** de votre fichier solution.  Cette procédure utilise des variables décrites ci-dessous que vous devez également ajouter.  Notez que les propriétés **Dashboard** et **OverviewTile** sont des espaces réservés que vous remplacerez par les propriétés correspondantes du fichier de vue exporté.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Ajoutez les variables suivantes à l’élément variables du fichier de la solution et remplacez les valeurs par celles de votre solution.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Notez que vous pouvez copier la ressource de vue entière depuis votre fichier de vue exporté, mais que vous devez apporter les modifications suivantes pour qu’elle fonctionne dans votre solution.  

* Le **type** pour la ressource de vue doit être modifié de **views** en **Microsoft.OperationalInsights/workspaces**.
* La propriété **name** pour la ressource de la vue doit être modifiée pour inclure le nom de l’espace de travail.
* La dépendance vis-à-vis de l’espace de travail doit être supprimée, car la ressource de l’espace de travail n’est pas définie dans la solution.
* La propriété **DisplayName** doit être ajoutée à la vue.  Les éléments **Id**, **Name**, et **DisplayName** doivent tous correspondre.
* Les noms des paramètres doivent être modifiés de manière à correspondre à l’ensemble de paramètres nécessaire.
* Les variables doivent être définies dans la solution et utilisées dans les propriétés appropriées.

### <a name="log-analytics-api-version"></a>Version de l’API Log Analytics
Toutes les ressources Log Analytics définies dans un modèle Resource Manager ont une propriété **apiVersion** qui définit la version de l’API que la ressource doit utiliser.  Cette version varie selon que les affichages avec des requêtes utilisent [le langage de requête hérité ou mis à niveau](../log-analytics/log-analytics-log-search-upgrade.md).  

 Le tableau suivant indique les versions de l’API Log Analytics pour les affichages dans des espaces de travail hérités et mis à niveau : 

| Version de l’espace de travail | Version de l'API | Requête |
|:---|:---|:---|
| v1 (hérité)   | 2015-11-01-preview | Format hérité.<br> Exemple : Type=Event EventLevelName = Error  |
| v2 (mis à niveau) | 2015-11-01-preview | Format hérité.  Converti au format mis à niveau durant l’installation.<br> Exemple : Type=Event EventLevelName = Error<br>Converti au format : Event &#124; where EventLevelName == "Error"  |
| v2 (mis à niveau) | 2017-03-03-preview | Format mis à niveau. <br>Exemple : Event &#124; where EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>Ajout des détails de la vue
La ressource de la vue dans le fichier exporté contient deux éléments dans l’élément **properties** nommés **Dashboard** et **OverviewTile** qui contiennent la configuration détaillée de la vue.  Copiez ces deux éléments et leur contenu dans l’élément **properties** de la ressource de vue dans votre fichier solution.

## <a name="example"></a>Exemple
Par exemple, l’exemple suivant montre un fichier solution simple avec une vue.  Des points de suspension (...) sont affichés à la place du contenu des éléments **Dashboard** et **OverviewTile** pour des raisons d’espace.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Étapes suivantes
* Découvrez plus de détails sur la création de [solutions de gestion](operations-management-suite-solutions-creating.md).
* Inclure [des runbooks Automation dans votre solution de gestion](operations-management-suite-solutions-resources-automation.md).
