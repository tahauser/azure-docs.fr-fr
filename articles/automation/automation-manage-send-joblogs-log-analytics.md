---
title: "Transférer des données de travaux Azure Automation à OMS Log Analytics | Microsoft Docs"
description: "Cet article montre comment envoyer des données d’état de travaux et des flux de travaux de runbook à Microsoft Operations Management Suite Log Analytics afin de renforcer la visibilité et la simplicité de gestion."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: tysonn
ms.assetid: c12724c6-01a9-4b55-80ae-d8b7b99bd436
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2017
ms.author: magoedte
ms.openlocfilehash: 0319a7b9248dec9d7cdabba9c18a25463d94284b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Transférer l’état d’un travail et des flux de travail d’Automation vers Log Analytics (OMS)
Automation peut envoyer l’état d’un travail de runbook et des flux de travail vers votre espace de travail Log Analytics dans Microsoft Operations Management Suite (OMS). Les journaux de travail et les flux de travail sont visibles dans le portail Azure, ou avec PowerShell, pour des travaux individuels. Cela vous permet d’effectuer des enquêtes simples. Désormais avec Log Analytics, vous pouvez :

* Obtenir des informations sur vos travaux Automation.
* Déclencher un e-mail ou une alerte en fonction du statut de votre travail de runbook (par exemple, échec ou état suspendu).
* Écrire des requêtes avancées sur plusieurs flux de travaux.
* Mettre en corrélation des travaux sur différents comptes Automation.
* Visualiser votre historique des travaux dans le temps.

## <a name="prerequisites-and-deployment-considerations"></a>Conditions préalables et considérations relatives au déploiement
Pour commencer à envoyer vos journaux Automation à Log Analytics, vous devez disposer des éléments suivants :

