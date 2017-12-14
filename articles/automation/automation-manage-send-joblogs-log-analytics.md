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
ms.openlocfilehash: b3b9457e6c8ce501a7295859923838460e7ab6cc
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="forward-job-status-and-job-streams-from-automation-to-log-analytics-oms"></a>Transférer l’état d’un travail et des flux de travail d’Automation vers Log Analytics (OMS)
Automation peut envoyer l’état d’un travail de runbook et des flux de travail vers votre espace de travail Log Analytics dans Microsoft Operations Management Suite (OMS).  Les journaux de travail et les flux de travail sont visibles dans le portail Azure, ou avec PowerShell, pour des travaux individuels. Cela vous permet d’effectuer des enquêtes simples. Désormais avec Log Analytics, vous pouvez :

* Obtenir des informations sur vos travaux Automation
* Déclencher un e-mail ou une alerte basé(e) sur le statut de votre travail de runbook (par exemple, échec ou état suspendu)
* Écrire des requêtes avancées sur vos flux de travail
* Mettre en corrélation des travaux sur différents comptes Automation
* Visualiser l’historique de vos travaux dans le temps     

## <a name="prerequisites-and-deployment-considerations"></a>Conditions préalables et considérations relatives au déploiement
Pour commencer à envoyer vos journaux Automation à Log Analytics, vous devez disposer des éléments suivants :

1. Version de novembre 2016 ou une version ultérieure d’[Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) (v2.3.0).
2. Un espace de travail Log Analytics. Pour plus d’informations, consultez l’article [Prise en main de Log Analytics](../log-analytics/log-analytics-get-started.md). 
3. L’ID de ressource de votre compte Azure Automation

Pour rechercher l’ID de ressource de votre compte Azure Automation et l’espace de travail Log Analytics, exécutez la commande PowerShell suivante :

```powershell
# Find the ResourceId for the Automation Account
Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"

# Find the ResourceId for the Log Analytics workspace
Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
```

Si vous possédez plusieurs comptes Automation ou plusieurs espaces de travail, recherchez le *nom* dans la sortie des commandes précédentes pour configurer et copier la valeur de *ResourceId*.

Pour rechercher Le *Nom* de votre compte Automation, sélectionnez votre compte Automation à partir du panneau **Compte Automation** du portail Azure, puis sélectionnez **Tous les paramètres**.  Dans le panneau **Tous les paramètres**, sous **Paramètres du compte**, sélectionnez **Propriétés**.  Dans le panneau **Propriétés**, vous pouvez observer ces valeurs.<br> ![Propriétés du compte Automation](media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png).

## <a name="set-up-integration-with-log-analytics"></a>Configurer l’intégration avec Log Analytics
1. Sur votre ordinateur, démarrez **Windows PowerShell** à partir de l’écran **Démarrer**.  
2. Copiez et collez la commande PowerShell suivante et modifiez la valeur de `$workspaceId` et `$automationAccountId`.  Pour le paramètre `-Environment`, les valeurs valides sont *AzureCloud* ou *AzureUSGovernment* selon l’environnement cloud dans lequel vous travaillez.     

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }

# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $automationAccountId -WorkspaceId $workspaceId -Enabled $true

```

Après avoir exécuté ce script, les enregistrements s’affichent dans Log Analytics dans les 10 minutes suivant l’écriture des nouveaux JobLogs ou JobStreams.

Pour afficher les journaux, exécutez la requête suivante dans la recherche de journal de Log Analytics : `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="verify-configuration"></a>Vérifier la configuration
Pour confirmer que votre compte Automation envoie des journaux à votre espace de travail Log Analytics, vérifiez que les diagnostics sont correctement définis sur le compte Automation à l’aide de la commande PowerShell suivante :

```powershell
[cmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$True)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$Environment="AzureCloud"
    )

#Check to see which cloud environment to sign into.
Switch ($Environment)
   {
       "AzureCloud" {Login-AzureRmAccount}
       "AzureUSGovernment" {Login-AzureRmAccount -EnvironmentName AzureUSGovernment} 
   }
# if you have one Log Analytics workspace you can use the following command to get the resource id of the workspace
$workspaceId = (Get-AzureRmOperationalInsightsWorkspace).ResourceId

$automationAccountId = "/SUBSCRIPTIONS/ec11ca67-1234-421e-5678-c25/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.AUTOMATION/ACCOUNTS/DEMO" 

Get-AzureRmDiagnosticSetting -ResourceId $automationAccountId
```

Dans la sortie, assurez-vous que :
+ Sous *Journaux*, la valeur de *Activé* est *True*
+ La valeur de *WorkspaceId* est définie sur l’ID de ressource de votre espace de travail Log Analytics.


## <a name="log-analytics-records"></a>Enregistrements Log Analytics
Le diagnostic d’Azure Automation crée deux types d’enregistrements dans Log Analytics, balisés par **Type=AzureDiagnostics**.

