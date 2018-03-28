---
title: Chiffrement des disques de groupes de machines virtuelles identiques Azure | Microsoft Docs
description: Découvrez comment utiliser Azure PowerShell pour chiffrer les instances de machine virtuelle et les disques attachés dans des groupes de machines virtuelles identiques
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: iainfou
ms.openlocfilehash: 856d4bc7dd636b3a2f3d072a10989cafd7efd6a6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Chiffrer des disques de données attachés et de systèmes d’exploitation dans un groupe de machines virtuelles identiques
Conçue pour protéger les données au repos, Azure Disk Encryption (ADE) est une technologie de chiffrement standard prise en charge par les groupes de machines virtuelles identiques. Le chiffrement peut être activé pour les groupes de machines virtuelles identiques Windows et Linux. Pour plus d’informations, consultez [Azure Disk Encryption pour machines virtuelles Windows et Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Azure Disk Encryption pour les groupes de machines virtuelles identiques est actuellement en préversion, disponible dans toutes les régions publiques Azure. 
>
> Les opérations de mise à niveau et de réinitialisation des machines virtuelles d’un groupe identique ne sont pas prises en charge dans la préversion actuelle. Dans la préversion, le chiffrement de groupes identiques est recommandé uniquement dans les environnements de test. Dans la préversion, n’activez pas le chiffrement de disque dans les environnements de production où vous pouvez avoir besoin de mettre à niveau une image de système d’exploitation.

Azure Disk Encryption est pris en charge :
- Pour les groupes identiques créés avec des disques managés, et non pris en charge pour les groupes identiques de disques natifs (ou non managés).
- Pour les volumes de données et de systèmes d’exploitation dans les groupes identiques Windows. La désactivation du chiffrement est prise en charge pour les volumes de données et de systèmes d’exploitation des groupes identiques Windows.
- Pour les volumes de données dans les groupes identiques Linux. Le chiffrement de disque de systèmes d’exploitation n’est PAS pris en charge dans la préversion actuelle pour les groupes identiques Linux.


## <a name="prerequisites"></a>Prérequis

Cet article nécessite l’utilisation du module Azure PowerShell version 5.3.0 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Inscrivez votre abonnement Azure pour la préversion du chiffrement de disque pour les groupes de machines virtuelles identiques à l’aide de la commande [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) : 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Attendez environ 10 minutes jusqu’à ce que l’état *Registered* soit retourné par [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature), puis réinscrivez le fournisseur `Microsoft.Compute` : 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```


## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Créer un coffre de clés Azure activé pour le chiffrement de disque
Un coffre de clés Azure peut stocker des clés, des clés secrètes ou des mots de passe vous permettant de les implémenter en toute sécurité dans vos applications et services. Les clés de chiffrement sont stockées dans le coffre de clés Azure à l’aide d’une protection logicielle, mais vous pouvez importer ou générer vos clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Ces clés de chiffrement servent à chiffrer et à déchiffrer les disques virtuels connectés à votre machine virtuelle. Vous gardez le contrôle de ces clés de chiffrement et pouvez effectuer un audit de leur utilisation.

Créez un coffre de clés avec [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Pour autoriser l’utilisation du coffre de clés pour le chiffrement de disque, définissez le paramètre *EnabledForDiskEncryption*. L’exemple suivant définit également les variables pour le nom du groupe de ressources, le nom du coffre de clés et l’emplacement. Indiquez un nom unique pour votre coffre de clés :

```powershell
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```


### <a name="use-an-existing-key-vault"></a>Utiliser un coffre de clés existant
Cette étape est requise uniquement si vous souhaitez utiliser un coffre de clés existant avec le chiffrement de disque. Ignorez cette étape si vous avez créé un coffre de clés dans la section précédente.

Vous pouvez activer un coffre de clés existant dans le même abonnement et la même région que le groupe identique pour le chiffrement de disque avec [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). Définissez le nom du coffre de clés existant dans la variable *$vaultName*, comme ceci :

```powershell
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```


## <a name="create-a-scale-set"></a>Créer un groupe identique
Tout d’abord, définissez un nom d’utilisateur administrateur et un mot de passe pour les instances de machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```powershell
$cred = Get-Credential
```

À présent, créez un groupe de machines virtuelles identiques avec [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Pour distribuer le trafic aux différentes instances de machine virtuelle, un équilibreur de charge est également créé. L’équilibreur de charge inclut des règles pour distribuer le trafic sur le port TCP 80, ainsi que pour autoriser le trafic Bureau à distance sur le port TCP 3389 et le trafic Accès distant PowerShell sur le port TCP 5985 :

```powershell
$vmssName="myScaleSet"

New-AzureRmVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```


## <a name="enable-encryption"></a>Activer le chiffrement
Pour chiffrer des instances de machine virtuelle dans un groupe identique, vous devez d’abord obtenir certaines informations sur l’URI et l’ID de ressources du coffre de clés avec [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault). Les variables obtenues sont ensuite utilisées pour démarrer le processus de chiffrement avec [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension) :

```powershell
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Quand vous y êtes invité, tapez *y* pour continuer le processus de chiffrement de disque sur les instances de machine virtuelle dans le groupe identique.


## <a name="check-encryption-progress"></a>Vérifier la progression du chiffrement
Pour vérifier l’état du chiffrement de disque, utilisez [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption) :

```powershell
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Une fois que les instances de machine virtuelle sont chiffrées, la variable *EncryptionSummary* retourne *ProvisioningState/succeeded* comme dans l’exemple de sortie suivant :

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```


## <a name="disable-encryption"></a>Désactiver le chiffrement
Si vous ne souhaitez plus utiliser les disques d’instances de machine virtuelle chiffrés, désactivez le chiffrement avec [Disable-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption), comme ceci :

```powershell
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```


## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez utilisé Azure PowerShell pour chiffrer un groupe de machines virtuelles identiques. Vous pouvez également utiliser [Azure CLI 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) ou des modèles pour [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) ou [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
