---
title: "Alertes et recherches enregistrées dans les solutions OMS | Microsoft Docs"
description: "Les solutions dans OMS incluent généralement des recherches enregistrées dans Log Analytics pour analyser les données collectées par la solution.  Elles peuvent également définir des alertes pour avertir l’utilisateur ou appliquer automatiquement une action en réponse à un problème critique.  Cet article décrit comment définir les alertes et recherches enregistrées de Log Analytics dans un modèle Resource Manager, de sorte qu’elles puissent être incluses dans des solutions de gestion."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e25ad9b9be6d02550b4be9c09496021cd7fe2d2
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-oms-management-solution-preview"></a>Ajout de recherches et d’alertes enregistrées Log Analytics à une solution de gestion OMS (préversion)

> [!NOTE]
> Il s’agit d’une documentation préliminaire pour la création de solutions de gestion dans OMS qui sont actuellement en préversion. Tout schéma décrit ci-dessous est susceptible d’être modifié.   


Les [solutions de gestion dans OMS](operations-management-suite-solutions.md) incluent généralement des [recherches enregistrées](../log-analytics/log-analytics-log-searches.md) dans Log Analytics pour analyser les données collectées par la solution.  Elles peuvent également définir des [alertes](../log-analytics/log-analytics-alerts.md) pour avertir l’utilisateur ou appliquer automatiquement une action en réponse à un problème critique.  Cet article décrit comment définir les alertes et recherches enregistrées de Log Analytics dans un [modèle Resource Manager](../resource-manager-template-walkthrough.md), de sorte qu’elles puissent être incluses dans des [solutions de gestion](operations-management-suite-solutions-creating.md).

> [!NOTE]
> Les exemples dans cet article utilisent des paramètres et des variables qui sont nécessaires ou courants pour les solutions de gestion. Ils sont décrits dans la rubrique [Création de solutions de gestion dans Operations Management Suite (OMS)](operations-management-suite-solutions-creating.md)  

## <a name="prerequisites"></a>configuration requise
Cet article suppose que vous êtes déjà familiarisé avec la [création d’une solution de gestion](operations-management-suite-solutions-creating.md) et la structure d’un [modèle Resource Manager](../resource-group-authoring-templates.md) et d’un fichier solution.


## <a name="log-analytics-workspace"></a>Espace de travail Log Analytics
Toutes les ressources dans Log Analytics sont contenues dans un [espace de travail](../log-analytics/log-analytics-manage-access.md).  Comme décrit dans [Espace de travail OMS et compte Automation](operations-management-suite-solutions.md#log-analytics-workspace-and-automation-account), l’espace de travail n’est pas inclus dans la solution de gestion mais doit exister avant l’installation de la solution.  Si ce n’est pas le cas, l’installation de la solution échoue.

Le nom de l’espace de travail figure dans le nom de chaque ressource Log Analytics.  Pour cela, utilisez le paramètre **workspace** comme dans l’exemple suivant, utilisant une ressource savedsearch.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Version de l’API Log Analytics
Toutes les ressources Log Analytics définies dans un modèle Resource Manager ont une propriété **apiVersion** qui définit la version de l’API que la ressource doit utiliser.  Cette version varie selon que les ressources utilisent [le langage de requête hérité ou mis à niveau](../log-analytics/log-analytics-log-search-upgrade.md).  

 Le tableau suivant indique les versions de l’API Log Analytics pour les recherches enregistrées dans des espaces de travail hérités et mis à niveau : 

| Version de l’espace de travail | Version de l'API | Requête |
|:---|:---|:---|
| v1 (hérité)   | 2015-11-01-preview | Format hérité.<br> Exemple : Type=Event EventLevelName = Error  |
| v2 (mis à niveau) | 2015-11-01-preview | Format hérité.  Converti au format mis à niveau durant l’installation.<br> Exemple : Type=Event EventLevelName = Error<br>Converti au format : Event &#124; where EventLevelName == "Error"  |
| v2 (mis à niveau) | 2017-03-03-preview | Format mis à niveau. <br>Exemple : Event &#124; where EventLevelName == "Error"  |



## <a name="saved-searches"></a>Recherches enregistrées
Ajoutez des [recherches enregistrées](../log-analytics/log-analytics-log-searches.md) à une solution pour permettre aux utilisateurs d’interroger les données collectées par votre solution.  Les recherches enregistrées apparaissent sous **Favoris** dans le portail OMS et sous **Recherches enregistrées** dans le portail Azure.  Une recherche enregistrée est également requise pour chaque alerte.   

Les ressources de [recherche enregistrée Log Analytics](../log-analytics/log-analytics-log-searches.md) ont le type `Microsoft.OperationalInsights/workspaces/savedSearches` et présentent la structure suivante.  Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }



