---
title: Utiliser PowerShell pour créer et configurer un espace de travail Log Analytics | Microsoft Docs
description: Log Analytics utilise des données des serveurs dans votre infrastructure locale ou dans le cloud. Vous pouvez collecter des données de la machine à partir du stockage Azure lorsqu’elles sont générées par les diagnostics Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: 6a3f91323a017533d2d012f1e81760396c17a643
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="manage-log-analytics-using-powershell"></a>Gérer Log Analytics à l’aide de PowerShell
Vous pouvez utiliser les [applets de commande PowerShell Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) pour exécuter diverses fonctions dans Log Analytics à partir d’une ligne de commande ou via un script.  Voici quelques exemples des tâches que vous pouvez effectuer avec PowerShell :

* Créer un espace de travail
* Ajouter ou supprimer une solution
* Importer et exporter des recherches enregistrées
* Créer un groupe d’ordinateurs
* Activer la collecte de journaux IIS à partir d’ordinateurs sur lesquels l’agent Windows est installé
* Collecter les compteurs de performances d’ordinateurs Linux et Windows
* Collecter les événements de Syslog sur des ordinateurs Linux 
* Collecter les événements des journaux des événements Windows
* Collecter des journaux des événements personnalisés
* Ajouter l’agent Log Analytics à une machine virtuelle Azure
* Configurer Log Analytics pour indexer les données collectées à l’aide des diagnostics Azure

Cet article fournit deux exemples de code qui illustrent quelques-unes des fonctions que vous pouvez effectuer à partir de PowerShell.  Pour obtenir des informations sur d’autres fonctions, consultez [Informations de référence sur les applets de commande PowerShell Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) .

> [!NOTE]
> Avant, Log Analytics s’appelait Operational Insights, ce qui explique pourquoi ce nom est présent dans les applets de commande.
> 
> 

## <a name="prerequisites"></a>Prérequis

Ces exemples fonctionnent avec la version 2.3.0 ou ultérieure du module AzureRm.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Créer et configurer un espace de travail Log Analytics
L’exemple de script suivant illustre comment :

1. Créer un espace de travail
2. Répertorier les solutions disponibles
3. Ajouter des solutions à l’espace de travail
4. Importer des recherches enregistrées
5. Exporter des recherches enregistrées
6. Créer un groupe d’ordinateurs
7. Activer la collecte de journaux IIS à partir d’ordinateurs sur lesquels l’agent Windows est installé
8. Collecter les compteurs de performances de disque logique d’ordinateurs Linux (% d’Inodes utilisés, Mo libres, % d’espace utilisé, Transferts disque/s, Lectures disque/s, Écritures disque/s)
9. Collecter les événements Syslog d’ordinateurs Linux
10. Collecter les événements d’erreur et d’avertissement du journal des événements d’application d’ordinateurs Windows
11. Collecter le compteur de performances Mo de mémoire disponible d’ordinateurs Windows
12. Collecter un journal personnalisé 

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configuration de Log Analytics pour indexer les diagnostics Azure
Pour une analyse sans agent des ressources Azure, celles-ci doivent avoir les diagnostics Azure activés et configurés pour écrire dans un espace de travail Log Analytics. Cette approche a pour effet d’envoyer des données directement à Log Analytics, et ne nécessite pas d’écriture de données dans un compte de stockage. Les ressources prises en charge sont les suivantes :

| Type de ressource | Journaux | Mesures |
| --- | --- | --- |
| Passerelles d’application    | OUI | OUI |
| Comptes Automation     | OUI | |
| Comptes Batch          | OUI | OUI |
| Data Lake analytics     | OUI | | 
| Data Lake Store         | OUI | |
| Pool élastique SQL        |     | OUI |
| Espace de noms Event Hub     |     | OUI |
| IoT Hubs                |     | OUI |
| Key Vault               | OUI | |
| Équilibreurs de charge          | OUI | |
| Logic Apps              | OUI | OUI |
| Network Security Group | OUI | |
| Cache Redis             |     | OUI |
| Services de recherche         | OUI | OUI |
| Espace de noms Service Bus   |     | OUI |
| SQL (v12)               |     | OUI |
| Sites web               |     | OUI |
| Batteries de serveurs web        |     | OUI |

Pour plus d’informations sur les métriques disponibles, voir [Mesures prises en charge avec Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Pour plus d’informations sur les journaux disponibles, voir [Schéma et services pris en charge pour les journaux de diagnostic](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Vous pouvez également utiliser l’applet de commande précédente pour collecter des journaux à partir de ressources qui se trouvent dans différents abonnements. L’applet de commande est capable d’opérer sur divers abonnements dans la mesure où vous fournissez les ID de la ressource créant les journaux et de l’espace de travail auquel les journaux sont envoyés.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Configuration de Log Analytics pour indexer les diagnostics Azure à partir du stockage
Pour collecter des données de journal à partir d’une instance en cours d’exécution d’un service cloud classique ou d’un cluster Service Fabric, vous devez commencer par écrire les données dans le Stockage Azure. Vous pouvez ensuite configurer Log Analytics pour collecter les journaux du compte de stockage. Les ressources prises en charge sont les suivantes :

* Services cloud classiques (rôles de travail et web)
* Clusters Service Fabric

L’exemple suivant montre comment :

1. Répertorier les emplacements et les comptes de stockage existants à partir desquels Log Analytics indexe les données
2. Créer une configuration pour lire à partir d’un compte de stockage
3. Mettre à jour la configuration créée pour indexer les données à partir d’emplacements supplémentaires
4. Supprimer la configuration créée

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

Vous pouvez également utiliser le script précédent pour collecter les journaux de comptes de stockage dans différents abonnements. Le script est en mesure d’opérer sur divers abonnements dans la mesure où vous fournissez l’ID de ressource du compte de stockage et une clé d’accès correspondante. Lorsque vous modifiez la clé d’accès, vous devez mettre à jour Storage Insight pour avoir la nouvelle clé.


## <a name="next-steps"></a>Étapes suivantes
* [Passez en revue les applets de commande PowerShell de Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) pour obtenir plus d’informations sur l’utilisation de PowerShell pour configurer Log Analytics.

