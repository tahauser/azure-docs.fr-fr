---
title: Créer des alertes pour les services Azure - PowerShell | Microsoft Docs
description: Déclenchez des e-mails et des notifications, appelez des URL de sites web (webhooks) ou déclenchez une automatisation lorsque les conditions spécifiées sont remplies.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
ms.openlocfilehash: 03026ee8bedd5277b2bb6cf28dabafabf207b0ae
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-classic-metric-alerts-in-azure-monitor-for-azure-services---powershell"></a>Créer des alertes de métriques classiques dans Azure Monitor pour les Services Azure - PowerShell
> [!div class="op_single_selector"]
> * [Portail](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [INTERFACE DE LIGNE DE COMMANDE](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Vue d'ensemble

> [!NOTE]
> Cet article décrit comment créer des alertes de métriques plus anciennes. Désormais, Azure Monitor prend en charge des [alertes de métriques plus récentes et plus performantes](monitoring-near-real-time-metric-alerts.md). Ces alertes peuvent surveiller plusieurs métriques, et permettent d’obtenir des alertes sur des métriques dimensionnelles. La prise en charge de PowerShell pour les alertes de métriques plus récentes sera bientôt disponible.
>
>

Cet article vous montre comment configurer des alertes de métrique Azure avec PowerShell.  

Vous pouvez recevoir une alerte en fonction de métriques de surveillance pour vos services Azure ou d'événements sur ces derniers.

* **Valeurs de métriques** : l’alerte se déclenche lorsque la valeur d’une métrique spécifiée dépasse un seuil que vous affectez dans un des deux sens. C’est-à-dire que le déclenchement se fait à la fois lorsque la condition est remplie et par la suite une fois que la condition n’est plus remplie.    
* **Événements du journal d’activité** : une alerte peut se déclencher sur *chaque* événement ou seulement quand un certain événement se produit. Pour plus d’informations sur les alertes du journal d’activité, [cliquez ici](monitoring-activity-log-alerts.md)

Vous pouvez configurer une alerte de métrique pour effectuer les opérations suivantes lors de son déclenchement :

* envoyer des notifications par courrier électronique à l’administrateur du service et aux coadministrateurs
* envoyer un courrier électronique à d’autres adresses que vous spécifiez.
* appeler un webhook
* démarrer l’exécution d’un runbook Azure (uniquement à partir du Portail Azure)

Vous pouvez configurer et obtenir des informations sur les règles d’alerte avec

* [Portail Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [interface de ligne de commande (CLI)](insights-alerts-command-line-interface.md)
* [API REST Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Pour obtenir des informations complémentaires, vous pouvez à tout moment taper ```Get-Help``` , suivi de la commande PowerShell sur laquelle vous souhaitez de l’aide.

## <a name="create-alert-rules-in-powershell"></a>Créer des règles d’alerte dans PowerShell
1. Connectez-vous à Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```
2. Récupérez la liste des abonnements dont vous disposez. Vérifiez que vous travaillez avec le bon abonnement. Dans le cas contraire, choisissez le bon à l’aide de la sortie de `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Pour répertorier les règles existantes sur un groupe de ressources, utilisez la commande suivante :

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Pour créer une règle, vous avez d’abord besoin de différentes informations importantes.

  * **L’ID de la ressource** pour laquelle vous souhaitez définir une alerte
  * Les **définitions de métriques** disponibles pour cette ressource

     Pour obtenir l’ID de la ressource, vous pouvez utiliser le Portail Azure. À supposer que la ressource soit déjà créée, sélectionnez-la dans le portail. Puis, dans le panneau suivant, sélectionnez *Propriétés* sous la section *Paramètres*. **ID DE RESSOURCE** est un champ du panneau suivant. Une autre méthode consiste à utiliser [l’Explorateur de ressources Azure](https://resources.azure.com/).

     Voici un exemple d’ID de ressource d’une application web

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Vous pouvez utiliser `Get-AzureRmMetricDefinition` pour afficher la liste de toutes les définitions de métriques d’une ressource spécifique.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     L’exemple suivant génère une table avec le nom de la métrique et son unité.

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     La liste complète des options disponibles pour Get-AzureRmMetricDefinition s’obtient en exécutant `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. L’exemple suivant configure une alerte sur une ressource de site web. L’alerte se déclenche chaque fois qu’il reçoit constamment du trafic pendant cinq minutes, et à nouveau lorsqu’il ne reçoit aucun trafic pendant cinq minutes.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Pour créer un webhook ou envoyer un courrier électronique lorsqu’une alerte se déclenche, commencez par créer le message et/ou les webhooks. Puis créez la règle immédiatement après avec la balise -Actions, comme l’indique l’exemple suivant. Vous ne pouvez pas associer un webhook ou des messages électroniques à des règles déjà créées à l’aide de PowerShell.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Pour vérifier que vos alertes ont été correctement créées en examinant les règles individuelles.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Supprimez vos alertes. Ces commandes suppriment les règles créées précédemment dans cet article.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Consultez une vue d’ensemble de la surveillance Azure](monitoring-overview.md) , notamment les types d’informations que vous pouvez collecter et surveiller.
* Découvrez plus en détail la [configuration des webhooks dans les alertes](insights-webhooks-alerts.md).
* Découvrez plus d’informations sur la [configuration des alertes sur les événements de journal d’activité](monitoring-activity-log-alerts.md).
* Découvrez plus en détails les [runbooks Azure Automation](../automation/automation-starting-a-runbook.md).
* Consultez une [vue d’ensemble de la collecte des journaux de diagnostic](monitoring-overview-of-diagnostic-logs.md) pour collecter des métriques détaillées à fréquence élevée sur votre service.
* Consultez une [vue d’ensemble de la collecte des métriques](insights-how-to-customize-monitoring.md) pour vous assurer que votre service est disponible et réactif.
