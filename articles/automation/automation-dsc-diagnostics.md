---
title: Transférer des données de rapport Azure Automation DSC à Log Analytics
description: Cet article montre comment envoyer des données de rapport DSC (Desired State Configuration) à Log Analytics, afin de fournir des informations supplémentaires et de simplifier la gestion.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ac0da2b73341f0c6ed4f1e99e077996525890ab7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="forward-azure-automation-dsc-reporting-data-to-log-analytics"></a>Transférer des données de rapport Azure Automation DSC à Log Analytics

Automation peut envoyer des données sur l’état du nœud DSC à votre espace de travail Log Analytics.  
L’état de conformité est visible dans le portail Azure, ou avec PowerShell, pour les nœuds et les ressources DSC individuelles dans les configurations de nœud. Avec Log Analytics, vous pouvez :

* Obtenir des informations de conformité pour les nœuds gérés et les ressources individuelles
* Déclencher un e-mail ou une alerte en fonction de l’état de conformité
* Écrire des requêtes avancées dans vos nœuds gérés
* Mettre en corrélation l’état de conformité dans les comptes Automation
* Visualiser votre historique de conformité associé aux nœuds au fil du temps

## <a name="prerequisites"></a>Prérequis


Pour commencer à envoyer vos rapports Automation DSC à Log Analytics, vous devez disposer des éléments suivants :

* Version de novembre 2016 ou une version ultérieure d’[Azure PowerShell](/powershell/azure/overview) (v2.3.0).
* Un compte Azure Automation. Pour plus d’informations, consultez la page [Prise en main d’Azure Automation](automation-offering-get-started.md).
* Un espace de travail Log Analytics avec une offre de service **Automation & Control**. Pour plus d’informations, consultez l’article [Prise en main de Log Analytics](../log-analytics/log-analytics-get-started.md).
* Au moins un nœud Azure Automation DSC. Pour plus d’informations, consultez [Gestion de machines avec Azure Automation DSC](automation-dsc-onboarding.md).

## <a name="set-up-integration-with-log-analytics"></a>Configurer l’intégration avec Log Analytics

Pour commencer l’importation des données à partir d’Azure Automation DSC dans Log Analytics, procédez comme suit :

1. Dans PowerShell, connectez-vous à votre compte Azure. Consultez [Log in with Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.0.0) (Se connecter avec Azure PowerShell).
1. Obtenez le _ResourceId_ de votre compte Automation en exécutant la commande PowerShell suivante : (si vous avez plusieurs comptes Automation, choisissez le _ResourceID_ du compte que vous souhaitez configurer).

  ```powershell
  # Find the ResourceId for the Automation Account
  Find-AzureRmResource -ResourceType "Microsoft.Automation/automationAccounts"
  ```
1. Obtenez le _ResourceId_ de votre espace de travail Log Analytics en exécutant la commande PowerShell suivante : (si vous avez plusieurs espaces de travail, choisissez le _ResourceID_ de l’espace de travail que vous souhaitez configurer).

  ```powershell
  # Find the ResourceId for the Log Analytics workspace
  Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces"
  ```
1. Exécutez la commande PowerShell suivante, en remplaçant `<AutomationResourceId>` et `<WorkspaceResourceId>` par les valeurs _ResourceId_ de chacune des étapes précédentes :

  ```powershell
  Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $true -Categories "DscNodeStatus"
  ```

Si vous souhaitez arrêter l’importation de données à partir d’Azure Automation DSC dans Log Analytics, exécutez la commande PowerShell suivante.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId <AutomationResourceId> -WorkspaceId <WorkspaceResourceId> -Enabled $false -Categories "DscNodeStatus"
```

## <a name="view-the-dsc-logs"></a>Afficher les journaux DSC

Après avoir configuré l’intégration à Log Analytics pour vos données Automation DSC, un bouton **Recherche dans les journaux** s’affiche dans le panneau **Nœuds DSC** de votre compte Automation. Cliquez sur le bouton **Recherche dans les journaux** pour afficher les journaux des données de nœud DSC.

![Bouton Recherche dans les journaux](media/automation-dsc-diagnostics/log-search-button.png)

Le panneau **Recherche dans les journaux** s’ouvre et vous voyez une opération **DscNodeStatusData** pour chaque nœud DSC et une opération **DscResourceStatusData** pour chaque [ressource DSC](https://msdn.microsoft.com/powershell/dsc/resources) appelée dans la configuration Nœud appliquée à ce nœud.

L’opération **DscResourceStatusData** contient des informations d’erreur pour les ressources DSC ayant échoué.

Cliquez sur chaque opération dans la liste pour afficher les données correspondant à cette opération.

Vous pouvez également afficher les journaux en effectuant une recherche dans Log Analytics. Consultez [Recherche de données à l’aide de recherches de journal](../log-analytics/log-analytics-log-searches.md).
Tapez la requête suivante pour rechercher vos journaux DSC : `Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus"`

