---
title: "Configurer l’environnement PowerShell de l’opérateur Azure Stack | Microsoft Docs"
description: "Découvrez comment configurer l’environnement PowerShell de l’opérateur Azure Stack."
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: sngun
ms.openlocfilehash: 54f0305afb94b8b19ea7fada837a73f7c6a1963c
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2017
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Configurer l’environnement PowerShell de l’opérateur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

En tant qu’opérateur Azure Stack, vous pouvez configurer l’environnement PowerShell de votre Kit de développement Azure Stack. Une fois la configuration effectuée, vous pouvez utiliser PowerShell pour gérer les ressources Azure Stack, par exemple créer des offres, des plans et des quotas, gérer les alertes, et ainsi de suite. Cette rubrique concerne uniquement les environnements d’opérateur cloud. Si vous souhaitez configurer PowerShell pour l’environnement utilisateur, consultez la rubrique [Configurer l’environnement PowerShell de l’utilisateur Azure Stack](user/azure-stack-powershell-configure-user.md). 

## <a name="prerequisites"></a>Composants requis

Effectuez les étapes prérequises suivantes à partir du [Kit de développement](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes [connecté par le biais d’un VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) : 

* Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).  
* Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurer l’environnement de l’opérateur et se connecter à Azure Stack

Selon le type de déploiement (Azure AD ou AD FS), exécutez un des scripts suivants pour configurer l’environnement de l’opérateur Azure Stack avec PowerShell (veillez à remplacer les valeurs AAD tenantName, GraphAudience et ArmEndpoint selon la configuration de votre environnement) :

### <a name="azure-active-directory-aad-based-deployments"></a>Déploiements basés sur Azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    
  Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience 

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackAdmin"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackAdmin" `
    -TenantId $TenantID 
  ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Déploiements basés sur Active Directory Federation Services (AD FS)
         
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

# For Azure Stack development kit, this value is adminvault.local.azurestack.external 
$KeyvaultDnsSuffix = “<Keyvault DNS suffix for your environment>”


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

Nous avons terminé l’installation. Nous allons maintenant utiliser PowerShell pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Étapes suivantes
* [Développer des modèles pour Azure Stack](user/azure-stack-develop-templates.md)
* [Déployer des modèles avec PowerShell](user/azure-stack-deploy-template-powershell.md)
