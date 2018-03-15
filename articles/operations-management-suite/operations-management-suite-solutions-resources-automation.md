---
title: Ressources Azure Automation dans des solutions OMS | Microsoft Docs
description: "Les solutions dans OMS comprennent généralement des runbooks dans Azure Automation pour automatiser des processus tels que la collecte, le traitement et la surveillance des données.  Cet article explique comment inclure des runbooks et leurs ressources associées dans une solution."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 5281462e-f480-4e5e-9c19-022f36dce76d
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1097b1ddd2e8f2fae0ffc809aee63be5c2ed4cb1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="adding-azure-automation-resources-to-an-oms-management-solution-preview"></a>Ajout de ressources Azure Automation pour une solution de gestion OMS (version préliminaire)
> [!NOTE]
> Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans OMS qui sont actuellement en préversion. Tout schéma décrit ci-dessous est susceptible d’être modifié.   


[Les solutions de gestion dans OMS](operations-management-suite-solutions.md) comprennent généralement des runbooks dans Azure Automation pour automatiser des processus tels que la collecte, le traitement et la surveillance des données.  En plus des runbooks, les comptes Automation incluent des ressources telles que des variables et des planifications qui prennent en charge les runbooks utilisés dans la solution.  Cet article explique comment inclure des runbooks et leurs ressources associées dans une solution.

> [!NOTE]
> Les exemples dans cet article utilisent des paramètres et des variables qui sont nécessaires ou courants pour les solutions de gestion. Ils sont décrits dans la rubrique [Création de solutions de gestion dans Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md) 


## <a name="prerequisites"></a>Prérequis
Cet article suppose que vous êtes déjà familiarisé avec les informations suivantes.

