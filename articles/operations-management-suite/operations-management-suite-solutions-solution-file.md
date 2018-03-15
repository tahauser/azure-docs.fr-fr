---
title: "Création d’un fichier de solution de gestion dans Azure | Microsoft Docs"
description: "Les solutions de gestion fournissent des scénarios de gestion empaquetée que les clients peuvent ajouter à leur environnement Azure.  Cet article fournit des informations sur la manière dont vous pouvez créer des solutions de gestion à utiliser dans votre propre environnement ou à mettre à la disposition de vos clients."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d896fb7c5ffed5c0fe338c2d2f1ef864aacd6f79
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Création d’un fichier de solution de gestion dans Azure (préversion)
> [!NOTE]
> Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans Azure qui sont actuellement en préversion. Tout schéma décrit ci-dessous est susceptible d’être modifié.  

Les solutions de gestion dans Azure sont implémentées en tant que [modèles Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).  Avant de créer des solutions de gestion, apprenez à [créer un modèle](../azure-resource-manager/resource-group-authoring-templates.md).  Cet article fournit des détails uniques sur les modèles utilisés pour des solutions. Il indique également comment configurer des ressources de solution courantes.


## <a name="tools"></a>Outils

Vous pouvez utiliser n’importe quel éditeur de texte pour travailler avec les fichiers de solution, mais nous vous recommandons d’exploiter les fonctionnalités fournies par Visual Studio et Visual Studio Code, comme décrit dans les articles suivants.

