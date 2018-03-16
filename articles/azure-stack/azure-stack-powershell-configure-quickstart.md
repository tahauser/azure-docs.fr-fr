---
title: "Installer et configurer PowerShell pour Azure Stack - Démarrage rapide | Microsoft Docs"
description: "Découvrez comment installer et configurer PowerShell pour Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 6996DFC1-5E05-423A-968F-A9427C24317C
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.openlocfilehash: cba6f8295e5d4b75192e566d4931cbd617e7dc8d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="get-up-and-running-with-powershell-in-azure-stack"></a>Devenez rapidement opérationnel avec PowerShell dans Azure Stack.

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Ce démarrage rapide vous aide à installer et configurer un environnement Azure Stack avec PowerShell. La portée du script fourni dans cet article est définie seulement pour **l’opérateur Azure Stack**.

Cet article est une version condensée des étapes décrites dans les articles [Installer PowerShell]( azure-stack-powershell-install.md), [Télécharger les outils]( azure-stack-powershell-download.md) et [Configurer l’environnement PowerShell de l’opérateur Azure Stack]( azure-stack-powershell-configure-admin.md). Les scripts fournis dans cette rubrique permettent de configurer PowerShell pour les environnements Azure Stack qui sont déployés avec Azure Active Directory ou Active Directory Federation Services (AD FS).  


## <a name="set-up-powershell-for-azure-active-directory-based-deployments"></a>Configurer PowerShell pour les déploiements basés sur Azure Active Directory

Connectez-vous à votre Kit de développement Azure Stack, ou à un client externe Windows si vous êtes connecté par le biais d’un VPN. Ouvrez une session PowerShell ISE avec élévation de privilèges et exécutez le script suivant. Veillez à mettre à jour les variables **TenantName**, **ArmEndpoint** et **GraphAudience** en fonction des besoins de la configuration de votre environnement :

> [!IMPORTANT]
> La version du module PowerShell AzureRM 1.2.11 est fournie avec une liste des modifications importantes. Pour mettre à niveau à partir de la version 1.2.10, consultez le [guide de migration](https://aka.ms/azspowershellmigration).

```powershell
# Specify Azure Active Directory tenant name.
$TenantName = "<mydirectory>.onmicrosoft.com"

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions, and then run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"


# Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName $TenantName `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
```

## <a name="set-up-powershell-for-ad-fs-based-deployments"></a>Configurer PowerShell pour les déploiements basés sur AD FS

Vous pouvez utiliser le script suivant si vous utilisez Azure Stack avec une connexion à Internet. Toutefois, si vous utilisez Azure Stack sans connectivité Internet, utilisez le [mode d’installation hors ligne de PowerShell](azure-stack-powershell-install.md#install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity) et les cmdlets de configuration de PowerShell resteront identiques, comme indiqué dans ce script. Connectez-vous à votre Kit de développement Azure Stack, ou à un client externe Windows si vous êtes connecté par le biais d’un VPN. Ouvrez une session PowerShell ISE avec élévation de privilèges et exécutez le script suivant. Veillez à mettre à jour les variables **ArmEndpoint** et **GraphAudience** en fonction des besoins de la configuration de votre environnement :

```powershell

# Set the module repository and the execution policy.
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted

Set-ExecutionPolicy RemoteSigned `
  -force

# Uninstall any existing Azure PowerShell modules. To uninstall, close all the active PowerShell sessions and run the following command:
Get-Module -ListAvailable -Name Azure* | `
  Uninstall-Module

# Install PowerShell for Azure Stack.
Install-Module `
  -Name AzureRm.BootStrapper `
  -Force

Use-AzureRmProfile `
  -Profile 2017-03-09-profile `
  -Force

Install-Module `
  -Name AzureStack `
  -RequiredVersion 1.2.11 `
  -Force 

# Download Azure Stack tools from GitHub and import the connect module.
cd \
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

expand-archive master.zip `
  -DestinationPath . `
  -Force

cd AzureStack-Tools-master

Import-Module .\Connect\AzureStack.Connect.psm1

# For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
$ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = "<Keyvault DNS suffix for your environment>"

# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint

# Get the Active Directory tenantId that is used to deploy Azure Stack     
$TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackAdmin"

# Sign in to your environment
Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID
```

## <a name="test-the-connectivity"></a>Tester la connectivité

Maintenant que vous avez configuré PowerShell, vous pouvez tester la configuration en créant un groupe de ressources :

```powershell
New-AzureRMResourceGroup -Name "ContosoVMRG" -Location Local
```

Une fois le groupe de ressources créé, la propriété **État de l’approvisionnement** est définie sur **Réussi**.

## <a name="next-steps"></a>Étapes suivantes

* [Installer et configurer l’interface de ligne de commande](azure-stack-connect-cli.md)

* [Développer des modèles](user/azure-stack-develop-templates.md)