Chaque propriété d’une recherche enregistrée est décrite dans le tableau suivant. 

| Propriété | Description |
|:--- |:--- |
| category | La catégorie de la recherche enregistrée.  Toutes les recherches enregistrées d’une même solution partagent généralement une catégorie unique. Elles sont ainsi regroupées dans la console. |
| displayname | Le nom à afficher pour la recherche enregistrée dans le portail. |
| query | Requête à exécuter. |

> [!NOTE]
> Vous devrez peut-être utiliser des caractères d’échappement dans la requête si elle inclut des caractères qui peuvent être interprétés comme du code JSON.  Par exemple, si votre recherche était **Type:AzureActivity OperationName:"Microsoft.Compute/virtualMachines/write"**, elle doit être écrite de la façon suivante dans le fichier solution : **Type:AzureActivity OperationName:\"Microsoft.Compute/virtualMachines/write\"**.

## <a name="alerts"></a>Alertes
Les [alertes Log Analytics](../log-analytics/log-analytics-alerts.md) sont créées par le biais de règles d’alerte exécutant une recherche enregistrée à intervalles réguliers.  Si les résultats de la requête correspondent aux critères spécifiés, un enregistrement d’alerte est créé et une ou plusieurs actions sont exécutées.  

Les règles d’alerte d’une solution de gestion sont constituées des trois ressources suivantes.

- **Recherche enregistrée.**  Définit la recherche dans les journaux qui est exécutée.  Plusieurs règles d’alerte peuvent partager une même recherche enregistrée.
- **Planification.**  Définit la fréquence d’exécution de la recherche dans les journaux.  Chaque règle d’alerte est associée à une planification unique.
- **Action d’alerte.**  Chaque règle d’alerte est associée à une ressource d’action avec un type **Alert**, qui définit les détails de l’alerte, notamment les critères déterminant quand un enregistrement d’alerte est créé et la gravité de l’alerte.  La ressource d’action peut éventuellement définir une réponse par e-mail et basée sur un runbook.
- **Action webhook (facultative).**  Si la règle d’alerte appelle un webhook, elle nécessite une ressource d’action supplémentaire de type **Webhook**.    

Les ressources de recherche enregistrée sont décrites ci-dessus.  Les autres ressources sont décrites ci-dessous.


### <a name="schedule-resource"></a>Ressource de planification

Une recherche enregistrée peut avoir une ou plusieurs planifications, chacune d’entre elles représentant une règle d’alerte distincte. La planification définit la fréquence à laquelle la recherche est exécutée et l’intervalle de temps pendant lequel les données sont récupérées.  Les ressources de planification ont le type `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` et présentent la structure suivante. Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 


    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }



Les propriétés des ressources de planification sont décrites dans le tableau suivant.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| Activé       | OUI | Spécifie si l’alerte est activée lors de sa création. |
| interval      | OUI | La fréquence d’exécution de la requête (en minutes). |
| queryTimeSpan | OUI | Durée sur laquelle les résultats sont évalués (en minutes). |

La ressource de planification doit dépendre de la recherche enregistrée, de sorte qu’elle soit créée avant la planification.


### <a name="actions"></a>Actions
La propriété **Type** spécifie deux types de ressources d’action.  Une planification requiert une action **Alert** qui définit les détails de la règle d’alerte et les actions appliquées lors de la création d’une alerte.  Elle peut également inclure une action **Webhook** si un webhook doit être appelé à partir de l’alerte.  

Les ressources d’action ont un type `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`.  

#### <a name="alert-actions"></a>Actions d’alerte

Chaque planification est associée à une action **Alert**.  Ceci définit les détails de l’alerte et, éventuellement, les actions de notification et de correction.  Une notification envoie un e-mail à une ou plusieurs adresses.  Une correction démarre un runbook dans Azure Automation pour tenter de résoudre le problème détecté.

Les actions d’alerte présentent la structure suivante.  Cela inclut des variables et des paramètres courants, vous pouvez donc copier et coller cet extrait de code dans votre fichier de solution et modifier les noms des paramètres. 



    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
        ],
        "properties": {
            "etag": "*",
            "type": "Alert",
            "name": "[variables('Alert').Name]",
            "description": "[variables('Alert').Description]",
            "severity": "[variables('Alert').Severity]",
            "threshold": {
                "operator": "[variables('Alert').Threshold.Operator]",
                "value": "[variables('Alert').Threshold.Value]",
                "metricsTrigger": {
                    "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                    "operator": "[variables('Alert').Trigger.Operator]",
                    "value": "[variables('Alert').Trigger.Value]"
                },
            },
            "emailNotification": {
                "recipients": [
                    "[variables('Alert').Recipients]"
                ],
                "subject": "[variables('Alert').Subject]"
            },
            "remediation": {
                "runbookName": "[variables('Alert').Remedition.RunbookName]",
                "webhookUri": "[variables('Alert').Remedition.WebhookUri]"
            }
        }
    }