Vous pouvez également affiner la requête par nom d’opération. Par exemple : « Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category = "DscNodeStatus" OperationName = "DscNodeStatusData" »

### <a name="send-an-email-when-a-dsc-compliance-check-fails"></a>Envoyer un e-mail si une vérification de la conformité DSC échoue

L’une des demandes majeures de nos clients est de pouvoir envoyer un e-mail ou un message texte lorsqu’une erreur survient avec une configuration DSC.   

Pour créer une règle d’alerte, vous commencez par créer une recherche dans les journaux pour les enregistrements de rapport DSC qui doivent appeler l’alerte.  Cliquez sur le bouton **Alerte** pour créer et configurer la règle d’alerte.

1. Dans la page de présentation de Log Analytics, cliquez sur **Recherche de journal**.
1. Créez une requête de recherche dans les journaux pour votre alerte en tapant la recherche suivante dans le champ de requête : `Type=AzureDiagnostics Category=DscNodeStatus NodeName_s=DSCTEST1 OperationName=DscNodeStatusData ResultType=Failed`.

  Si vous avez configuré des journaux dans votre espace de travail à partir de plusieurs abonnements ou comptes Automation, vous pouvez également regrouper vos alertes par abonnement ou par compte Automation.  
  Le nom du compte Automation peut être dérivé du champ Ressource dans la recherche de DscNodeStatusData.  
