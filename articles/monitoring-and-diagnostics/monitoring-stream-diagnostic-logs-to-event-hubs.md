---
title: Diffuser en continu les journaux de diagnostic Azure vers un hub d’événements | Microsoft Docs
description: Découvrez comment diffuser en continu les journaux de diagnostic Azure vers un hub d’événements.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: johnkem
ms.openlocfilehash: 72876e38f77aa7a13c0dd9a8cdf9479e058f4a0d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Diffuser en continu les journaux de diagnostic Azure vers un hub d’événements
Les **[journaux de diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)** peuvent être diffusés quasiment en temps réel vers n’importe quelle application à l’aide de l’option « Exporter vers Event Hubs » intégrée au portail, ou en activant l’ID de règle d’autorisation Event Hubs dans un paramètre de diagnostic via les cmdlets Azure PowerShell ou l’interface de ligne de commande Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>Ce que vous pouvez faire avec les journaux de diagnostic et Event Hubs
Voici quelques façons d’utiliser la fonctionnalité de diffusion en continu pour les journaux de diagnostic :

* **Diffuser en continu les journaux vers des systèmes de journalisation et de télémétrie tiers** : vous pouvez diffuser tous vos journaux de diagnostic vers un hub d’événements unique pour envoyer les données de journal vers un outil analytique de journalisation ou STEM tiers.
* **Afficher l’état d’intégrité du service en diffusant des données de chemin réactif vers PowerBI** : en utilisant Event Hubs, Stream Analytics et PowerBI, vous pouvez facilement transformer vos données de diagnostic en informations en temps réel sur vos services Azure. [Cette documentation vous explique comment configurer un client Event Hubs, traiter les données avec Stream Analytics et utiliser PowerBI comme sortie](../stream-analytics/stream-analytics-power-bi-dashboard.md). Voici quelques conseils pour la configuration des journaux de diagnostic :
  
  * Un hub d’événements est automatiquement créé pour une catégorie de journaux de diagnostic lorsque vous activez l’option dans le portail ou par le biais de PowerShell. Sélectionnez le hub d’événements dans l’espace de noms dont le nom commence par **insights-**.
  * Le code SQL suivant est un exemple de requête Stream Analytics que vous pouvez utiliser pour analyser toutes les données de journal dans une table PowerBI :

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the PowerBI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Créer une plateforme de journalisation et de télémétrie personnalisée** : si vous disposez déjà d’une plateforme de télémétrie personnalisée, ou si vous envisagez d’en créer une, la nature hautement évolutive de publication et d’abonnement d’Event Hubs vous permet d’intégrer avec souplesse les journaux de diagnostic. [Consultez ici le guide de Dan Rosanova sur l’utilisation d’Event Hubs dans une plateforme de télémétrie à échelle mondiale.](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)

## <a name="enable-streaming-of-diagnostic-logs"></a>Activer la diffusion en continu des journaux de diagnostic
Vous pouvez activer la diffusion en continu des journaux de diagnostic par programme, via le portail ou à l’aide des [API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). Dans tous les cas, vous créez un paramètre de diagnostic dans lequel vous spécifiez un espace de noms Event Hubs et les catégories de journal et les indicateurs de performance que vous voulez envoyer dans l’espace de noms. Un hub d’événements est créé dans l’espace de noms pour chaque catégorie de journal que vous activez. Une **catégorie de journal** de diagnostic est un type de journal qu’une ressource peut collecter.

