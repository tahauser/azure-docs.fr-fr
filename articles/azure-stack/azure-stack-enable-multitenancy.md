---
title: Activer l’architecture multilocataire dans Azure Stack | Microsoft Docs
description: Découvrez comment prendre en charge plusieurs annuaires Azure Active Directory dans Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 66689d80cbee0be36b3e8c9951a43d0d43fa01be
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/14/2018
---
# <a name="enable-multi-tenancy-in-azure-stack"></a>Activer l’architecture multilocataire dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Vous pouvez configurer Azure Stack pour prendre en charge l’utilisation des services Azure Stack par les utilisateurs de plusieurs locataires Azure Active Directory (Azure AD). Par exemple, considérez le scénario suivant :

 - Vous êtes l’administrateur de services de contoso.onmicrosoft.com, où Azure Stack est installé.
 - Marie est l’administrateur de l’annuaire de fabrikam.onmicrosoft.com, où se trouvent les utilisateurs invités. 
 - La société de Marie reçoit des services IaaS et PaaS de la part de votre entreprise, et doit autoriser les utilisateurs de l’annuaire invité (fabrikam.onmicrosoft.com) à se connecter et à utiliser des ressources Azure Stack dans contoso.onmicrosoft.com.

Ce guide fournit les étapes nécessaires, dans le cadre de ce scénario, pour configurer une architecture multilocataire dans Azure Stack.  Dans ce scénario, Marie et vous-même devez effectuer des étapes pour permettre aux utilisateurs de Fabrikam de se connecter et d’utiliser les services du déploiement Azure Stack dans Contoso.  

## <a name="before-you-begin"></a>Avant de commencer
Il existe quelques prérequis à prendre en compte avant de configurer une architecture multilocataire Azure Stack :
  
 - Marie et vous-même devez coordonner les étapes administratives dans l’annuaire où Azure Stack est installé (Contoso) et dans l’annuaire invité (Fabrikam).  
 - Vérifiez que vous avez [installé](azure-stack-powershell-install.md) et [configuré](azure-stack-powershell-configure-admin.md) PowerShell pour Azure Stack.
 - [Téléchargez les outils Azure Stack](azure-stack-powershell-download.md) et importez les modules de connexion et d’identité :

    ````PowerShell
        Import-Module .\Connect\AzureStack.Connect.psm1
        Import-Module .\Identity\AzureStack.Identity.psm1
    ```` 
 - Marie aura besoin d’un accès [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) à Azure Stack. 

## <a name="configure-azure-stack-directory"></a>Configurer l’annuaire Azure Stack
Dans cette section, vous allez configurer Azure Stack pour autoriser les connexions à partir de locataires de l’annuaire Azure AD Fabrikam.

### <a name="onboard-guest-directory-tenant"></a>Intégrer le locataire d’annuaire invité
Ensuite, intégrez le locataire d’annuaire invité (Fabrikam) à Azure Stack.  Cette étape configure Azure Resource Manager pour accepter les utilisateurs et les principaux du service du locataire d’annuaire invité.

````PowerShell
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location "local" `
 -ResourceGroupName $ResourceGroupName
````



## <a name="configure-guest-directory"></a>Configurer l’annuaire invité
Après avoir terminé les étapes dans l’annuaire Azure Stack, Marie doit autoriser Azure Stack à accéder à l’annuaire invité et inscrire Azure Stack auprès de l’annuaire invité. 

### <a name="registering-azure-stack-with-the-guest-directory"></a>Inscription d’Azure Stack auprès de l’annuaire invité
Une fois que l’administrateur de l’annuaire invité a autorisé Azure Stack à accéder à l’annuaire de Fabrikam, il doit inscrire Azure Stack auprès du locataire d’annuaire de Fabrikam.

````PowerShell
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
````
## <a name="direct-users-to-sign-in"></a>Inviter les utilisateurs à se connecter
Maintenant que Marie et vous-même avez effectué les étapes d’intégration de l’annuaire de Marie, celle-ci peut inviter les utilisateurs de Fabrikam à se connecter.  Les utilisateurs de Fabrikam (autrement dit, ceux avec le suffixe fabrikam.onmicrosoft.com) se connectent en accédant à https://portal.local.azurestack.external.  

Marie invitera les [principaux étrangers](../active-directory/active-directory-understanding-resource-access.md) dans l’annuaire Fabrikam (autrement dit, les utilisateurs dans l’annuaire Fabrikam sans le suffixe fabrikam.onmicrosoft.com) à se connecter à l’aide de https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  S’ils n’utilisent pas cette URL, ils sont dirigés vers leur annuaire par défaut (Fabrikam) et reçoivent une erreur indiquant que leur administrateur n’a pas donné son consentement.

## <a name="next-steps"></a>Étapes suivantes

- [Gérer les fournisseurs délégués](azure-stack-delegated-provider.md)
- [Concepts clés d’Azure Stack](azure-stack-key-features.md)
