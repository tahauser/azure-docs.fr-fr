---
title: "Comment attribuer à une identité du service administré un accès à une ressource Azure, à l’aide de PowerShell"
description: "Instructions détaillées pour attribuer une identité du service administré à une ressource, et un accès à une autre ressource, à l’aide de PowerShell."
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 36b95a1de64fe49a1225bd647ca963c8957fe160
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>Attribuer à une identité de service administré (MSI) un accès à une ressource à l’aide de PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Une fois que vous avez configuré une ressource Azure avec une identité du service administré, vous pouvez accorder à cette dernière un accès à une autre ressource, tout comme n’importe quel principal de sécurité. Cet exemple montre comment accorder à l’identité du service administré d’une machine virtuelle Azure l’accès à un compte de stockage Azure, à l’aide de PowerShell.

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

En outre, si ce n’est pas déjà fait, installez [Azure PowerShell version 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1).

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Utiliser le contrôle d’accès en fonction du rôle (RBAC) pour attribuer à l’identité du service administré un accès à une autre ressource

Après avoir activé l’identité du service administré sur une ressource Azure, [telle qu’une machine virtuelle Azure](qs-configure-powershell-windows-vm.md) :

1. Connectez-vous à Azure à l’aide du cmdlet `Login-AzureRmAccount`. Utilisez un compte associé à l’abonnement Azure sous lequel vous avez configuré l’identité du service administré :

   ```powershell
   Login-AzureRmAccount
   ```
2. Dans cet exemple, nous accordons à une machine virtuelle Azure l’accès à un compte de stockage. Tout d’abord, nous utilisons [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) pour obtenir le principal de service pour la machine virtuelle nommée « myVM », qui a été créé lorsque nous avons activé l’identité du service administré. Ensuite, nous utilisons [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) pour accorder à la machine virtuelle un accès de type « Lecteur » vers un compte de stockage appelé « myStorageAcct »  :

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>Résolution de problèmes

Si l’identité du service administré pour la ressource n’apparaît pas dans la liste des identités disponibles, vérifiez si elle a été activée correctement. Dans notre cas, nous pouvons revenir à la machine virtuelle Azure dans le [portail Azure](https://portal.azure.com) et :

- Examinez la page « Configuration » et s’assurer que le paramètre d’identité du service administré est activé.
- Examinez la page « Extensions » et assurez-vous que l’extension de l’identité du service administré est déployée avec succès.

En cas d’inexactitude, vous devez redéployer l’identité du service administré sur votre ressource, ou résoudre le problème de déploiement.

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](overview.md).
- Pour activer l’identité du service administré sur une machine virtuelle Azure, consultez [Configurer l’Identité du service administré (MSI) d’une machine virtuelle Azure à l’aide de PowerShell](qs-configure-powershell-windows-vm.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

