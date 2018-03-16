---
title: "Gérer des solutions Azure avec PowerShell | Microsoft Docs"
description: "Utilisez Azure PowerShell et Resource Manager pour gérer vos ressources."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 96206482195cdcbd06ee2dafdc551f7b1f81d319
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Gérer les ressources avec Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="understand-scope"></a>Comprendre l’étendue

[!INCLUDE [Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

Dans cet article, vous appliquez tous les paramètres de gestion à un groupe de ressources afin de pouvoir facilement supprimer ces paramètres lorsque vous avez terminé.

Passons à la création du groupe de ressources.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Pour le moment, le groupe de ressources est vide.

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Attribuer un rôle

Dans cet article, vous allez déployer une machine virtuelle et son réseau virtuel. Pour gérer les solutions de machine virtuelle, il existe trois rôles de ressource qui fournissent un accès souvent nécessaire :

* [Collaborateur de machine virtuelle](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Collaborateur de réseau](../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Collaborateur de compte de stockage](../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Au lieu d’affecter des rôles à des utilisateurs, il est souvent plus facile de [créer un groupe Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md) et d’y regrouper les utilisateurs qui ont besoin d’effectuer des actions similaires. Ensuite, vous affectez ce groupe au rôle approprié. Pour simplifier, vous allez créer un groupe Azure Active Directory vide. Vous pouvez toujours affecter ce groupe à un rôle pour une étendue. 

L’exemple suivant crée un groupe et l’affecte au rôle Contributeur de machines virtuelles pour le groupe de ressources. Pour exécuter la commande `New-AzureAdGroup`, vous devez utiliser [Azure Cloud Shell](/azure/cloud-shell/overview) ou [télécharger le module PowerShell Azure AD](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

En règle générale, vous répétez ce processus pour **Contributeur de réseaux** et **Contributeur de comptes de stockage**, pour être sûr que les utilisateurs sont affectés à la gestion des ressources déployées. Dans cet article, vous pouvez ignorer ces étapes.

## <a name="azure-policies"></a>Stratégies Azure

[!INCLUDE [Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Appliquer les stratégies

Votre abonnement comprend déjà plusieurs définitions de stratégie. Pour afficher les définitions de stratégie disponibles, utilisez ceci :

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Vous voyez les définitions de stratégie existantes. Le type de stratégie est **BuiltIn** ou **Custom**. Recherchez les définitions qui décrivent la condition que vous souhaitez affecter. Dans cet article, vous affectez des stratégies qui :

* Limitent les emplacements pour toutes les ressources
* Limitent les références SKU pour les machines virtuelles
* Auditent les machines virtuelles qui n’utilisent pas de disques managés

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Déployer la machine virtuelle

Vous avez affecté des rôles et des stratégies, vous êtes donc prêt à déployer votre solution. La taille par défaut est Standard_DS1_v2, qui correspond à l’une des références SKU autorisées. Lors de l’exécution de cette étape, vous êtes invité à saisir vos informations d’identification. Les valeurs que vous saisissez sont configurées comme le nom d’utilisateur et le mot de passe pour la machine virtuelle.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Une fois votre déploiement terminé, vous pouvez appliquer davantage de paramètres de gestion à la solution.

## <a name="lock-resources"></a>Verrouiller des ressources

[!INCLUDE [Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Verrouiller une ressource

Pour verrouiller la machine virtuelle et le groupe de sécurité réseau, utilisez ceci:

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

La machine virtuelle ne peut être supprimée que si vous supprimez le verrou. Cette étape est expliquée dans [Nettoyer les ressources](#clean-up-resources).

## <a name="tag-resources"></a>Baliser des ressources

[!INCLUDE [Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Baliser des ressources

[!INCLUDE [Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Pour appliquer des balises à une machine virtuelle, utilisez ceci :

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Rechercher des ressources à l’aide de leurs balises

Pour rechercher des ressources à l’aide du nom et de la valeur de leurs balises, utilisez ceci :

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

Vous pouvez utiliser les valeurs retournées pour des tâches de gestion telles que l’arrêt de toutes les machines virtuelles avec une valeur de balise.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Afficher les coûts selon les valeurs de balise

Après avoir appliqué des balises aux ressources, vous pouvez afficher les coûts des ressources à l’aide de ces balises. L’analyse des coûts prend un certain temps pour afficher les dernières données d’utilisation. Il est donc possible que vous ne voyiez pas encore les coûts. Lorsqu’ils sont disponibles, vous pouvez afficher les coûts des ressources des différents groupes de ressources de votre abonnement. Pour voir les coûts, les utilisateurs doivent avoir un [accès de niveau d’abonnement aux informations de facturation](../billing/billing-manage-access.md).

Pour afficher les coûts par balise dans le portail, sélectionnez votre abonnement, puis sélectionnez **Analyse du coût**.

![Analyse des coûts](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Ensuite, filtrez par valeur de balise, puis sélectionnez **Appliquer**.

![Afficher les coûts par balise](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Vous pouvez également utiliser les [API Facturation Azure](../billing/billing-usage-rate-card-overview.md) pour afficher les coûts par programmation.

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous ne pouvez pas supprimer le groupe de sécurité réseau verrouillé tant que vous n’avez pas supprimé le verrou. Pour supprimer le verrou, utilisez ceci :

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la surveillance des machines virtuelles, consultez [Surveiller et mettre à jour une machine virtuelle Windows avec Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Pour plus d’informations sur l’utilisation d’Azure Security Center pour implémenter les pratiques de sécurité recommandées, consultez [Surveiller la sécurité des machines virtuelles à l’aide d’Azure Security Center](../virtual-machines/windows/tutorial-azure-security.md).
* Vous pouvez déplacer des ressources existantes vers un nouveau groupe de ressources. Pour obtenir des exemples, consultez [Déplacer des ressources vers un nouveau groupe de ressources ou un nouvel abonnement](resource-group-move-resources.md).
* Pour obtenir des conseils sur l’utilisation de Resource Manager par les entreprises pour gérer efficacement les abonnements, voir [Structure d’Azure Enterprise - Gouvernance normative de l’abonnement](resource-manager-subscription-governance.md).