- Comment [créer une solution de gestion](operations-management-suite-solutions-creating.md).
- La structure d’un [fichier de solution](operations-management-suite-solutions-solution-file.md).
- Guide de [Création de modèles Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Compte Automation
Toutes les ressources dans Azure Automation sont contenues dans un [compte Automation](../automation/automation-security-overview.md#automation-account-overview).  Comme décrit dans [Espace de travail OMS et compte Automation](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account), le compte Automation n’est pas inclus dans la solution de gestion mais doit exister avant l’installation de la solution.  Si ce n’est pas le cas, l’installation de la solution échoue.

Le nom de chaque ressource Automation inclut le nom de son compte Automation.  Pour cela, utilisez le paramètre **accountName** comme dans l’exemple suivant d’une ressource de runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Vous devez inclure tous les runbooks utilisés par la solution dans le fichier de solution afin qu’ils soient créés lorsque la solution est installée.  Vous ne pouvez toutefois pas inclure le corps du runbook dans le modèle, vous devez donc publier le runbook dans un emplacement public, où il sera accessible par n’importe quel utilisateur installant votre solution.

Les ressources [Azure Automation runbook](../automation/automation-runbook-types.md) sont de type **Microsoft.Automation/automationAccounts/runbooks** et ont les propriétés du tableau suivant.a structure suivante. Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


Les propriétés des runbooks sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| runbookType |Spécifie les types de runbook. <br><br> Script : script PowerShell <br>PowerShell : workflow PowerShell <br> GraphPowerShell : runbook de script PowerShell graphique <br> GraphPowerShellWorkflow : runbook de workflow PowerShell graphique |
| logProgress |Spécifie si les [enregistrements de progression](../automation/automation-runbook-output-and-messages.md) doivent être générés pour le runbook. |
| logVerbose |Spécifie si les [enregistrements détaillés](../automation/automation-runbook-output-and-messages.md) doivent être générés pour le runbook. |
| description |Description facultative du runbook. |
| publishContentLink |Spécifie le contenu du runbook. <br><br>uri : Uri du contenu du runbook.  Il s’agit d’un fichier .ps1 pour des runbooks PowerShell et Script et d’un fichier de runbook graphique exporté pour un runbook Graph.  <br> version : version du runbook pour votre propre suivi. |


## <a name="automation-jobs"></a>Tâches Automation
Lorsque vous démarrez un Runbook dans Azure Automation, il crée un travail d’automatisation.  Vous pouvez ajouter une ressource de travail d’automatisation à votre solution pour démarrer automatiquement un runbook lorsque la solution de gestion est installée.  Cette méthode est généralement utilisée pour démarrer des runbooks qui sont utilisés pour la configuration initiale de la solution.  Pour démarrer un runbook à intervalles réguliers, créez une [planification](#schedules) et une [planification de travail](#job-schedules)

Les ressources de tâche sont de type **Microsoft.Automation/automationAccounts/jobs** et ont la structure suivante.  Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

Les propriétés des travaux d’automatisation sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| runbook |Entité name unique portant le nom du runbook à démarrer. |
| parameters |Entité pour chaque valeur de paramètre exigée par le runbook. |

La tâche comprend le nom du runbook et toute valeur de paramètre à lui envoyer.  La tâche doit [dépendre](operations-management-suite-solutions-solution-file.md#resources) du runbook qu’elle démarre, car le runbook doit être créé avant la tâche.  Si vous avez plusieurs runbooks qui doivent être lancés, vous pouvez définir leur ordre en rendant un travail dépendant des autres travaux qui doivent être exécutés en premier.

Le nom d’une ressource de tâche doit contenir un GUID, qui est généralement affecté par un paramètre.  Vous trouverez plus d’informations sur les paramètres GUID dans la rubrique [Création de solutions dans Operations Management Suite (OMS)](operations-management-suite-solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certificats
Les [certificats Azure Automation](../automation/automation-certificates.md) sont de type **Microsoft.Automation/automationAccounts/certificate** et ont la structure suivante. Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



Les propriétés des ressources de certificat sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| base64Value |Valeur de base 64 pour le certificat. |
| thumbprint |Thumbprint pour le certificat. |



## <a name="credentials"></a>Informations d'identification
Les [informations d'identification Azure Automation](../automation/automation-credentials.md) sont de type **Microsoft.Automation/automationAccounts/credentials** et ont la structure suivante.  Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

Les propriétés des ressources d’informations d’identification sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| userName |Nom d’utilisateur des informations d’identification. |
| password |Mot de passe des informations d’identification. |


## <a name="schedules"></a>Planifications
Les [planifications Azure Automation](../automation/automation-schedules.md) sont de type **Microsoft.Automation/automationAccounts/schedules** et ont la structure suivante. Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

Les propriétés des ressources de planification sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| description |Description facultative de la planification. |
| startTime |Spécifie l’heure de début d’une planification en tant qu’objet DateTime. Une chaîne peut être fournie si elle peut être convertie en un élément DateTime valide. |
| isEnabled |Spécifie si la planification est activée. |
| interval |Le type d’intervalle pour la planification.<br><br>day<br>hour |
| frequency |Fréquence à laquelle la planification doit se déclencher en nombre de jours ou d’heures. |

Les planifications doivent avoir une heure de début avec une valeur supérieure à l’heure actuelle.  Vous ne pouvez pas fournir cette valeur avec une variable puisque vous n’auriez aucun moyen de savoir l’heure d’installation.

Utilisez une des deux stratégies suivantes lors de l’utilisation des ressources de planification dans une solution.

- Utilisez un paramètre pour l’heure de début de la planification.  Cela invite l’utilisateur à fournir une valeur lorsqu’il installe la solution.  Si vous avez plusieurs planifications, vous pouvez utiliser une valeur de paramètre unique pour plusieurs d'entre eux.
- Créez les planifications à l’aide d’un runbook qui démarre lorsque la solution est installée.  Cela supprime la nécessité pour l’utilisateur de spécifier une heure, mais vous ne pouvez pas inclure la planification dans votre solution, elle sera donc supprimée en même temps que la solution.


### <a name="job-schedules"></a>Planifications de travaux
Les ressources de planification de tâche lient un runbook à une planification.  Elles ont un type de **Microsoft.Automation/automationAccounts/jobSchedules** et la structure suivante.  Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


Les propriétés des planifications de travail sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| nom de la planification |Entité **name** unique portant le nom de la planification. |
| nom du runbook  |Entité **name** unique portant le nom du runbook.  |



## <a name="variables"></a>variables
Les [variables Azure Automation](../automation/automation-variables.md) sont de type **Microsoft.Automation/automationAccounts/variables** et ont la structure suivante.  Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

Les propriétés des ressources de variable sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| description | Description facultative de la variable. |
| isEncrypted | Spécifie si la variable doit être chiffrée. |
| Type | Cette propriété n’a actuellement aucun effet.  Le type de données de la variable sera déterminé par la valeur initiale. |
| value | Valeur de la variable. |

> [!NOTE]
> La propriété **type** n’a actuellement aucun effet sur la variable en train d’être créée.  Le type de données de la variable sera déterminé par la valeur.  

Si vous définissez la valeur initiale pour la variable, elle doit être configurée en tant que type de données correct.  Le tableau suivant fournit les différents types de données autorisés et leur syntaxe.  Notez que les valeurs dans JSON doivent toujours être placées entre guillemets avec les caractères spéciaux à l’intérieur des guillemets.  Par exemple, une valeur de chaîne serait spécifiée en plaçant la chaîne entre guillemets (en utilisant le caractère d’échappement (\\)), tandis qu’une valeur numérique serait spécifiée avec une seule paire de guillemets.

| Type de données | Description | exemples | Est résolu en |
|:--|:--|:--|:--|
| chaîne   | Placer la valeur entre des guillemets doubles.  | "\"Hello world\"" | "Hello world" |
| numérique  | Valeur numérique avec des guillemets simples.| "64" | 64 |
| booléenne  | **true** ou **false** entre guillemets.  Notez que cette valeur doit être en minuscules. | "true" | true |
| Datetime | Valeur de date sérialisée.<br>Vous pouvez utiliser la cmdlet ConvertTo-Json dans PowerShell pour générer cette valeur pour une date particulière.<br>Exemple : get-date "5/24/2017 13:14:57" \| ConvertTo-Json | "\\/Date(1495656897378)\\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Modules
Votre solution de gestion ne doit pas nécessairement définir de [modules globaux](../automation/automation-integration-modules.md) utilisés par vos runbooks, car ils sont toujours disponibles dans votre compte Automation.  Vous devez inclure une ressource pour tout module utilisé par vos runbooks.

Les [modules d’intégration](../automation/automation-integration-modules.md) sont de type **Microsoft.Automation/automationAccounts/modules** et ont la structure suivante.  Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


Les propriétés des ressources de module sont décrites dans le tableau suivant.

| Propriété | Description |
|:--- |:--- |
| contentLink |Spécifie le contenu du module. <br><br>uri : Uri du contenu du module.  Il s’agit d’un fichier .ps1 pour des runbooks PowerShell et Script et d’un fichier de runbook graphique exporté pour un runbook Graph.  <br> version : version du module pour votre propre suivi. |

Le runbook doit dépendre de la ressource de module pour vous assurer que cette dernière est créée avant le runbook.

### <a name="updating-modules"></a>Mise à jour des modules
Si vous mettez à jour une solution de gestion qui inclut un runbook qui utilise une planification alors que la nouvelle version de votre solution comprend un nouveau module utilisé par ce runbook, le runbook peut utiliser l’ancienne version du module.  Vous devez inclure les runbooks suivants dans votre solution et créer une tâche à exécuter avant les autres runbooks.  Cela garantit que tous les modules sont mis à jour comme exigé avant que les runbooks ne soient chargés.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/DisplayScript) vous assure que tous les modules utilisés par des runbooks de votre solution sont à la version la plus récente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/DisplayScript) enregistre de nouveau toutes les ressources de planification pour vous assurer que les runbooks qui y sont liés utilisent les modules actuels.




## <a name="sample"></a>Exemple
L’exemple de solution ci-après inclut les ressources suivantes :

- Runbook.  Il s’agit d’un exemple de runbook stocké dans un référentiel GitHub public.
- Un travail d’automatisation qui démarre le runbook lorsque la solution est installée.
- Planification et planification de travaux pour démarrer le runbook à intervalles réguliers.
- Certificat.
- Informations d'identification.
- Variable.
- Module.  Il s’agit du [module OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) pour écrire des données dans Log Analytics. 

L’exemple utilise des variables de [paramètres de solution standard](operations-management-suite-solutions-solution-file.md#parameters) qui seraient généralement utilisées dans une solution, par opposition aux valeurs de codage en dur dans les définitions de ressource.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for shedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Étapes suivantes
* [Ajout d’une vue à votre solution](operations-management-suite-solutions-resources-views.md) pour visualiser les données collectées.