1. Cliquez sur **Alerte** en haut de la page pour ouvrir l’écran **Ajouter une règle d’alerte**. Pour plus d’informations sur les options de configuration de l’alerte, consultez [Comprendre les alertes dans Log Analytics](../log-analytics/log-analytics-alerts.md#alert-rules).

### <a name="find-failed-dsc-resources-across-all-nodes"></a>Rechercher les ressources DSC ayant échoué dans tous les nœuds

L’un des avantages de l’utilisation de Log Analytics est que vous pouvez rechercher les vérifications ayant échoué dans les nœuds.
Pour rechercher toutes les instances de ressources DSC ayant échoué.

1. Dans la page de présentation de Log Analytics, cliquez sur **Recherche de journal**.
1. Créez une requête de recherche dans les journaux pour votre alerte en tapant la recherche suivante dans le champ de requête : `Type=AzureDiagnostics Category=DscNodeStatus OperationName=DscResourceStatusData ResultType=Failed`.

### <a name="view-historical-dsc-node-status"></a>Afficher l’état de nœud DSC historique

Enfin, vous pouvez souhaiter visualiser l’historique de l’état de votre nœud DSC au fil du temps.  
Vous pouvez utiliser cette requête pour rechercher l’état de votre nœud DSC au fil du temps.

`Type=AzureDiagnostics ResourceProvider="MICROSOFT.AUTOMATION" Category=DscNodeStatus NOT(ResultType="started") | measure Count() by ResultType interval 1hour`  

Cela affichera un graphique de l’état du nœud au fil du temps.

## <a name="log-analytics-records"></a>Enregistrements Log Analytics

La fonction de diagnostic d’Azure Automation crée deux catégories d’enregistrements dans Log Analytics.

### <a name="dscnodestatusdata"></a>DscNodeStatusData

| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution de la vérification de conformité. |
| Nom d'opération |DscNodeStatusData |
| ResultType |Indique si le nœud est conforme. |
| NodeName_s |Le nom du nœud géré. |
| NodeComplianceStatus_s |Indique si le nœud est conforme. |
| DscReportStatus |Indique si la vérification de conformité a été correctement exécutée. |
| ConfigurationMode | La façon dont la configuration est appliquée au nœud. Les valeurs possibles sont __« ApplyOnly »__,__« ApplyandMonitor »__ et __« ApplyandAutoCorrect »__. <ul><li>__ApplyOnly__ : DSC applique la configuration et ne fait rien de plus, sauf si une nouvelle configuration est envoyée au nœud cible ou lorsqu’une nouvelle configuration est extraite d’un serveur. Après l’application initiale d’une nouvelle configuration, DSC ne vérifie pas l’écart par rapport à un état configuré précédemment. DSC tente d’appliquer la configuration jusqu’à ce que l’opération réussisse avant que la valeur __ApplyOnly__ prenne effet. </li><li> __ApplyAndMonitor__ : il s’agit de la valeur par défaut. Le gestionnaire de configuration locale applique toutes les nouvelles configurations. Après l’application initiale d’une nouvelle configuration, si le nœud cible s’écarte de l’état désiré, DSC signale l’écart dans les journaux. DSC tente d’appliquer la configuration jusqu’à ce que l’opération réussisse avant que la valeur __ApplyAndMonitor__ prenne effet.</li><li>__ApplyAndAutoCorrect__ : DSC applique toutes les nouvelles configurations. Après l’application initiale d’une nouvelle configuration, si le nœud cible s’écarte de l’état désiré, DSC signale l’écart dans les journaux puis applique à nouveau la configuration actuelle.</li></ul> |
| HostName_s | Le nom du nœud géré. |
| IPAddress | L’adresse IPv4 du nœud géré. |
| Catégorie | DscNodeStatus |
| Ressource | Le nom du compte Azure Automation. |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| NodeId_g |GUID qui identifie le nœud géré. |
| DscReportId_g |GUID qui identifie le rapport. |
| LastSeenTime_t |Date et heure auxquelles le rapport a été consulté pour la dernière fois. |
| ReportStartTime_t |Date et heure auxquelles le rapport a été commencé. |
| ReportEndTime_t |Date et heure auxquelles le rapport a été terminé. |
| NumberOfResources_d |Le nombre de ressources DSC appelées dans la configuration appliquée au nœud. |
| SourceSystem | Manière dont Log Analytics a collecté les données. Toujours *Azure* pour les diagnostics Azure. |
| ResourceId |Spécifie le compte Azure Automation. |
| resultDescription | La description de cette opération. |
| SubscriptionId | ID d’abonnement Azure (GUID) du compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID représentant l’ID de corrélation du rapport de conformité. |

### <a name="dscresourcestatusdata"></a>DscResourceStatusData

| Propriété | Description |
| --- | --- |
| TimeGenerated |Date et heure d’exécution de la vérification de conformité. |
| Nom d'opération |DscResourceStatusData|
| ResultType |Indique si la ressource est conforme. |
| NodeName_s |Le nom du nœud géré. |
| Catégorie | DscNodeStatus |
| Ressource | Le nom du compte Azure Automation. |
| Tenant_g | GUID identifiant le locataire pour l’appelant. |
| NodeId_g |GUID qui identifie le nœud géré. |
| DscReportId_g |GUID qui identifie le rapport. |
| DscResourceId_s |Le nom de l’instance de ressource DSC. |
| DscResourceName_s |Le nom de la ressource DSC. |
| DscResourceStatus_s |Indique si la ressource DSC est conforme. |
| DscModuleName_s |Le nom du module PowerShell qui contient la ressource DSC. |
| DscModuleVersion_s |La version du module PowerShell qui contient la ressource DSC. |
| DscConfigurationName_s |Le nom de la configuration appliquée au nœud. |
| ErrorCode_s | Le code d’erreur en cas d’échec de la ressource. |
| ErrorMessage_s |Le message d’erreur en cas d’échec de la ressource. |
| DscResourceDuration_d |La durée, en secondes, d’exécution de la ressource DSC. |
| SourceSystem | Manière dont Log Analytics a collecté les données. Toujours *Azure* pour les diagnostics Azure. |
| ResourceId |Spécifie le compte Azure Automation. |
| resultDescription | La description de cette opération. |
| SubscriptionId | ID d’abonnement Azure (GUID) du compte Automation. |
| ResourceGroup | Nom du groupe de ressources du compte Automation. |
| ResourceProvider | MICROSOFT.AUTOMATION |
| ResourceType | AUTOMATIONACCOUNTS |
| CorrelationId |GUID représentant l’ID de corrélation du rapport de conformité. |

## <a name="summary"></a>Résumé

L’envoi de vos données Automation DSC à Log Analytics vous permet d’obtenir plus d’informations sur l’état de vos nœuds Automation DSC en :

* Configurant des alertes pour vous avertir lorsqu’il y a un problème
* Utilisant des vues et des requêtes de recherche personnalisées pour visualiser les résultats de votre runbook, l’état du travail de runbook et d’autres indicateurs ou mesures clés associées.  

Log Analytics offre une plus grande visibilité opérationnelle sur vos données Automation DSC et peut vous aider à traiter les incidents plus rapidement.  

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la façon de construire différentes requêtes de recherche et sur la manière de consulter les journaux Automation DSC avec Log Analytics, consultez [Recherche de données à l’aide de recherches de journal](../log-analytics/log-analytics-log-searches.md).
* Pour en savoir plus sur l’utilisation d’Azure Automation DSC, consultez [Prise en main d’Azure Automation DSC](automation-dsc-getting-started.md).
* Pour en savoir plus sur Log Analytics et sur les sources de collecte de données, consultez [Vue d’ensemble de la collecte des données de stockage Azure dans Log Analytics](../log-analytics/log-analytics-azure-storage.md)