Les propriétés des ressources d’action d’alerte sont décrites dans les tableaux suivants.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| type | OUI | Type de l’action.  **Alert** pour les actions d’alerte. |
| NOM | OUI | Nom d’affichage de l’alerte.  Il s’agit du nom qui s’affiche dans la console pour la règle d’alerte. |
| Description | Non  | La description facultative de l’alerte. |
| Severity | OUI | La gravité de l’enregistrement d’alerte selon les valeurs suivantes :<br><br> **Critical**<br>**Avertissement**<br>**Informational** |


##### <a name="threshold"></a>Seuil
Cette section est obligatoire.  Elle définit les propriétés du seuil d’alerte.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| Operator | OUI | L’opérateur de comparaison selon les valeurs suivantes :<br><br>**gt = supérieur à<br>lt = inférieur à** |
| Valeur | OUI | La valeur par rapport à laquelle les résultats doivent être comparés. |


##### <a name="metricstrigger"></a>MetricsTrigger
Cette section est facultative.  Vous devez l’inclure pour une alerte relative aux mesures métriques.

> [!NOTE]
> Les alertes relatives aux mesures métriques sont actuellement en version préliminaire publique. 

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| TriggerCondition | OUI | Spécifie si le seuil est défini pour le nombre total de violations ou pour des violations consécutives selon les valeurs suivantes :<br><br>**Total<br>Consecutive** |
| Operator | OUI | L’opérateur de comparaison selon les valeurs suivantes :<br><br>**gt = supérieur à<br>lt = inférieur à** |
| Valeur | OUI | Le nombre de fois où les critères doivent être respectés pour que l’alerte soit déclenchée. |

##### <a name="throttling"></a>Limitation
Cette section est facultative.  Insérez cette section si vous souhaitez supprimer des alertes à partir de la même règle sur une certaine période après la création d’une alerte.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| DurationInMinutes | Oui, si l’élément Throttling est inclus | Le délai de suppression des alertes, en minutes, une fois créée une alerte à partir de la même règle. |

##### <a name="emailnotification"></a>EmailNotification
 Cette section est facultative. Insérez-la si vous souhaitez que l’alerte envoie un e-mail à un ou plusieurs destinataires.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| Destinataires | OUI | Liste des adresses e-mail (séparées par des virgules) auxquelles une notification est envoyée quand une alerte est créée, comme dans l’exemple suivant.<br><br>**[ "recipient1@contoso.com", "recipient2@contoso.com" ]** |
| Objet | OUI | La ligne d’objet du message. |
| Pièce jointe | Non  | Actuellement, les pièces jointes ne sont pas prises en charge.  Si cet élément est inclus, il doit avoir la valeur **None**. |


##### <a name="remediation"></a>Correction
Cette section est facultative. Insérez-la si vous souhaitez qu’un runbook démarre en réponse à l’alerte. |

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| RunbookName | OUI | Le nom du runbook à démarrer. |
| WebhookUri | OUI | L’URI du webhook pour le runbook. |
| Expiry | Non  | La date et l’heure d’expiration de la correction. |

#### <a name="webhook-actions"></a>Actions de webhook

Les actions de webhook démarrent un processus en appelant une URL et, éventuellement, en fournissant une charge utile à envoyer. Elles sont similaires aux actions correctives, à la différence qu’elles sont destinées à des webhooks qui peuvent appeler des processus autres que des runbooks Azure Automation. En outre, elles permettent de fournir une charge utile à remettre au processus distant.

Si votre alerte appelle un webhook, elle nécessite une ressource d’action avec un type **Webhook** en plus de la ressource d’action **Alerte**.  

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }

Les propriétés des ressources d’action Webhook sont décrites dans les tableaux suivants.

| Nom de l'élément | Obligatoire | Description |
|:--|:--|:--|
| Type | OUI | Type de l’action.  **Webhook** pour les actions de webhook. |
| Nom | OUI | Le nom d’affichage de l’action.  Il n’est pas affiché dans la console. |
| wehookUri | OUI | L’URI du webhook. |
| customPayload | Non  | Charge utile personnalisée à envoyer au webhook. Le format dépend de ce que le webhook attend. |




## <a name="sample"></a>Exemple

L’exemple de solution ci-après inclut les ressources suivantes :

- Recherche enregistrée
- Planification
- Action d’alerte
- Action webhook

