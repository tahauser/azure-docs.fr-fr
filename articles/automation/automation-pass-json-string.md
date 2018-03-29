---
title: Passer un objet JSON dans un runbook Azure Automation
description: Comment passer des paramètres dans un runbook en tant qu’objet JSON
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
keywords: powerShell, runbook, json, azure automation
ms.openlocfilehash: dd90c15ca70b08a010215a10f35abb3706825dea
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Passer un objet JSON dans un runbook Azure Automation

Il peut être utile stocker les données à passer dans un runbook dans un fichier JSON.
Par exemple, vous pouvez créer un fichier JSON qui contient tous les paramètres que vous souhaitez passer dans un runbook.
Pour ce faire, vous devez convertir le fichier JSON en une chaîne, puis convertir la chaîne en un objet PowerShell avant de passer son contenu dans le runbook.

Dans cet exemple, nous allons créer un script PowerShell qui appelle [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) pour démarrer un runbook PowerShell, en passant le contenu de l’objet JSON dans le runbook.
Le runbook PowerShell démarre une machine virtuelle Azure, en obtenant les paramètres pour la machine virtuelle à partir de l’objet JSON qui a été passé.

## <a name="prerequisites"></a>Prérequis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou <a href="/pricing/free-account/" target="_blank">[créer un compte gratuit](https://azure.microsoft.com/free/).
* [compte Automation](automation-sec-configure-azure-runas-account.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure.  Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Nous arrêtons et démarrons cette machine afin qu’elle ne soit pas une machine virtuelle de production.
* Azure Powershell installé sur un ordinateur local. Pour plus d'informations sur l’obtention d’Azure PowerShell, consultez la section [Installation et configuration d'Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0).

## <a name="create-the-json-file"></a>Créer le fichier JSON

Tapez le test suivant dans un fichier texte et enregistrez-le sous `test.json` quelque part sur votre ordinateur local.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Créer le runbook

Créer un nouveau runbook PowerShell nommé « Test-Json » dans Azure Automation.
Pour savoir comment créer un nouveau runbook PowerShell, consultez [Mon premier runbook PowerShell](automation-first-runbook-textual-powershell.md).

Pour accepter les données JSON, le runbook doit prendre un objet comme paramètre d’entrée.

Le runbook peut utiliser ensuite les propriétés définies dans le JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Enregistrez et publiez ce runbook dans votre compte Automation.

## <a name="call-the-runbook-from-powershell"></a>Appeler le runbook à partir de PowerShell

Vous pouvez maintenant appeler le runbook à partir de votre ordinateur local à l’aide d’Azure PowerShell.
Lancer les commandes PowerShell suivantes :

1. Connexion à Azure :
   ```powershell
   Login-AzureRmAccount
   ```
    Vous êtes invité à entrer vos informations d’identification Azure.
1. Obtenir le contenu du fichier JSON et les convertir en une chaîne :
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` est le chemin d’accès où vous avez enregistré le fichier JSON.
1. Convertir le contenu de la chaîne de `$json` dans un objet PowerShell :
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Créer une table de hachage pour les paramètres pour `Start-AzureRmAutomationRunbook` :
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Notez que vous définissez la valeur de `Parameters` sur l’objet PowerShell qui contient les valeurs à partir du fichier JSON. 
1. Démarrer le runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Le runbook utilise les valeurs du fichier JSON pour démarrer une machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la modification des runbooks PowerShell et de workflow PowerShell avec un éditeur de texte, consultez [Modifier des runbooks textuels dans Azure Automation](automation-edit-textual-runbook.md) 
* Pour savoir comment créer et importer des runbooks, consultez [Création ou importation d’un runbook dans Azure Automation](automation-creating-importing-runbook.md)


