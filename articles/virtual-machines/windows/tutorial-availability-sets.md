---
title: "Tutoriel sur les groupes à haute disponibilité pour les machines virtuelles Windows dans Azure | Microsoft Docs"
description: "Découvrez les groupes à haute disponibilité pour les machines virtuelles Windows dans Azure."
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 02/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 247f86dafe35d69dd742583d246862b739d9fe90
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-use-availability-sets"></a>Utilisation des groupes à haute disponibilité

Ce didacticiel explique comment améliorer la disponibilité et la fiabilité de vos solutions de machine virtuelle sur Azure en utilisant une fonctionnalité appelée Groupes à haute disponibilité. Les groupes à haute disponibilité veillent à ce que les machines virtuelles que vous déployez sur Azure soient distribuées sur plusieurs nœuds matériels isolés d'un cluster. Leur utilisation garantit qu’en cas de défaillance matérielle ou logicielle dans Azure, seul un sous-ensemble de vos machines virtuelles est affecté et que votre solution globale reste disponible et opérationnelle. 

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles
> * Vérifier Azure Advisor

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.3 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 

## <a name="availability-set-overview"></a>Vue d’ensemble des groupes à haute disponibilité

Un groupe à haute disponibilité est une fonctionnalité de regroupement logique que vous pouvez utiliser dans Azure pour vous assurer que les ressources de machine virtuelle que vous y incluez sont isolées les unes des autres lors de leur déploiement dans un centre de données Azure. Azure veille à ce que les machines virtuelles que vous placez dans un groupe à haute disponibilité s’exécutent sur plusieurs serveurs physiques, racks de calcul, unités de stockage et commutateurs réseau. En cas de défaillance matérielle ou logicielle dans Azure, seul un sous-ensemble de vos machines virtuelles est affecté et votre application globale reste opérationnelle et continue d’être disponible pour vos clients. Les groupes à haute disponibilité sont une fonctionnalité essentielle pour créer des solutions cloud fiables.

Prenons l’exemple d’une solution basée sur une machine virtuelle classique dans laquelle vous disposez de 4 serveurs web frontaux et utilisez 2 machines virtuelles principales hébergeant une base de données. Avec Azure, vous pouvez définir deux groupes à haute disponibilité avant de déployer vos machines virtuelles : l’un pour le niveau web et l’autre pour le niveau base de données. Lorsque vous créez une machine virtuelle, vous pouvez spécifier le groupe à haute disponibilité en tant que paramètre pour la commande az vm create, de sorte qu’Azure veille automatiquement à ce que les machines virtuelles que vous créez dans le groupe disponible soient isolées sur plusieurs ressources matérielles. Si le matériel sur lequel s’exécute l’une de vos machines virtuelles serveur web ou serveur de base de données rencontre un problème, vous savez que les autres instances de votre serveur web et de vos machines virtuelles de base de données continueront à s’exécuter parce qu’elles se trouvent sur d’autres éléments matériels.

Utilisez des groupes à haute disponibilité quand vous souhaitez déployer des solutions fiables basées sur des machines virtuelles dans Azure.

## <a name="create-an-availability-set"></a>Créer un groupe à haute disponibilité

