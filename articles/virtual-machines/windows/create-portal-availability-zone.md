---
title: "Créer une machine virtuelle Windows zonale avec le portail Azure | Microsoft Docs"
description: "Créer une machine virtuelle Windows dans une zone de disponibilité avec le portail Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 63c2baa2f3924cf2274608df98b854683bc2baa8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Créer une machine virtuelle Windows dans une zone de disponibilité avec le portail Azure

Cet article aborde l’utilisation du portail Azure pour créer une machine virtuelle dans une zone de disponibilité Azure. Une [zone de disponibilité](../../availability-zones/az-overview.md) est une zone physiquement séparée dans une région Azure. Utilisez les zones de disponibilité pour protéger vos applications et vos données dans l’éventualité peu probable d’une défaillance ou d’une perte d’un centre de données entier.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]


## <a name="log-in-to-azure"></a>Connexion à Azure 

Connectez-vous au portail Azure à l’adresse https://portal.azure.com.

## <a name="create-virtual-machine"></a>Create virtual machine

1. Cliquez sur **Créer une ressource** en haut à gauche du portail Azure.

2. Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. 

3. Saisissez les informations de la machine virtuelle. Le nom d’utilisateur et le mot de passe que vous avez entrés vous serviront pour vous connecter à la machine virtuelle. Lorsque vous avez terminé, cliquez sur **OK**.

    ![Saisie des informations de base sur votre machine virtuelle dans le panneau du portail](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. Veillez à sélectionner l’une des tailles prises en charge dans la préversion des zones de disponibilité, telles que *DS1_v2 Standard*. 

    ![Capture d’écran montrant les tailles de machine virtuelle](./media/create-portal-availability-zone/create-linux-vm-portal-sizes.png)  

5. Dans **Paramètres** > **Haute disponibilité**, sélectionnez une des zones numérotées dans la liste déroulante **Zone de disponibilité**, conservez les valeurs par défaut restantes et cliquez sur **OK**.

    ![Sélectionner une zone de disponibilité](./media/create-portal-availability-zone/create-linux-vm-portal-availability-zone.png)

6. Sur la page Résumé, cliquez sur **Acheter** pour lancer le déploiement de machine virtuelle.

7. La machine virtuelle sera épinglée au tableau de bord du portail Azure. Une fois le déploiement terminé, le récapitulatif de la machine virtuelle s’ouvre automatiquement.


## <a name="zone-for-ip-address-and-managed-disk"></a>Zone pour l’adresse IP et le disque managé

Lorsque la machine virtuelle est déployée dans une zone de disponibilité, les ressources d’adresse IP et de disque géré sont déployées dans la même zone de disponibilité. Vous pouvez confirmer les paramètres de la zone à l’aide d’Azure PowerShell. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Les exemples suivants obtiennent des informations sur les ressources d’un groupe de ressources nommé *myResourceGroup*. Remplacez le nom du groupe de ressources que vous avez utilisé pour créer la machine virtuelle.

Recherchez la zone de l’adresse IP publique avec [Get-AzureRmPublicIpAddress](/en-us/powershell/module/azurerm.network/get-azurermpublicipaddress) :

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup
```
Le paramètre `Zones` dans le résultat montre que l’adresse IP publique se trouve dans la même zone de disponibilité que la machine virtuelle :

```powershell
Name                     : myVM-ip
ResourceGroupName        : myResourceGroup
Location                 : eastus2
Id                       : /subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/danlep0911/providers/Micr
                           osoft.Network/publicIPAddresses/myVM-ip
Etag                     : W/"b67e14c0-7e8a-4d12-91c5-da2a5dfad132"
ResourceGuid             : 314bf57d-9b25-4474-9282-db3561d536aa
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.68.16.25
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVM11842/ipConfigurations/ipconfig1"
                           }
DnsSettings              : null
Zones                    : {2}
```


La ressource de disque géré de la machine virtuelle est également créée dans la même zone de disponibilité. Vous pouvez le vérifier avec [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk) :

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

Le résultat montre que le disque géré se trouve dans la même zone de disponibilité que la machine virtuelle :

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer une machine virtuelle dans une zone de disponibilité. Apprenez-en davantage sur les [régions et la disponibilité](regions-and-availability.md) des machines virtuelles Azure.
