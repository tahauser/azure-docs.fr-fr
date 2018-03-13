---
title: "Gestion d’Azure Data Lake Analytics à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Apprenez à gérer des comptes Data Lake Analytics, des sources de données, des travaux et des éléments de catalogue. "
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: dd81e9d6c91387b3873593b84e952ca4f2546c57
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gestion d'Azure Data Lake Analytics à l'aide d'Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Apprenez à gérer des comptes Azure Data Lake Analytics, des sources de données, des travaux et des éléments de catalogue à l'aide d'Azure PowerShell. 

## <a name="prerequisites"></a>Prérequis

Pour créer un compte Data Lake Analytics, vous devez connaître les éléments suivants :

* **ID d’abonnement** : ID d’abonnement Azure sous lequel réside votre compte Data Lake Analytics.
* **Groupe de ressources** : nom du groupe de ressources Azure qui contient votre compte Data Lake Analytics.
* **Nom du compte Data Lake Analytics** : le nom du compte ne doit contenir que des lettres minuscules et des chiffres.
* **Compte Data Lake Store par défaut** : chaque compte Data Lake Analytics possède un compte Data Lake Store par défaut. Ces comptes doivent résider au même emplacement.
* **Emplacement** : emplacement de votre compte Data Lake Analytics, comme « États-Unis de l’Est 2 » ou autres emplacements pris en charge. Les emplacements pris en charge sont indiqués sur notre [page de tarification](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

Dans ce didacticiel, les extraits de code PowerShell utilisent ces variables pour stocker ces informations.

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Se connecter

Connectez-vous à l’aide d’un ID d’abonnement.

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

Connectez-vous à l’aide d’un nom d’abonnement.

```
Login-AzureRmAccount -SubscriptionName $subname 
```

La cmdlet `Login-AzureRmAccount` demande toujours les informations d’identification. Vous pouvez éviter cela à l’aide des cmdlets suivantes :

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="managing-accounts"></a>Gérer des comptes

### <a name="create-a-data-lake-analytics-account"></a>Créer un compte Data Lake Analytics

Si vous ne disposez pas d’un [groupe de ressources](../azure-resource-manager/resource-group-overview.md#resource-groups), créez-en un. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Chaque compte Data Lake Analytics nécessite un compte Data Lake Store par défaut pour stocker les journaux. Vous pouvez réutiliser un compte ou en créer un. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Lorsqu’un groupe de ressources et un compte Data Lake Store sont disponibles, créez un compte Data Lake Analytics.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>Obtenir des informations sur un compte

Obtenez les détails relatifs à un compte.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Vérifiez l’existence d’un compte Data Lake Analytics spécifique. La cmdlet retourne `$true` ou `$false`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Vérifiez l’existence d’un compte Data Lake Store spécifique. La cmdlet retourne `$true` ou `$false`.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="listing-accounts"></a>Lister les comptes

Listez les comptes Data Lake Analytics dans l’abonnement sélectionné.

```powershell
Get-AdlAnalyticsAccount
```

Listez les comptes Data Lake Analytics dans un groupe de ressources spécifique.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>Gérer les règles de pare-feu

Listez les règles de pare-feu.

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

Ajoutez une règle de pare-feu.

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Modifiez une règle de pare-feu.

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Supprimez une règle de pare-feu.

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

Autorisez des adresses IP Azure.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Gérer les sources de données
Azure Data Lake Analytics prend actuellement en charge les sources de données suivantes :

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Stockage Azure](../storage/common/storage-introduction.md)

Quand vous créez un compte Analytics, vous devez désigner un compte Data Lake Store comme source de données par défaut. Le compte Data Lake Store par défaut est utilisé pour stocker les métadonnées du travail et les journaux d'audit du travail. Après la création d'un compte Data Lake Analytics, vous pouvez ajouter des comptes Data Lake Store et/ou des comptes de stockage supplémentaires. 

### <a name="find-the-default-data-lake-store-account"></a>Rechercher le compte Data Lake Store par défaut

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Vous trouverez le compte Data Lake Store par défaut en filtrant la liste des sources de données par le `IsDefault` propriété :

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Ajouter une source de données

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Lister les sources de données

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Envoyer des travaux U-SQL

### <a name="submit-a-string-as-a-u-sql-script"></a>Envoyer une chaîne en tant que script U-SQL

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```

### <a name="submit-a-file-as-a-u-sql-script"></a>Envoyer un fichier en tant que script U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Lister des travaux dans un compte

### <a name="list-all-the-jobs-in-the-account"></a>Affichez la liste de tous les travaux dans le compte. 

La sortie comprend les travaux en cours et ceux qui ont été terminés récemment.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>Répertorier les N premiers travaux

Par défaut, la liste des travaux est triée en fonction de l’heure d’envoi. Par conséquent, les travaux envoyés le plus récemment s’affichent en premier. Par défaut, le compte ADLA garde en mémoire les travaux pendant 180 jours, mais l’applet de commande AdlJob-Ge ne retourne par défaut que les 500 premiers travaux. Utilisez le paramètre -Top pour lister un nombre spécifique de travaux.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-based-on-the-value-of-job-property"></a>Lister les travaux en fonction de la valeur de la propriété du travail

Utilisation du `-State` paramètre. Vous pouvez combiner chacune de ces valeurs :

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

Utilisez le `-Result` paramètre pour détecter si les travaux terminés se sont achevés correctement. Il possède ces valeurs :

* Annulé
* Échec
* Aucun
* Succeeded

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

Le `-Submitter` paramètre vous permet d’identifier qui a envoyé une tâche.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

Le paramètre `-SubmittedAfter` est utile lorsque vous filtrez dans un intervalle de temps.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Analyse de l’historique des travaux

Utiliser Azure PowerShell pour analyser l’historique des travaux qui ont été exécutés dans Data Lake analytics est une technique puissante. Vous pouvez l’utiliser pour obtenir des informations sur l’utilisation et le coût. Pour en savoir plus, consultez [l’exemple de dépôt de l’analyse de l’historique des travaux](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis).  

## <a name="get-information-about-pipelines-and-recurrences"></a>Obtenir des informations sur les pipelines et les périodicités

Utilisez l’ `Get-AdlJobPipeline` applet de commande pour afficher les tâches déjà soumises sur les informations de pipeline.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Utilisez l’ `Get-AdlJobRecurrence` applet de commande pour afficher les informations sur la périodicité des tâches déjà soumises.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>Obtenir des informations sur un travail

### <a name="get-job-status"></a>Obtenir l’état de la tâche

Affichez l’état d’un travail.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Analyser les sorties du travail

Dès qu’un travail est terminé, vérifiez si le fichier de sortie existe en affichant les fichiers dans un dossier.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Gérer les tâches en cours d’exécution

### <a name="cancel-a-job"></a>Annulation d’une tâche

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Attendre la fin d’une tâche

Au lieu de répéter `Get-AdlAnalyticsJob` jusqu’à ce qu’un travail se termine, vous pouvez utiliser la cmdlet `Wait-AdlJob` pour attendre la fin du travail.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>Gérer les stratégies de calcul

### <a name="list-existing-compute-policies"></a>Lister les stratégies de calcul existantes

L’ `Get-AdlAnalyticsComputePolicy` applet de commande récupère des informations sur les stratégies de calcul pour un compte Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Créer une stratégie de calcul

L’ `New-AdlAnalyticsComputePolicy` applet de commande crée une nouvelle stratégie de calcul pour un compte Data Lake Analytics. Cet exemple définit les AU maximales disponibles pour l’utilisateur spécifié à 50 et la priorité minimale de la tâche à 250.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>Vérifiez l’existence d’un fichier.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Chargement et téléchargement

Chargez un fichier.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Chargez un dossier entier de manière récursive.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Téléchargez un fichier.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Téléchargez un dossier entier de manière récursive.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Si le processus de chargement ou de téléchargement est interrompu, vous pouvez tenter de reprendre le processus en exécutant à nouveau la cmdlet avec l’indicateur ``-Resume``.

## <a name="manage-catalog-items"></a>Gestion des éléments du catalogue

Le catalogue U-SQL est utilisé pour structurer les données et le code afin que les scripts U-SQL puissent les partager. Le catalogue permet les meilleures performances possibles avec les données comprises dans Azure Data Lake. Pour plus d'informations, consultez [Utilisation du catalogue U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Lister les éléments dans le catalogue U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

Listez tous les assemblys dans toutes les bases de données d’un compte ADLA.

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Obtenir des détails sur un élément de catalogue

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>Créer des informations d’identification dans un catalogue

Dans une base de données U-SQL, créez un objet informations d’identification pour une base de données hébergée dans Azure. Actuellement, les informations d’identification U-SQL sont le seul type d’élément de catalogue que vous pouvez créer via PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

### <a name="get-basic-information-about-an-adla-account"></a>Obtenir des informations de base sur un compte ADLA

À l’aide du nom d’un compte, le code suivant recherche des informations de base sur ce compte

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>Utilisation d’Azure

### <a name="get-details-of-azurerm-errors"></a>Obtenir des détails sur les erreurs AzureRm

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>Vérifier que vous exécutez en tant qu’administrateur

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Trouver un TenantID

À partir du nom d’un abonnement :

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

À partir de l’ID d’un abonnement :

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

À partir d’une adresse de domaine, comme « contoso.com »


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Lister tous vos abonnements et leur ID client

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Créer un compte Data Lake Analytics à l’aide d’un modèle

Vous pouvez également utiliser un modèle de groupe de ressources Azure à l’aide du script PowerShell suivant :

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

Pour plus d’informations, consultez [Déploiement d’une application avec un modèle Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) et [Création de modèles Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

**Exemple de modèle**

Enregistrez le texte suivant en tant que fichier `.json`, puis utilisez le script PowerShell précédent pour utiliser le modèle. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>étapes suivantes
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* Prise en main de Data Lake Analytics à l’aide du [portail Azure](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)
* Gérer Azure Data Lake Analytics à l’aide du [portail Azure](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
