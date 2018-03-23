---
title: Configurer l’environnement PowerShell de l’opérateur Azure Stack | Microsoft Docs
description: Découvrez comment configurer l’environnement PowerShell de l’opérateur Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 37D9CAC9-538B-4504-B51B-7336158D8A6B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: mabrigg
ms.openlocfilehash: 57aa5a1ccc45548c3e789b50c888f669df39d5f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="configure-the-azure-stack-operators-powershell-environment"></a>Configurer l’environnement PowerShell de l’opérateur Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez configurer Azure Stack pour utiliser PowerShell en vue de gérer les ressources Azure Stack, par exemple créer des offres, des plans, des quotas et des alertes. Cette rubrique vous permet de configurer l’environnement de l’opérateur. Si vous voulez configurer PowerShell pour l’environnement utilisateur, consultez l’article [Configurer l’environnement PowerShell de l’utilisateur Azure Stack](user/azure-stack-powershell-configure-user.md).

## <a name="prerequisites"></a>Prérequis

Effectuez les étapes prérequises suivantes à partir du [Kit de développement](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes [connecté par le biais d’un VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) : 

* Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).  
* Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md).  

## <a name="configure-the-operator-environment-and-sign-in-to-azure-stack"></a>Configurer l’environnement de l’opérateur et se connecter à Azure Stack

Configurez l’environnement de l’opérateur Azure Stack avec PowerShell. En fonction du type de déploiement (Azure AD ou AD FS), exécutez l’un des scripts suivants (remplacez les valeurs tenantName Azure AD, le point de terminaison GraphAudience et la valeur ArmEndpoint par la configuration de votre propre environnement) :

### <a name="azure-active-directory-azure-ad-based-deployments"></a>Déploiements basés sur Azure Active Directory (Azure AD)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -AADTenantName "<mydirectorytenant>.onmicrosoft.com" -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````


### <a name="active-directory-federation-services-ad-fs-based-deployments"></a>Déploiements basés sur Active Directory Federation Services (AD FS)

````powershell  
#  Create an administrator environment
Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint "https://adminmanagement.local.azurestack.external"

# Get the value of your Directory Tenant ID
$TenantID = Get-AzsDirectoryTenantId -ADFS -EnvironmentName AzureStackAdmin

# After registering the AzureRM environment, cmdlets can be 
# easily targeted at your Azure Stack instance.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID
````

## <a name="test-the-connectivity"></a>Tester la connectivité

Vous avez terminé l’installation. Nous allons maintenant utiliser PowerShell pour créer des ressources dans Azure Stack. Par exemple, vous pouvez créer un groupe de ressources pour une application et ajouter une machine virtuelle. Utilisez la commande suivante pour créer le groupe de ressources nommé « myResourceGroup » :

```powershell
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Étapes suivantes
* [Développer des modèles pour Azure Stack](user/azure-stack-develop-templates.md)
* [Déployer des modèles avec PowerShell](user/azure-stack-deploy-template-powershell.md)