* Version de novembre 2016 ou une version ultérieure d’[Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
* Un espace de travail Log Analytics. Pour plus d’informations, consultez l’article [Prise en main de Log Analytics](../log-analytics/log-analytics-get-started.md). 
* L’ID de ressource de votre compte Azure Automation.


Pour trouver l’ID de ressource de votre compte Azure Automation :

```powershell-interactive
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
```

Pour trouver l’ID de ressource de votre espace de travail Log Analytics, exécutez la commande PowerShell suivante :

```powershell-interactive
# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Si vous possédez plusieurs comptes Automation ou plusieurs espaces de travail, recherchez le *Nom* à configurer dans la sortie des commandes précédentes et copiez la valeur de *ResourceId*.

Pour rechercher Le *Nom* de votre compte Automation, sélectionnez votre compte Automation à partir du panneau **Compte Automation** du portail Azure, puis sélectionnez **Tous les paramètres**. Dans le panneau **Tous les paramètres**, sous **Paramètres du compte**, sélectionnez **Propriétés**.  Dans le panneau **Propriétés**, vous pouvez observer ces valeurs.<br> ![Propriétés du compte Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Configurer l’intégration avec Log Analytics

1. Sur votre ordinateur, démarrez **Windows PowerShell** à partir de l’écran **Démarrer**.
2. Exécutez la commande PowerShell suivante, et remplacez la valeur de `[your resource id]` et de `[resource id of the log analytics workspace]` par celles de l’étape précédente.

   ```powershell-interactive
   $workspaceId = "[resource id of the log analytics workspace]"
   $automationAccountId = "[resource id of your automation account]"

   Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true
   ```

Une fois ce script exécuté, les enregistrements s’affichent dans Log Analytics dans les 10 minutes suivant l’écriture des nouveaux JobLogs ou JobStreams.

Pour afficher les journaux, exécutez la requête suivante dans la recherche de journal de Log Analytics : `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION""`

### <a name="verify-configuration"></a>Vérifier la configuration
Pour vous assurer que votre compte Automation envoie des journaux à votre espace de travail Log Analytics, vérifiez que les diagnostics sont correctement configurés sur le compte Automation à l’aide de la commande PowerShell suivante :

```powershell-interactive
Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

Dans la sortie, assurez-vous que :
+ Sous *Journaux*, la valeur de *Activé* est *True*.
+ La valeur de *WorkspaceId* est définie sur l’ID de ressource de votre espace de travail Log Analytics.

## <a name="log-analytics-records"></a>Enregistrements Log Analytics

Les diagnostics d’Azure Automation créent deux types d’enregistrements dans Log Analytics, sous la balise **AzureDiagnostics**. Les requêtes suivantes utilisent le langage de requête mis à niveau pour Log Analytics. Pour plus d’informations sur les requêtes courantes entre le langage de requête hérité et le nouveau langage de requête Azure Log Analytics, consultez la page [Aide-mémoire du passage du langage de requête hérité au nouveau langage de requête Azure Log Analytics](https://docs.loganalytics.io/docs/Learn/References/Legacy-to-new-to-Azure-Log-Analytics-Language).

### <a name="job-logs"></a>Journaux de travail
| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution du travail du runbook. |
| RunbookName_s |Nom du runbook. |
| Caller_s |Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés. |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| JobId_g |GUID représentant l’ID du travail du runbook. |
| ResultType |L’état du travail du runbook. Les valeurs possibles sont les suivantes :<br>- Nouveau<br>Démarré<br>Arrêté<br>Interrompu<br>Échec<br>- Terminé |
| Catégorie | Classification du type de données. Pour Automation, la valeur est JobLogs. |
| Nom d'opération | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur est Job. |
| Ressource | Nom du compte Automation |
| SourceSystem | Manière dont Log Analytics a collecté les données. Toujours *Azure* pour les diagnostics Azure. |
| resultDescription |Décrit l’état résultant du travail du runbook. Les valeurs possibles sont les suivantes :<br>- Job is started<br>- Job Failed<br>- Job Completed |
| CorrelationId |GUID représentant l’ID de corrélation du travail du runbook. |
| ResourceId |Spécifie l’id de ressource de compte Azure Automation du runbook. |
| SubscriptionId | ID d’abonnement Azure (GUID) du compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |


### <a name="job-streams"></a>Flux de travail
| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution du travail du runbook. |
| RunbookName_s |Nom du runbook. |
| Caller_s |Ce qui a initié l’opération. Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés. |
| StreamType_s |Type de flux de travail. Les valeurs possibles sont les suivantes :<br>progress<br>Sortie<br>Avertissement<br>error<br>DEBUG<br>- Verbose |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| JobId_g |GUID représentant l’ID du travail du runbook. |
| ResultType |L’état du travail du runbook. Les valeurs possibles sont les suivantes :<br>- In Progress |
| Catégorie | Classification du type de données. Pour Automation, la valeur est JobStreams. |
| Nom d'opération | Spécifie le type d’opération exécutée dans Azure. Pour Automation, la valeur est Job. |
| Ressource | Nom du compte Automation |
| SourceSystem | Manière dont Log Analytics a collecté les données. Toujours *Azure* pour les diagnostics Azure. |
| resultDescription |Inclut le flux de sortie du runbook. |
| CorrelationId |GUID représentant l’ID de corrélation du travail du runbook. |
| ResourceId |Spécifie l’id de ressource de compte Azure Automation du runbook. |
| SubscriptionId | ID d’abonnement Azure (GUID) du compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |

## <a name="viewing-automation-logs-in-log-analytics"></a>Affichage d’Automation Logs dans Log Analytics
Maintenant que nous avons commencé à envoyer des journaux de travaux Automation à Log Analytics, nous allons voir comment les utiliser dans Log Analytics.

Pour afficher les journaux, exécutez la requête suivante :`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envoyer un e-mail en cas d’échec ou de suspension d’un travail de runbook
Nos clients demandent souvent à pouvoir envoyer un e-mail ou un SMS lorsqu’une erreur survient sur un travail de runbook.   

Pour créer une règle d’alerte, vous devez commencer par créer une recherche de journal pour les enregistrements de travaux de runbook qui doivent appeler l’alerte. Cliquez sur le bouton **Alerte** pour créer et configurer la règle d’alerte.

1. Dans la page de présentation de Log Analytics, cliquez sur **Recherche de journal**.
2. Créez une requête Recherche dans les journaux pour votre alerte en tapant la recherche suivante dans le champ de requête : `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended")`. Vous pouvez également les regrouper par RunbookName ainsi : `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and (ResultType == "Failed" or ResultType == "Suspended") | summarize AggregatedValue = count() by RunbookName_s`.

   Si vous avez configuré des journaux dans votre espace de travail sur plusieurs abonnements ou comptes Automation, vous pouvez également regrouper vos alertes par abonnement ou par compte Automation. Le nom du compte Automation se trouve dans le champ Ressource de la recherche de JobLogs.
1. Cliquez sur **Alerte** en haut de la page pour ouvrir l’écran **Ajouter une règle d’alerte**. Pour plus d’informations sur les options de configuration de l’alerte, consultez [Comprendre les alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Rechercher tous les travaux qui ont rencontré des erreurs
En plus des alertes concernant les échecs, vous pouvez déterminer lorsqu’une tâche de runbook comporte une erreur sans fin d’exécution. Dans ce cas, PowerShell génère un flux d’erreur. Toutefois, les erreurs sans fin d’exécution ne provoquent la suspension ou l’échec du travail.    

1. Dans votre espace de travail Log Analytics, cliquez sur **Recherche de journal**.
2. Dans le champ de requête, tapez `AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and StreamType_s == "Error" | summarize AggregatedValue = count() by JobId_g`, puis cliquez sur le bouton **Rechercher**.

### <a name="view-job-streams-for-a-job"></a>Afficher les flux de travail pour un travail
Lorsque vous déboguez un travail, vous pouvez également examiner les flux de travaux. La requête ci-dessous montre tous les flux pour un seul travail avec le GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 :   

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobStreams" and JobId_g == "2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort by TimeGenerated asc | project ResultDescription`

### <a name="view-historical-job-status"></a>Afficher l’état de travail historique
Vous pouvez enfin souhaiter visualiser l’historique de vos travaux dans le temps. Vous pouvez utiliser cette requête pour rechercher l’état de vos travaux au fil du temps.

`AzureDiagnostics | where ResourceProvider == "MICROSOFT.AUTOMATION" and Category == "JobLogs" and ResultType != "started" | summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h)`  
<br> ![Graphique d’état de travail historique OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Résumé
L’envoi de vos données de diffusion en continu et d’état des travaux Automation à Log Analytics permet d’obtenir plus de détails sur l’état de vos travaux Automation en :
+ Configurant des alertes pour vous avertir en cas de problème.
+ Utilisant des vues et des requêtes de recherche personnalisées pour visualiser les résultats de votre runbook, l’état du travail de runbook et d’autres indicateurs ou mesures clés associées.  

Log Analytics offre une plus grande visibilité opérationnelle sur vos travaux Automation et peut permettre de traiter les incidents plus rapidement.  

## <a name="next-steps"></a>Étapes suivantes
* Pour savoir comment construire différentes requêtes de recherche et consulter les journaux de travaux Automation avec Log Analytics, consultez la page [Recherches dans les journaux dans Log Analytics](../log-analytics/log-analytics-log-searches.md).
* Pour savoir comment créer et récupérer la sortie et les messages d’erreur de runbooks, consultez la page [Sortie et messages de runbooks](automation-runbook-output-and-messages.md).
* Pour plus d’informations sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et autres détails techniques, voir [Suivi d’une tâche de runbook](automation-runbook-execution.md).
* Pour plus d’informations sur OMS Log Analytics et sur les sources de collecte de données, consultez la page [Collecter des données de stockage Azure dans Log Analytics – Vue d’ensemble](../log-analytics/log-analytics-azure-storage.md).