Vous pouvez créez un groupe à haute disponibilité avec la commande [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Dans cet exemple, définissez le nombre de domaines de mise à jour et d’erreur sur *2* pour le groupe à haute disponibilité nommé *myAvailabilitySet* dans le groupe de ressources *myResourceGroupAvailability*.

Créez un groupe de ressources.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Créez un groupe à haute disponibilité managé à l’aide de [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) avec le paramètre `-sku aligned`.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location "EastUS" `
   -Name "myAvailabilitySet" `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Créer des machines virtuelles dans un groupe à haute disponibilité
Vous devez créer des machines virtuelles au sein du groupe à haute disponibilité pour vous assurer qu’elles sont correctement réparties dans le matériel. Vous ne pouvez pas ajouter une machine virtuelle existante à un groupe à haute disponibilité après sa création. 

Le matériel situé à un emplacement est divisé en plusieurs domaines de mise à jour et d’erreur. Un **domaine de mise à jour** est un groupe de machines virtuelles et d’équipements physiques sous-jacents pouvant être redémarrés en même temps. Les machines virtuelles d’un même **domaine d’erreur** partagent un espace de stockage commun ainsi qu’une source d’alimentation et un commutateur réseau. 

Quand vous créez une machine virtuelle à l’aide de [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm), vous utilisez le paramètre `-AvailabilitySetName` pour spécifier le nom du groupe à haute disponibilité.

Tout d’abord, définissez un nom d’utilisateur administrateur et un mot de passe pour la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

Créez ensuite deux machines virtuelles avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) dans le groupe à haute disponibilité.

```azurepowershell-interactive
for ($i=1; $i -le 2; $i++)
{
    New-AzureRmVm `
        -ResourceGroupName "myResourceGroupAvailability" `
        -Name "myVM$i" `
        -Location "East US" `
        -VirtualNetworkName "myVnet" `
        -SubnetName "mySubnet" `
        -SecurityGroupName "myNetworkSecurityGroup" `
        -PublicIpAddressName "myPublicIpAddress$i" `
        -AvailabilitySetName "myAvailabilitySet" `
        -Credential $cred
}
```

Le paramètre `-AsJob` crée la machine virtuelle en tant que tâche en arrière-plan. Vous recevez donc les invites PowerShell. Vous pouvez afficher les détails des travaux en arrière-plan à l’aide du cmdlet `Job`. La création et la configuration des deux machines virtuelles prennent quelques minutes. Quand vous avez terminé, vous disposez de deux machines virtuelles réparties sur le matériel sous-jacent. 

Si vous examinez le groupe à haute disponibilité dans le portail en accédant à Groupes de ressources > myResourceGroupAvailability > myAvailabilitySet, vous devez voir la façon dont les machines virtuelles sont réparties entre les deux domaines d’erreur et de mise à jour.

![Groupe à haute disponibilité dans le portail](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Vérifier les tailles de machines virtuelles disponibles 

Vous pouvez ajouter ultérieurement d’autres machines virtuelles au groupe à haute disponibilité, mais vous devez connaître les tailles des machines virtuelles qui sont disponibles sur le matériel. Utilisez [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) pour répertorier toutes les tailles disponibles sur le cluster matériel pour le groupe à haute disponibilité.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -ResourceGroupName "myResourceGroupAvailability" `
   -AvailabilitySetName "myAvailabilitySet"
```

## <a name="check-azure-advisor"></a>Vérifier Azure Advisor 

Vous pouvez également utiliser Azure Advisor pour obtenir plus d’informations sur les façons d’améliorer la disponibilité de vos machines virtuelles. Azure Advisor décrit les meilleures pratiques à suivre pour optimiser vos déploiements Azure. Il analyse votre télémétrie de configuration et d’utilisation des ressources, puis recommande des solutions qui peuvent vous aider à améliorer la rentabilité, les performances, la haute disponibilité et la sécurité de vos ressources Azure.

Connectez-vous au [portail Azure](https://portal.azure.com), sélectionnez **Tous les services** et saisissez **Advisor**. Le tableau de bord du conseiller affiche des recommandations personnalisées pour l’abonnement sélectionné. Pour plus d’informations, consultez [Bien démarrer avec Azure Advisor](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un groupe à haute disponibilité
> * Créer une machine virtuelle dans un groupe à haute disponibilité
> * Vérifier les tailles de machines virtuelles disponibles
> * Vérifier Azure Advisor

Passez au didacticiel suivant pour en savoir plus sur les groupes de machines virtuelles identiques.

> [!div class="nextstepaction"]
> [Créer un groupe de machines virtuelles identiques](tutorial-create-vmss.md)


