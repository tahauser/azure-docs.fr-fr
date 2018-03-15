---
title: "Déployer un modèle Azure Resource Manager dans un runbook Azure Automation | Microsoft Docs"
description: "Comment déployer un modèle Azure Resource Manager stocké dans Stockage Azure à partir d’un runbook"
services: automation
documentationcenter: dev-center-name
author: georgewallace
manager: carmonm
keywords: powerShell, runbook, json, azure automation
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 07/09/2017
ms.author: gwallace
ms.openlocfilehash: 63c8f1b1190e19e1f1d2a7871bffee44ef5c7877
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Déployer un modèle Azure Resource Manager dans un runbook PowerShell Azure Automation

Vous pouvez écrire un [runbook PowerShell Azure Automation](automation-first-runbook-textual-powershell.md) qui déploie une ressource Azure en utilisant un [modèle Azure Resource Manager](../azure-resource-manager/resource-manager-create-first-template.md).

Ainsi, vous pouvez automatiser le déploiement de ressources Azure. Vous pouvez gérer vos modèles Resource Manager dans un emplacement central et sécurisé, tel que Stockage Azure.

Dans cette rubrique, nous créons un runbook PowerShell qui utilise un modèle Resource Manager stocké dans [Stockage Azure](../storage/common/storage-introduction.md) pour déployer un nouveau compte de stockage Azure.

## <a name="prerequisites"></a>Prérequis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[créer un compte gratuit](https://azure.microsoft.com/free/).
* [compte Automation](automation-sec-configure-azure-runas-account.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure.  Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* [Compte de stockage Azure](../storage/common/storage-create-storage-account.md) dans lequel stocker le modèle Resource Manager
* Azure Powershell installé sur un ordinateur local. Pour plus d'informations sur l’obtention d’Azure PowerShell, consultez la section [Installation et configuration d'Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0).

## <a name="create-the-resource-manager-template"></a>Créer le modèle Resource Manager

Pour cet exemple, nous utilisons un modèle Resource Manager qui déploie un nouveau compte de stockage Azure.

Dans un éditeur de texte, copiez le texte suivant :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Enregistrez le fichier localement sous le nom `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Enregistrer le modèle Resource Manager dans Stockage Azure

Maintenant, nous allons utiliser PowerShell pour créer un partage de fichiers Stockage Azure et charger le fichier `TemplateTest.json`.
Pour obtenir des instructions sur la création d’un partage de fichier et sur le chargement d’un fichier sur le portail Azure, consultez [Bien démarrer avec le stockage de fichiers Azure sur Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Lancez PowerShell sur votre ordinateur local et exécutez les commandes suivantes pour créer un partage de fichiers et y charger le modèle Resource Manager.

```powershell
# Login to Azure
Login-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Créer le script de runbook PowerShell

Nous allons maintenant créer un script PowerShell qui récupère le fichier `TemplateTest.json` de Stockage Azure et déploie le modèle pour créer un compte de stockage Azure.

Dans un éditeur de texte, collez le texte suivant :

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Enregistrez le fichier localement sous le nom `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importer et publier le runbook dans votre compte Azure Automation

Nous allons maintenant utiliser PowerShell pour importer le runbook dans votre compte Azure Automation, puis publier le runbook.
Pour plus d’informations sur la façon d’importer et de publier un runbook dans le portail Azure, consultez [Création ou importation d’un runbook dans Azure Automation](automation-creating-importing-runbook.md).

Pour importer `DeployTemplate.ps1` dans votre compte Automation en tant que runbook PowerShell, exécutez les commandes PowerShell suivantes :

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Démarrer le runbook

Maintenant, nous démarrons le runbook en appelant l’applet de commande [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0).

Pour plus d’informations sur la façon de démarrer un runbook dans le portail Azure, consultez [Démarrage d’un Runbook dans Azure Automation](automation-starting-a-runbook.md).

Dans la console PowerShell, exécutez les commandes suivantes :

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Le runbook s’exécute, et vous pouvez vérifier son état en exécutant `$job.Status`.

Le runbook récupère le modèle Resource Manager et l’utilise pour déployer un nouveau compte de stockage Azure.
Vous pouvez voir que le nouveau compte de stockage a été créé en exécutant la commande suivante :
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Résumé

Et voilà ! Maintenant, vous pouvez utiliser Azure Automation, Stockage Azure et des modèles Resource Manager pour déployer toutes vos ressources Azure.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les modèles Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Pour démarrer avec Stockage Azure, consultez [Introduction à Azure Storage](../storage/common/storage-introduction.md).
* Pour rechercher d’autres runbooks Azure Automation utiles, consultez [Galeries de runbooks et de modules pour Azure Automation](automation-runbook-gallery.md).
* Pour rechercher d’autres modèles Resource Manager utiles, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/).