> [!WARNING]
> L’activation et la diffusion en continu de journaux de diagnostic à partir de ressources de calcul (par exemple, les machines virtuelles ou Service Fabric) [nécessitent des étapes de configuration différentes](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

Il n’est pas nécessaire que l’espace de noms Event Hubs se trouve dans le même abonnement que la ressource générant des journaux, à condition que l’utilisateur configurant le paramètre ait un accès RBAC approprié aux deux abonnements.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diffuser en continu les journaux de diagnostic à l’aide du portail
1. Dans le portail, accédez à Azure Monitor, puis cliquez sur **Paramètres de diagnostic**

    ![Section Surveillance d’Azure Monitor](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. Vous pouvez également filtrer la liste par type ou groupe de ressources, puis cliquez sur la ressource pour laquelle vous souhaitez définir un paramètre de diagnostic.

3. S’il n’existe aucun paramètre sur la ressource que vous avez sélectionnée, vous êtes invité à créer un paramètre. Cliquez sur « Activer les diagnostics ».

   ![Ajouter le paramètre de diagnostic - aucun paramètre existant](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   S’il existe des paramètres existants sur la ressource, vous verrez une liste de paramètres déjà configurés sur cette ressource. Cliquez sur « Ajouter le paramètre de diagnostic ».

   ![Ajouter le paramètre de diagnostic - paramètres existants](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. Donnez un nom à votre définition et cochez la case **Diffuser en continu vers un hub d’événements**, puis sélectionnez un espace de noms Event Hubs.
   
   ![Ajouter le paramètre de diagnostic - paramètres existants](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)
    
   L’espace de noms sélectionné sera l’espace où le hub d’événements sera créé (si c’est la première fois que vous diffusez en continu des journaux de diagnostic) ou vers lequel le hub d’événements diffusera les journaux (si des ressources diffusent déjà cette catégorie de journal vers cet espace de noms). La stratégie définit les autorisations dont dispose le mécanisme de diffusion en continu. À l’heure actuelle, la diffusion vers un hub d’événements requiert des autorisations de gestion, d’envoi et d’écoute. Vous pouvez créer ou modifier les stratégies d’accès partagé de l’espace de noms Event Hubs dans le portail sous l’onglet Configurer pour votre espace de noms. Pour mettre à jour l’un de ces paramètres de diagnostic, le client doit avoir l’autorisation ListKey sur la règle d’autorisation Event Hubs. Vous pouvez éventuellement spécifier un nom de hub d’événements. Si vous spécifiez un nom de hub d’événements, les journaux sont acheminés vers ce hub d’événements plutôt que vers un nouveau hub d’événements selon la catégorie de journal.

4. Cliquez sur **Enregistrer**.

Après quelques instants, le nouveau paramètre apparaît dans la liste des paramètres de cette ressource, et les journaux de diagnostic sont diffusés en continu dans ce hub d’événements dès que de nouvelles données d’événement sont générées.

### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell
Pour activer la diffusion en continu via les [applets de commande Azure PowerShell](insights-powershell-samples.md), vous pouvez utiliser l’applet de commande `Set-AzureRmDiagnosticSetting` avec ces paramètres :

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

L’ID de règle d’autorisation du concentrateur d’événements est une chaîne au format suivant : `{Event Hub namespace resource ID}/authorizationrules/{key name}`, par exemple, `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Actuellement, il est impossible de sélectionner un nom de hub d’événements particulier avec PowerShell.

### <a name="via-azure-cli"></a>Via l’interface de ligne de commande Azure
Pour activer la diffusion en continu via [l’interface de ligne de commande Azure](insights-cli-samples.md), vous pouvez utiliser la commande `insights diagnostic set` comme suit :

```azurecli
azure insights diagnostic set --resourceId <resourceID> --serviceBusRuleId <serviceBusRuleID> --enabled true
```

Pour l’ID de règle d’autorisation du concentrateur d’événements, utilisez le même format que celui indiqué pour l’applet de commande PowerShell. Actuellement, il est impossible de sélectionner un nom de hub d’événements particulier avec l’interface de ligne de commande Azure.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Comment utiliser les données de journal d’Event Hubs ?
Voici des exemples de données de sortie provenant d’Event Hubs :

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nom de l’élément | Description |
| --- | --- |
| records |Un tableau regroupant tous les événements de journal de cette charge utile. |
| time |L’heure à laquelle l’événement s’est produit. |
| category |La catégorie de journal associée à cet événement. |
| ResourceId |L’ID de la ressource qui a généré cet événement. |
| operationName |Nom de l’opération. |
| level |facultatif. Indique le niveau de l’événement de journal. |
| properties |Les propriétés de l’événement. |

Une liste de tous les fournisseurs de ressources qui prennent en charge la diffusion en continu vers Event Hubs est disponible [ici](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Diffusion de données à partir des ressources de calcul
Vous pouvez également diffuser en continu des journaux de diagnostic à partir des ressources de calcul à l’aide de l’agent Windows Azure Diagnostics. [Consultez cet article](../event-hubs/event-hubs-streaming-azure-diags-data.md) pour découvrir comment configurer cela.

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les journaux de diagnostic Azure](monitoring-overview-of-diagnostic-logs.md)
* [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

