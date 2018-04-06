---
title: Réinitialisation du mot de passe ou de la configuration Bureau à distance sur une machine virtuelle Windows | Microsoft Docs
description: Découvrez comment réinitialiser un mot de passe de compte ou des services Bureau à distance sur une machine virtuelle Windows à l’aide du Portail Azure ou d’Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: danis
ms.openlocfilehash: d793b101e3b6fa5a441158ef8ca9f7cd6d3a5a5b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Comment réinitialiser le service Bureau à distance ou son mot de passe de connexion dans une machine virtuelle Windows
Si vous ne pouvez pas vous connecter à une machine virtuelle Windows, vous pouvez réinitialiser le mot de passe d’administrateur local ou la configuration du service Bureau à distance (pas de prise en charge sur les contrôleurs de domaine Windows). Vous pouvez utiliser le portail Azure ou l’extension d’accès aux machines virtuelles dans Azure PowerShell pour réinitialiser le mot de passe. Une fois que vous êtes connecté à la machine virtuelle, vous devez réinitialiser le mot de passe pour cet utilisateur.  
Si vous utilisez PowerShell, assurez-vous d’avoir le [dernier module PowerShell installé et configuré](/powershell/azure/overview)et d’être connecté à votre abonnement Azure. Vous pouvez également [effectuer ces étapes pour les machines virtuelles créées à l’aide du modèle de déploiement classique](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Comment réinitialiser la configuration ou les informations d’identification
Vous pouvez réinitialiser les services Bureau à distance et les informations d’identification de différentes manières, selon vos besoins :

- [Réinitialisation à l’aide du portail Azure](#azure-portal)
- [Réinitialisation à l’aide d’Azure PowerShell](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Portail Azure
Pour développer le menu du portail, cliquez sur les trois barres dans l’angle supérieur gauche, puis sur **Machines virtuelles** :

![Accédez à votre machine virtuelle Azure](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe de compte d’administrateur local**

Sélectionnez votre machine virtuelle Windows, puis cliquez sur **Support + dépannage** > **Réinitialiser le mot de passe**. Le panneau de réinitialisation du mot de passe s’affiche :

![Page Réinitialiser le mot de passe](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Entrez le nom d’utilisateur et un nouveau mot de passe, puis cliquez sur **Mettre à jour**. Essayez à nouveau de vous connecter à votre machine virtuelle.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Réinitialiser la configuration du service Bureau à distance**

Sélectionnez votre machine virtuelle Windows, puis cliquez sur **Support + dépannage** > **Réinitialiser le mot de passe**. Le panneau de réinitialisation du mot de passe s’affiche. 

![Réinitialiser la configuration RDP](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Sélectionnez **Réinitialiser la configuration uniquement** dans le menu déroulant, puis cliquez sur **Mettre à jour**. Essayez à nouveau de vous connecter à votre machine virtuelle.


## <a name="vmaccess-extension-and-powershell"></a>Extension VMAccess et PowerShell
Assurez-vous d’avoir le [dernier module PowerShell installé et configuré](/powershell/azure/overview)et d’être connecté à votre abonnement Azure à l’aide de l’applet de commande `Login-AzureRmAccount`.

### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe de compte d’administrateur local**
Réinitialisez le mot de passe ou le nom d’utilisateur de l’administrateur à l’aide de l’applet de commande PowerShell [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension). Créez vos informations d’identification de compte comme suit :

```powershell
$cred=Get-Credential
```

> [!NOTE] 
> Si vous tapez un nom différent de celui du compte d’administrateur local actuel sur votre machine virtuelle, l’extension VMAccess ajoute un compte d’administrateur local avec ce nom et affecte le mot de passe spécifié à ce compte. Si le compte d’administrateur local sur votre machine virtuelle existe, le mot de passe est réinitialisé et, si le compte est désactivé, l’extension VMAccess l’active.


L’exemple suivant met à jour la machine virtuelle nommée `myVM` dans le groupe de ressources nommé `myResourceGroup`, sur les informations d’identification spécifiées.

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().UserName -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

### <a name="reset-the-remote-desktop-service-configuration"></a>**Réinitialiser la configuration du service Bureau à distance**
Réinitialisez l’accès à distance à votre machine virtuelle avec l’applet de commande PowerShell [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension). L’exemple suivant réinitialise l’extension d’accès nommée `myVMAccess`, sur la machine virtuelle nommée `myVM`, dans le groupe de ressources nommé `myResourceGroup` :

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> En toutes circonstances, une machine virtuelle ne peut avoir qu’un seul agent d’accès de machine virtuelle. Pour pouvoir définir les propriétés de l’agent d’accès à la machine virtuelle, l’option `-ForceRerun` peut être utilisée. Si vous utilisez `-ForceRerun`, assurez-vous d’utiliser le même nom pour l’agent d’accès à la machine virtuelle que celui défini par les commandes précédentes.

Si vous ne pouvez toujours pas vous connecter à distance à votre machine virtuelle, consultez les étapes supplémentaires dans la rubrique [Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure exécutant Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Si vous perdez la connexion au contrôleur de domaine Windows, vous devez le restaurer à partir d’une sauvegarde de contrôleur de domaine.

## <a name="next-steps"></a>Étapes suivantes
Si l’extension d’accès aux machines virtuelles Azure ne répond pas et que vous ne pouvez pas réinitialiser le mot de passe, vous pouvez [réinitialiser le mot de passe Windows local en mode hors connexion](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Cette méthode est plus avancée et nécessite que vous connectiez le disque dur virtuel de la machine virtuelle problématique à une autre machine virtuelle. Suivez d’abord la procédure décrite dans cet article et n’utilisez la méthode de réinitialisation du mot de passe en mode hors connexion qu’en dernier recours.

[Extensions et fonctionnalités des machines virtuelles Azure](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Connexion à une machine virtuelle Microsoft Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Résolution des problèmes de connexion Bureau à distance avec une machine virtuelle Azure Windows](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