### <a name="job-logs"></a>Journaux de travail
| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution du travail du runbook. |
| RunbookName_s |Nom du runbook. |
| Caller_s |Ce qui a initié l’opération.  Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés. |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| JobId_g |GUID représentant l’ID du travail du runbook. |
| ResultType |L’état du travail du runbook.  Les valeurs possibles sont les suivantes :<br>- Nouveau<br>Démarré<br>Arrêté<br>Interrompu<br>Échec<br>- Terminé |
| Catégorie | Classification du type de données.  Pour Automation, la valeur est JobLogs. |
| Nom d'opération | Spécifie le type d’opération exécutée dans Azure.  Pour Automation, la valeur est Job. |
| Ressource | Nom du compte Automation |
| SourceSystem | Manière dont Log Analytics a collecté les données. Toujours *Azure* pour les diagnostics Azure. |
| resultDescription |Décrit l’état résultant du travail du runbook.  Les valeurs possibles sont les suivantes :<br>- Job is started<br>- Job Failed<br>- Job Completed |
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
| Caller_s |Ce qui a initié l’opération.  Les valeurs possibles sont une adresse de messagerie ou un système pour les travaux planifiés. |
| StreamType_s |Type de flux de travail. Les valeurs possibles sont les suivantes :<br>progress<br>Sortie<br>Avertissement<br>error<br>DEBUG<br>- Verbose |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| JobId_g |GUID représentant l’ID du travail du runbook. |
| ResultType |L’état du travail du runbook.  Les valeurs possibles sont les suivantes :<br>- In Progress |
| Catégorie | Classification du type de données.  Pour Automation, la valeur est JobStreams. |
| Nom d'opération | Spécifie le type d’opération exécutée dans Azure.  Pour Automation, la valeur est Job. |
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
Maintenant que vous avez commencé à envoyer vos journaux de travaux Automation à Log Analytics, nous allons voir comment vous pouvez utiliser ces journaux dans Log Analytics.

Pour afficher les journaux, exécutez la requête suivante :`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION"`

### <a name="send-an-email-when-a-runbook-job-fails-or-suspends"></a>Envoyer un e-mail en cas d’échec ou de suspension d’un travail de runbook
Nos clients attendent souvent d’être en mesure d’envoyer un e-mail ou un message texte lorsqu’une erreur survient avec un travail de runbook.   

Pour créer une règle d’alerte, vous devez commencer par créer une recherche de journal pour les enregistrements de travaux de runbook qui doivent appeler l’alerte.  Cliquez sur le bouton **Alerte** pour créer et configurer la règle d’alerte.

1. Dans la page de présentation de Log Analytics, cliquez sur **Recherche de journal**.
2. Créer une requête de recherche de journal pour votre alerte en tapant la recherche suivante dans le champ de requête : `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended)` vous pouvez également regrouper par RunbookName à l’aide de : `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs (ResultType=Failed OR ResultType=Suspended) | measure Count() by RunbookName_s`   

   Si vous avez configuré des journaux dans votre espace de travail à partir de plusieurs abonnements ou comptes Automation, vous pouvez également regrouper vos alertes par abonnement ou par compte Automation.  Le nom du compte Automation peut être dérivé du champ Ressource dans la recherche de JobLogs.  
3. Cliquez sur **Alerte** en haut de la page pour ouvrir l’écran **Ajouter une règle d’alerte**. Pour plus d’informations sur les options de configuration de l’alerte, consultez l’article [Alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-all-jobs-that-have-completed-with-errors"></a>Rechercher tous les travaux qui ont rencontré des erreurs
En plus des alertes concernant les échecs, vous pouvez déterminer lorsqu’une tâche de runbook comporte une erreur sans fin d’exécution. Dans ces cas, PowerShell génère un flux d’erreur. Toutefois, les erreurs sans fin d’exécution ne provoquent la suspension ou l’échec de votre travail.    

1. Dans votre espace de travail Log Analytics, cliquez sur **Recherche de journal**.
2. Dans le champ de requête, tapez `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams StreamType_s=Error | measure count() by JobId_g` puis cliquez sur **Rechercher**.

### <a name="view-job-streams-for-a-job"></a>Afficher les flux de travail pour un travail
Lorsque vous déboguez un projet, vous pouvez également examiner les flux de travail.  La requête ci-dessous montre tous les flux pour un seul travail avec le GUID 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 :   

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`

### <a name="view-historical-job-status"></a>Afficher l’état de travail historique
Vous pouvez enfin souhaiter visualiser l’historique de vos travaux dans le temps.  Vous pouvez utiliser cette requête pour rechercher l’état de vos travaux au fil du temps.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  
<br> ![Graphique d’état de travail historique OMS](media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## <a name="summary"></a>Résumé
L’envoi de vos données de diffusion en continu et d’état des travaux Automation à Log Analytics permet d’obtenir plus de détails sur l’état de vos travaux Automation en :
+ Configurant des alertes pour vous avertir lorsqu’il y a un problème
+ Utilisant des vues et des requêtes de recherche personnalisées pour visualiser les résultats de votre runbook, l’état du travail de runbook et d’autres indicateurs ou mesures clés associées.  

Log Analytics offre une plus grande visibilité opérationnelle sur vos travaux Automation et peut permettre de traiter les incidents plus rapidement.  

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la façon de construire différentes requêtes de recherche et sur la manière de consulter les journaux de travaux Automation avec Log Analytics, consultez [Recherches de journal dans Log Analytics](../log-analytics/log-analytics-log-searches.md)
* Pour savoir comment créer et récupérer la sortie et les messages d’erreur à partir des runbooks, consultez [Sortie et messages de Runbook dans Azure Automation](automation-runbook-output-and-messages.md)
* Pour en savoir plus sur l’exécution d’un runbook, la manière de surveiller des tâches de runbook et d’autres détails techniques, consultez [Suivre une tâche de runbook](automation-runbook-execution.md)
* Pour en savoir plus sur OMS Log Analytics et sur les sources de collecte de données, consultez [Collecting Azure storage data in Log Analytics overview](../log-analytics/log-analytics-azure-storage.md)