L’exemple utilise des variables de [paramètres de solution standard](operations-management-suite-solutions-solution-file.md#parameters) qui seraient généralement utilisées dans une solution, par opposition aux valeurs de codage en dur dans les définitions de ressource.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0",
        "parameters": {
          "workspaceName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Log Analytics workspace"
            }
          },
          "accountName": {
            "type": "string",
            "metadata": {
              "Description": "Name of Automation account"
            }
          },
          "workspaceregionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Log Analytics workspace"
            }
          },
          "regionId": {
            "type": "string",
            "metadata": {
              "Description": "Region of Automation account"
            }
          },
          "pricingTier": {
            "type": "string",
            "metadata": {
              "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
          },
          "recipients": {
            "type": "string",
            "metadata": {
              "Description": "List of recipients for the email alert separated by semicolon"
            }
          }
        },
        "variables": {
          "SolutionName": "MySolution",
          "SolutionVersion": "1.0",
          "SolutionPublisher": "Contoso",
          "ProductName": "SampleSolution",
    
          "LogAnalyticsApiVersion": "2015-11-01-preview",
    
          "MySearch": {
            "displayName": "Error records by hour",
            "query": "Type=MyRecord_CL | measure avg(Rating_d) by Instance_s interval 60minutes",
            "category": "Samples",
            "name": "Samples-Count of data"
          },
          "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert.  Fires when 3 error records found over hour interval.",
            "Severity": "Critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
              "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
              "Interval": 15,
              "TimeSpan": 60
            },
            "MetricsTrigger": {
              "TriggerCondition": "Consecutive",
              "Operator": "gt",
              "Value": 3
            },
            "ThrottleMinutes": 60,
            "Notification": {
              "Recipients": [
                "[parameters('recipients')]"
              ],
              "Subject": "Sample alert"
            },
            "Remediation": {
              "RunbookName": "MyRemediationRunbook",
              "WebhookUri": "https://s1events.azure-automation.net/webhooks?token=TluBFH3GpX4IEAnFoImoAWLTULkjD%2bTS0yscyrr7ogw%3d"
            },
            "Webhook": {
              "Name": "MyWebhook",
              "Uri": "https://MyService.com/webhook",
              "Payload": "{\"field1\":\"value1\",\"field2\":\"value2\"}"
            }
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
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
              "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
            ],
            "properties": {
              "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
              "referencedResources": [
              ],
              "containedResources": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Webhook.Name)]"
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
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
              "etag": "*",
              "query": "[variables('MySearch').query]",
              "displayName": "[variables('MySearch').displayName]",
              "category": "[variables('MySearch').category]"
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
              "etag": "*",
              "interval": "[variables('MyAlert').Schedule.Interval]",
              "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
              "enabled": true
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/',  variables('MyAlert').Schedule.Name, '/',  variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/',  variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Alert",
              "Name": "[variables('MyAlert').DisplayName]",
              "Description": "[variables('MyAlert').Description]",
              "Severity": "[variables('MyAlert').Severity]",
              "Threshold": {
                "Operator": "[variables('MyAlert').ThresholdOperator]",
                "Value": "[variables('MyAlert').ThresholdValue]",
                "MetricsTrigger": {
                  "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                  "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                  "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                }
              },
              "Throttling": {
                "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
              },
              "EmailNotification": {
                "Recipients": "[variables('MyAlert').Notification.Recipients]",
                "Subject": "[variables('MyAlert').Notification.Subject]",
                "Attachment": "None"
              },
              "Remediation": {
                "RunbookName": "[variables('MyAlert').Remediation.RunbookName]",
                "WebhookUri": "[variables('MyAlert').Remediation.WebhookUri]"
              }
            }
          },
          {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Webhook.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion')]",
            "dependsOn": [
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]",
              "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name, '/actions/',variables('MyAlert').Name)]"
            ],
            "properties": {
              "etag": "*",
              "Type": "Webhook",
              "Name": "[variables('MyAlert').Webhook.Name]",
              "WebhookUri": "[variables('MyAlert').Webhook.Uri]",
              "CustomPayload": "[variables('MyAlert').Webhook.Payload]"
            }
          }
        ]
    }


Le fichier de paramètres suivant fournit des exemples de valeurs pour cette solution.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspacename": {
                "value": "myWorkspace"
            },
            "accountName": {
                "value": "myAccount"
            },
            "workspaceregionId": {
                "value": "East US"
            },
            "regionId": {
                "value": "East US 2"
            },
            "pricingTier": {
                "value": "Free"
            },
            "recipients": {
                "value": "recipient1@contoso.com;recipient2@contoso.com"
            }
        }
    }


## <a name="next-steps"></a>étapes suivantes
* [Ajoutez des vues](operations-management-suite-solutions-resources-views.md) à votre solution de gestion.
* [Ajoutez des runbooks Automation et d’autres ressources](operations-management-suite-solutions-resources-automation.md) à votre solution de gestion.