- [Création et déploiement de groupes de ressources Azure à l’aide de Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Utiliser des modèles Azure Resource Manager dans Visual Studio Code](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>Structure
La structure de base d’un fichier solution de gestion est identique à un [modèle Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format) qui se présente comme suit.  Chacune des sections ci-dessous décrit les éléments de niveau supérieur et leur contenu dans une solution.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>parameters
Les [Paramètres](../azure-resource-manager/resource-group-authoring-templates.md#parameters) sont des valeurs que l’utilisateur doit indiquer lorsqu’il installe la solution de gestion.  Il existe des paramètres standard compris dans toutes les solutions, et vous pouvez ajouter des paramètres supplémentaires en fonction des besoins spécifiques de votre solution.  La manière dont les utilisateurs fournissent des valeurs de paramètre lors de l’installation de la solution dépend du paramètre lui-même et de la façon dont la solution est installée.

Quand un utilisateur installe votre solution de gestion par le biais de la [Place de Marché Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions) ou des [modèles de démarrage rapide Azure](operations-management-suite-solutions.md#finding-and-installing-management-solutions), il est invité à sélectionner un [espace de travail Log Analytics et un compte Automation](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account).  Ils sont utilisés pour remplir les valeurs de chacun des paramètres standard.  L’utilisateur n’est pas invité à fournir directement des valeurs pour les paramètres standard, mais il est invité à fournir une valeur pour tout paramètre supplémentaire.


Lorsque l’utilisateur installe votre solution par le biais [d’une autre méthode](operations-management-suite-solutions.md#finding-and-installing-management-solutions), il doit fournir une valeur pour tous les paramètres standard et tous les paramètres supplémentaires.

Vous trouverez ci-dessous un exemple de paramètre.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

Le tableau suivant décrit les attributs d’un paramètre.

| Attribut | Description |
|:--- |:--- |
| Type |Type de données pour le paramètre. Le contrôle de saisie affiché pour l’utilisateur dépend du type de données.<br><br>Valeur booléenne : zone de liste déroulante<br>Chaîne : zone de texte<br>Entier : zone de texte<br>securestring : champ de mot de passe<br> |
| category |Catégorie du paramètre facultative.  Les paramètres de la même catégorie sont regroupés. |
| contrôle |Fonctionnalité supplémentaire pour les paramètres de type chaîne.<br><br>datetime : le contrôle Datetime est affiché.<br>guid : la valeur Guid est générée automatiquement et le paramètre n’est pas affiché. |
| description |Description du paramètre facultative.  Affichée dans une bulle d’informations en regard du paramètre. |

### <a name="standard-parameters"></a>Paramètres standard
Le tableau suivant répertorie les paramètres standard pour toutes les solutions de gestion.  Ces valeurs sont remplies pour l’utilisateur, qui n’a donc pas à le faire lorsque la solution est installée à partir de la Place de marché Azure ou de modèles de démarrage rapide.  L’utilisateur doit fournir des valeurs pour ces paramètres si la solution est installée par le biais d’une autre méthode.

> [!NOTE]
> L’interface utilisateur dans la Place de marché Azure ou des modèles de démarrage rapide attend que les noms de paramètre soient renseignés dans la table.  Si vous utilisez des noms de paramètres différents, l’utilisateur sera invité à les renseigner et les valeurs ne seront pas automatiquement remplies.
>
>

| Paramètre | type | Description |
|:--- |:--- |:--- |
| accountName |chaîne |Nom de compte Azure Automation. |
| pricingTier |chaîne |Niveau tarifaire de l’espace de travail Log Analytics et du compte Azure Automation. |
| regionId |chaîne |Région du compte Azure Automation. |
| solutionName |chaîne |Nom de la solution.  Si vous déployez votre solution via des modèles de démarrage rapide, vous devez définir solutionName comme un paramètre afin de pouvoir définir une chaîne au lieu d’obliger l’utilisateur à en spécifier une. |
| workspaceName |chaîne |Nom de l’espace de travail Log Analytics. |
| workspaceRegionId |chaîne |Région de l’espace de travail Log Analytics. |


Voici la structure des paramètres standards que vous pouvez copier et coller dans votre fichier de solution.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Faites référence aux valeurs de paramètre dans d’autres éléments de la solution avec la syntaxe **parameters('nom du paramètre')**.  Par exemple, pour accéder au nom de l’espace de travail, utilisez **parameters('workspaceName')**.

## <a name="variables"></a>variables
Les [Variables](../azure-resource-manager/resource-group-authoring-templates.md#variables) sont des valeurs que vous allez utiliser dans le reste de la solution de gestion.  Celles-ci ne sont pas exposées à l’utilisateur qui installe la solution.  Elles sont conçues pour fournir à l’auteur un emplacement unique où il peut gérer les valeurs qui peuvent être utilisées plusieurs fois dans la solution. Vous devez placer toutes les valeurs spécifiques à votre solution dans des variables, contrairement au codage en dur dans l’élément **resources**.  Cela rend le code plus lisible et vous permet de modifier facilement ces valeurs dans des versions ultérieures.

Voici un exemple d’élément **variables** avec des paramètres standard utilisés dans les solutions.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Faites référence aux valeurs de variable dans la solution avec la syntaxe **variables('nom de la variable')**.  Par exemple, pour accéder à la variable SolutionName, utilisez **variables('SolutionName')**.

Vous pouvez également définir des variables complexes pour plusieurs ensembles de valeurs.  Elles sont particulièrement utiles dans les solutions de gestion où vous définissez plusieurs propriétés pour différents types de ressources.  Par exemple, vous pouvez restructurer les variables de solution ci-dessus avec ce qui suit.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Dans ce cas, faites référence aux valeurs de variable dans la solution avec la syntaxe **variables('nom de la variable').property**.  Par exemple, pour accéder à la variable SolutionName, utilisez **variables('Solution').Name**.

## <a name="resources"></a>Ressources
Les [ressources](../azure-resource-manager/resource-group-authoring-templates.md#resources) définissent les différentes ressources installées et gérées par votre solution de gestion.  Il s’agit de la partie la plus vaste et la plus complexe du modèle.  Vous pouvez obtenir la structure et une description complète des éléments de ressource dans [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#resources).  Différentes ressources que vous définirez généralement sont détaillées dans d’autres articles de cette documentation. 


### <a name="dependencies"></a>Les dépendances
L’élément **dependsOn** spécifie une [dépendance](../azure-resource-manager/resource-group-define-dependencies.md) vis-à-vis d’une autre ressource.  Lorsque la solution est installée, aucune ressource n’est créée tant que toutes ses dépendances n’ont pas été créées.  Par exemple, votre solution peut [démarrer un runbook](operations-management-suite-solutions-resources-automation.md#runbooks) lorsqu’il est installé à l’aide d’une [ressource de tâche](operations-management-suite-solutions-resources-automation.md#automation-jobs).  La ressource de tâche dépend de la ressource de runbook pour garantir que le runbook est créé avant la tâche.

### <a name="log-analytics-workspace-and-automation-account"></a>Espace de travail Log Analytics et compte Automation
Les solutions de gestion nécessitent un [espace de travail Log Analytics](../log-analytics/log-analytics-manage-access.md) qui contient des vues et un [compte Automation](../automation/automation-security-overview.md#automation-account-overview) qui contient les Runbooks et les ressources associées.  Ces éléments doivent être disponibles avant que les ressources de la solution soient créées et ne doivent pas être définis dans la solution elle-même.  L’utilisateur [spécifiera un espace de travail et un compte](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account) lors du déploiement de la solution, mais l’auteur doit tenir compte des points suivants.


## <a name="solution-resource"></a>Ressource de la solution
Chaque solution nécessite une entrée de ressource dans l’élément **resources** qui définit la solution elle-même.  Cette entrée est de type **Microsoft.OperationsManagement/solutions** et a la structure suivante. Cela inclut les [paramètres standard](#parameters) et les [variables](#variables) qui sont généralement utilisés pour définir les propriétés de la solution.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Les dépendances
La ressource de la solution doit avoir une [dépendance](../azure-resource-manager/resource-group-define-dependencies.md) vis-à-vis de toute autre ressource de la solution, car ces ressources doivent exister avant que la solution ne puisse être créée.  Pour cela, ajoutez une entrée pour chaque ressource dans l’élément **dependsOn**.

### <a name="properties"></a>properties
La ressource de solution possède les propriétés indiquées dans le tableau suivant.  Cela inclut les ressources référencées et contenues dans la solution qui définit la manière dont la ressource est gérée après l’installation de la solution.  Chaque ressource de la solution doit être répertoriée dans la propriété **referencedResources** ou **containedResources**.

| Propriété | Description |
|:--- |:--- |
| workspaceResourceId |ID de l’espace de travail Log Analytics au format *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<Workspace Name\>*. |
| referencedResources |Liste des ressources de la solution qui ne doivent pas être supprimées en même temps que la solution. |
| containedResources |Liste des ressources de la solution qui doivent être supprimées en même temps que la solution. |

L’exemple ci-dessus est valable pour une solution avec un runbook, une planification et une vue.  La planification et le runbook sont *référencés* dans l’élément **properties** afin de ne pas être supprimés le cas échéant en même temps que la solution.  La vue est *contenue* afin qu’elle soit supprimée en même temps que la solution.

### <a name="plan"></a>Planification
L’entité **plan** de la ressource de solution possède les propriétés indiquées dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| Nom |Nom de la solution. |
| version |Version de la solution, telle que déterminée par l’auteur. |
| product |Chaîne unique pour identifier la solution. |
| publisher |Éditeur de la solution. |



## <a name="sample"></a>Exemple
Vous pouvez voir des exemples de fichiers de solution avec une ressource de solution aux emplacements suivants.

- [Ressources Automation](operations-management-suite-solutions-resources-automation.md#sample)
- [Ressources de recherche et d’alerte](operations-management-suite-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Étapes suivantes
* [Ajoutez des alertes et des recherches enregistrées](operations-management-suite-solutions-resources-searches-alerts.md) à votre solution de gestion.
* [Ajoutez des vues](operations-management-suite-solutions-resources-views.md) à votre solution de gestion.
* [Ajoutez des runbooks et d’autres ressources Automation](operations-management-suite-solutions-resources-automation.md) à votre solution de gestion.
* Découvrez comment [créer des modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Dans [Modèles de démarrage rapide Azure](https://azure.microsoft.com/documentation/templates), recherchez des exemples de modèles Resource Manager.
