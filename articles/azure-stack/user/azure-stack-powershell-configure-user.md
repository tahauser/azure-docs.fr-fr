---
title: "Configurer l’environnement PowerShell de l’utilisateur Azure Stack | Microsoft Docs"
description: "Configurer l’environnement PowerShell de l’utilisateur Azure Stack"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: F4ED2238-AAF2-4930-AA7F-7C140311E10F
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: mabrigg
ms.openlocfilehash: 0bd5b4a98fee7a5d914e53e49a9517f5d3682a88
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-the-azure-stack-users-powershell-environment"></a>Configurer l’environnement PowerShell de l’utilisateur Azure Stack

En tant qu’utilisateur d’Azure Stack, vous pouvez configurer l’environnement PowerShell de votre Kit de développement Azure Stack. Une fois la configuration effectuée, vous pouvez utiliser PowerShell pour gérer les ressources Azure Stack, par exemple vous abonner à des offres, créer des machines virtuelles, déployer des modèles Azure Resource Manager, et ainsi de suite. Cette rubrique concerne uniquement les environnements utilisateur. Si vous souhaitez configurer PowerShell pour l’environnement d’opérateur cloud, consultez l’article [Configurer l’environnement PowerShell de l’opérateur Azure Stack](../azure-stack-powershell-configure-admin.md). 

## <a name="prerequisites"></a>Composants requis 

Effectuez les étapes prérequises suivantes à partir du [Kit de développement](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes [connecté par le biais d’un VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) :

* Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).  
* Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md). 

## <a name="configure-the-user-environment-and-sign-in-to-azure-stack"></a>Configurer l’environnement de l’utilisateur et se connecter à Azure Stack

Selon le type de déploiement (Azure AD ou AD FS), exécutez un des scripts suivants pour configurer PowerShell pour Azure Stack (veillez à remplacer les valeurs AAD tenantName, GraphAudience Endpoint et ArmEndpoint selon la configuration de votre environnement) :

### <a name="azure-active-directory-aad-based-deployments"></a>Déploiements basés sur Azure Active Directory (AAD)
       
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience

  # Get the Active Directory tenantId that is used to deploy Azure Stack
  $TenantID = Get-AzsDirectoryTenantId `
    -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
   ```

### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Déploiements basés sur Active Directory Federation Services (AD FS) 
          
  ```powershell
  # Navigate to the downloaded folder and import the **Connect** PowerShell module
  Set-ExecutionPolicy RemoteSigned
  Import-Module .\Connect\AzureStack.Connect.psm1

  # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Register an AzureRM environment that targets your Azure Stack instance
  Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

  # Set the GraphEndpointResourceId value
  Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

  # Get the Active Directory tenantId that is used to deploy Azure Stack     
  $TenantID = Get-AzsDirectoryTenantId `
    -ADFS `
    -EnvironmentName "AzureStackUser"

  # Sign in to your environment
  Login-AzureRmAccount `
    -EnvironmentName "AzureStackUser" `
    -TenantId $TenantID 
  ```

## <a name="register-resource-providers"></a>Inscrire des fournisseurs de ressources

Quand il s’agit d’un abonnement utilisateur nouvellement créé qui n’a aucune ressource déployée par le biais du portail, les fournisseurs de ressources ne sont pas automatiquement inscrits. Vous devez les inscrire explicitement à l’aide du script suivant :

```powershell
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider -Force
    } 
```

## <a name="test-the-connectivity"></a>Tester la connectivité

Nous avons terminé l’installation. Nous allons maintenant utiliser PowerShell pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Étapes suivantes
* [Développer des modèles pour Azure Stack](azure-stack-develop-templates.md)
* [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
