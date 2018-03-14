---
title: "Disques chiffrés de groupes de machines virtuelles identiques Azure | Microsoft Docs"
description: "Découvrez comment chiffrer des disques joints dans des groupes de machines virtuelles identiques."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: dddcece9f7566961b256369330661e5dbd5d4665
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Chiffrer des disques de données joints et de systèmes d’exploitation dans un groupe de machines virtuelles identiques
Les [groupes de machines virtuelles identiques](/azure/virtual-machine-scale-sets/) Azure prennent en charge Azure Disk Encryption (ADE).  Azure Disk Encryption peut être activé pour les groupes de machines virtuelles identiques Windows et Linux, afin de protéger les données au repos des groupes identiques à l’aide d’une technologie de chiffrement standard du secteur. Pour plus d’informations, consultez Azure Disk Encryption pour machines virtuelles Windows et Linux.

> [!NOTE]
>  Azure Disk Encryption pour les groupes de machines virtuelles identiques est actuellement en préversion publique, disponible dans toutes les régions publiques Azure.

Azure Disk Encryption est pris en charge :
- Pour les groupes identiques créés avec des disques managés, et non pris en charge pour les groupes identiques de disques natifs (ou non managés).
- Pour les volumes de données et de systèmes d’exploitation dans les groupes identiques Windows. La désactivation du chiffrement est prise en charge pour les volumes de données et de systèmes d’exploitation des groupes identiques Windows.
- Pour les volumes de données dans les groupes identiques Linux. Le chiffrement de disque de systèmes d’exploitation n’est PAS pris en charge dans la préversion actuelle pour les groupes identiques Linux.

Les opérations de mise à niveau et de réinitialisation des machines virtuelles d’un groupe identique ne sont pas prises en charge dans la préversion actuelle. L’utilisation de la préversion d’Azure Disk Encryption pour les groupes de machines virtuelles identiques est uniquement recommandée dans les environnements de test. Dans la préversion, n’activez pas le chiffrement de disque dans les environnements de production dans lesquels vous devez mettre à niveau une image de système d’exploitation dans un groupe identique chiffré.

## <a name="prerequisites"></a>Prérequis
Installez les dernières versions [d’Azure Powershell](https://github.com/Azure/azure-powershell/releases) qui contiennent les commandes de chiffrement.

La préversion d’Azure Disk Encryption pour les groupes de machines virtuelles identiques nécessite que vous inscriviez automatiquement votre abonnement via les commandes PowerShell suivantes : 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Attendez environ 10 minutes jusqu’à ce que l’état « Registered » soit renvoyé par la commande suivante : 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Créer un coffre de clés Azure activé pour le chiffrement de disque
Créez un coffre de clés dans le même abonnement et la même région que le groupe identique, et définissez la stratégie d’accès « EnabledForDiskEncryption ».

```powershell
$rgname="windatadiskencryptiontest"
$VaultName="encryptionvault321"

New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $rgName -Location southcentralus -EnabledForDiskEncryption
``` 

Ou bien, activez un coffre de clés existant dans le même abonnement et la même région que le groupe identique pour le chiffrement de disque.

```powershell
$VaultName="encryptionvault321"
Set-AzureRmKeyVaultAccessPolicy -VaultName $VaultName -EnabledForDiskEncryption
```

## <a name="enable-encryption"></a>Activer le chiffrement
Les commandes suivantes permettent de chiffrer un disque de données dans un groupe identique en cours d’exécution à l’aide d’un coffre de clés dans le même groupe de ressources. Vous pouvez également utiliser des modèles pour chiffrer des disques dans un [groupe identique Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) ou un [groupe identique Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) en cours d’exécution.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
$DiskEncryptionKeyVaultUrl="https://encryptionvault321.vault.azure.net"
$KeyVaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/windatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvault321"

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $VmssName `
    -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId –VolumeType Data
```

## <a name="check-encryption-progress"></a>Vérifier la progression du chiffrement
Utilisez les commandes suivantes pour afficher l’état de chiffrement du groupe identique.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

Get-AzureRmVmssVMDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName -InstanceId "4"
```

## <a name="disable-encryption"></a>Désactiver le chiffrement
Désactivez le chiffrement d’un groupe de machines virtuelles identiques en cours d’exécution à l’aide des commandes suivantes. Vous pouvez également utiliser des modèles pour désactiver le chiffrement dans un [groupe identique Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) ou un [groupe identique Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux) en cours d’exécution.

```powershell
$rgname="windatadiskencryptiontest"
$VmssName="nt1vm"
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName
```
