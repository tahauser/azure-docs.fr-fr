---
title: "Comment configurer la MSI sur une machine virtuelle Azure à l’aide de PowerShell"
description: "Instructions détaillées sur la configuration de l’identité du service administré (MSI) sur une machine virtuelle Azure, à l’aide de PowerShell."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: cb87dd88c0425383243edcf12b946cb1821aabe5
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurer une identité du service administré (MSI) de machine virtuelle à l’aide de PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L’identité du service administré fournit des services Azure avec une identité automatiquement gérée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous apprenez à activer et à supprimer la MSI pour une machine virtuelle Azure à l’aide de PowerShell.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Installez également [la dernière version d'Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) (version 4.3.1 ou ultérieure), le cas échéant.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Activer l’identité du service administré lors de la création d’une machine virtuelle Azure

Pour créer une machine virtuelle compatible avec l’identité du service administré :

1. Consultez l’un des démarrages rapides de machine virtuelle Azure suivants, en ne complétant que les sections nécessaires (« Se connecter à Azure », « Créer un groupe de ressources », « Créer un groupe de mise en réseau », « Créer la machine virtuelle »). 

   > [!IMPORTANT] 
   > Lorsque vous accédez à la section « Créer la machine virtuelle », apportez une légère modification à la syntaxe de l’applet de commande [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Veillez à ajouter un paramètre `-IdentityType "SystemAssigned"` pour configurer la machine virtuelle avec une identité du service administré, par exemple :
   >  
   > `$vmConfig = New-AzureRmVMConfig -VMName myVM -IdentityType "SystemAssigned" ...`

   - [Créer une machine virtuelle Windows avec PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md)
   - [Créer une machine virtuelle Linux avec PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md)



2. Ajoutez l’extension de machine virtuelle de la MSI à l’aide du paramètre `-Type` sur la cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de machine virtuelle, et nommez-le à l’aide paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton :

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Activer l’identité du service administré sur une machine virtuelle Azure existante

Si vous devez activer l’identité du service administré sur une machine virtuelle existante :

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

   ```powershell
   Login-AzureRmAccount
   ```

2. Commencez par récupérer les propriétés de la machine virtuelle à l’aide de la cmdlet `Get-AzureRmVM`. Ensuite, pour activer la MSI, utilisez l’option `-IdentityType` de la cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) :

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Ajoutez l’extension de machine virtuelle de la MSI à l’aide du paramètre `-Type` sur la cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Vous pouvez transmettre « ManagedIdentityExtensionForWindows » ou « ManagedIdentityExtensionForLinux », selon le type de machine virtuelle, et nommez-le à l’aide paramètre `-Name`. Le paramètre `-Settings` spécifie le port utilisé par le point de terminaison de jeton OAuth pour l’acquisition de jeton. Veillez à indiquer le paramètre `-Location` approprié, correspondant à l’emplacement de la machine virtuelle existante :

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Supprimer l’identité du service administré d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle pour laquelle la MSI n’est plus nécessaire, vous pouvez utiliser la cmdlet `RemoveAzureRmVMExtension` pour supprimer la MSI de la machine virtuelle :

1. Connectez-vous au portail Azure à l’aide de `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

   ```powershell
   Login-AzureRmAccount
   ```

2. Utilisez le commutateur `-Name` avec l’applet de commande [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension), en spécifiant le même nom que celui utilisé lorsque vous avez ajouté l’extension :

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Contenu connexe

- [Vue d’ensemble de l’identité du service administré](msi-overview.md)
- Pour obtenir les guides de démarrages rapides complets sur la création de machines virtuelles Azure, consultez :
  
  - [Créer une machine virtuelle Windows avec PowerShell](~/articles/virtual-machines/windows/quick-create-powershell.md) 
  - [Créer une machine virtuelle Linux avec PowerShell](~/articles/virtual-machines/linux/quick-create-powershell.md) 

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
















