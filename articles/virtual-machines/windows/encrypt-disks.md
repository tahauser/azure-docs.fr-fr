---
title: Chiffrer des disques sur une machine virtuelle Windows dans Azure | Microsoft Docs
description: Guide de chiffrage de disques virtuels sur une machine virtuelle Windows pour renforcer la sécurité à l’aide d’Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/07/2018
ms.author: iainfou
ms.openlocfilehash: 4f21e457b266fdd0106992dad29578eef6e89144
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Guide de chiffrage de disques virtuels sur une machine virtuelle Windows
Pour renforcer la sécurité et la conformité de la machine virtuelle, les disques virtuels dans Azure peuvent être chiffrés. Les disques sont chiffrés à l’aide de clés de chiffrement sécurisées dans un coffre de clés Azure. Vous contrôlez ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Cet article explique comment chiffrer des disques virtuels sur une machine virtuelle Windows à l’aide de l’interface Azure PowerShell. Vous pouvez également [chiffrer une machine virtuelle Linux à l’aide d’Azure CLI 2.0](../linux/encrypt-disks.md).

## <a name="overview-of-disk-encryption"></a>Présentation du chiffrement de disque
Les disques virtuels sur des machines virtuelles Windows sont chiffrés au repos à l’aide de Bitlocker. Le chiffrement de disques virtuels dans Azure n’entraîne aucun frais. Les clés de chiffrement sont stockées dans le coffre de clés Azure à l’aide d’une protection logicielle, mais vous pouvez importer ou générer vos clés dans des modules de sécurité matériels (HSM) certifiés conformes aux normes FIPS 140-2 de niveau 2. Ces clés de chiffrement servent à chiffrer et à déchiffrer les disques virtuels connectés à votre machine virtuelle. Vous gardez le contrôle de ces clés de chiffrement et pouvez effectuer un audit de leur utilisation. Un principal de service Azure Active Directory fournit un mécanisme sécurisé pour l’émission de ces clés de chiffrement lors de la mise sous tension et hors tension des machines virtuelles.

Le processus de chiffrement d’une machine virtuelle est le suivant :

1. Créez une clé de chiffrement dans un coffre de clés Azure.
2. Configurez la clé de chiffrement afin de la rendre utilisable pour le chiffrement des disques.
3. Pour lire la clé de chiffrement à partir du coffre de clés Azure, créez un principal de service Azure Active Directory avec les autorisations appropriées.
4. Exécutez la commande pour chiffrer vos disques virtuels, en spécifiant le principal de service Azure Active Directory et la clé de chiffrement appropriée à utiliser.
5. Le principal de service Azure Active Directory demande la clé de chiffrement requise au coffre de clés Azure.
6. Les disques virtuels sont chiffrés à l’aide de la clé de chiffrement fournie.

## <a name="encryption-process"></a>Processus de chiffrement
Le chiffrement de disque s’appuie sur les composants supplémentaires suivants :

* **Coffre de clés Azure** - permet de sauvegarder les clés de chiffrement et les clés secrètes utilisées pour le processus de chiffrement/déchiffrement de disque. 
  * Le cas échéant, vous pouvez utiliser un coffre de clés Azure existant. Vous n’êtes pas obligé de dédier un coffre de clés au chiffrement des disques.
  * Pour séparer les limites administratives et la visibilité de la clé, vous pouvez créer un coffre de clés dédié.
* **Azure Active Directory** - gère l’échange sécurisé des clés de chiffrement requises et l’authentification des actions demandées. 
  * Vous pouvez généralement utiliser une instance Azure Active Directory existante pour héberger votre application.
  * Le principal du service fournit un mécanisme sécurisé pour demander et émettre les bonnes clés de chiffrement. Vous ne développez pas de réelle application qui s’intègre à Azure Active Directory.

## <a name="requirements-and-limitations"></a>Spécifications et limitations
Configuration requise et scénarios pris en charge pour le chiffrement de disque :

* Activation du chiffrement sur les nouvelles machines virtuelles Windows à partir d’images Azure Marketplace ou d’une image de disque dur virtuel personnalisée.
* Activation du chiffrement sur les machines virtuelles Windows existantes dans Azure.
* Activation du chiffrement sur les machines virtuelles Windows configurées à l’aide d’espaces de stockage.
* Désactivation du chiffrement sur les systèmes d’exploitation et les lecteurs de données pour les machines virtuelles Windows.
* Toutes les ressources (par exemple, le coffre de clés, le compte de stockage, la machine virtuelle, etc.) doivent appartenir à la même région et au même abonnement Azure.
* Machines virtuelles de niveau standard (A, D, DS, G, GS et GS, par exemple).

Le chiffrement de disque n’est actuellement pas pris en charge dans les scénarios suivants :

* Machines virtuelles de niveau de base.
* Machines virtuelles créées à l’aide du modèle de déploiement Classic.
* Mise à jour des clés de chiffrement sur une machine virtuelle déjà chiffrée.
* Intégration au service de gestion de clés local.

## <a name="create-azure-key-vault-and-keys"></a>Créer le coffre de clés Azure et les clés
Avant de commencer, assurez-vous que la dernière version du module Azure PowerShell est installée. Pour plus d’informations, consultez [Installer et configurer Azure PowerShell](/powershell/azure/overview). Dans les exemples de commandes, remplacez tous les exemples de paramètres par vos propres noms, emplacement et valeurs de clés. Les exemples suivants utilisent une convention de *myResourceGroup*, *myKeyVault*, *myVM*, etc.

La première étape consiste à créer un coffre de clés Azure pour y stocker les clés de chiffrement. Un coffre de clés Azure peut stocker des clés, des clés secrètes ou des mots de passe vous permettant de les implémenter en toute sécurité dans vos applications et services. Pour le chiffrement de disque virtuel, vous créez un coffre de clés afin de stocker une clé de chiffrement pour chiffrer ou déchiffrer vos disques virtuels. 

Activez le fournisseur Azure Key Vault au sein de votre abonnement Azure avec [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) puis créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *États-Unis de l'Est* :

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

Le coffre de clés Azure contenant les clés de chiffrement et les ressources de calcul associées tels que le stockage et la machine virtuelle elle-même doit résider dans la même région. Créez un coffre Azure Key Vault avec [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) et activez Key Vault pour une utilisation avec le chiffrement de disque. Spécifiez un nom de Key Vault unique pour *keyVaultName* comme suit :

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

Vous pouvez stocker des clés de chiffrement à l’aide d’une protection logicielle ou HSM (modèle de sécurité matériel). L’utilisation d’une protection HSM nécessite un coffre de clés Premium. Contrairement à l’utilisation d’un coffre de clés standard qui stocke les clés protégées par logiciel, la création d’un coffre de clés Premium entraîne des frais. Pour créer un coffre Key Vault Premium, ajoutez les paramètres *-Sku "Premium"* à l’étape précédente. L’exemple suivant utilise des clés protégées par logiciel puisque nous avons créé un coffre de clés standard. 

Pour les deux modèles de protection, la plateforme Azure doit être autorisée à demander les clés de chiffrement lorsque la machine virtuelle démarre pour déchiffrer les disques virtuels. Créez une clé de chiffrement dans le coffre Key Vault avec [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey). L’exemple qui suit permet de créer une clé nommée *myKey* :

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Création d’un principal de service Azure Active Directory
Lorsque des disques virtuels sont chiffrés ou déchiffrés, vous spécifiez un compte pour gérer l’authentification et l’échange de clés de chiffrement à partir du coffre de clés. Ce compte, un principal de service Azure Active Directory, permet à la plateforme Azure de demander les clés de chiffrement appropriées pour le compte de la machine virtuelle. Une instance Azure Active Directory par défaut est disponible dans votre abonnement, bien que de nombreuses organisations utilisent des répertoires Azure Active Directory dédiés.

Créez un principal du service dans Azure Active Directory avec [New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal). Pour spécifier un mot de passe sécurisé, consultez [Stratégies et restrictions de mot de passe dans Azure Active Directory](../../active-directory/active-directory-passwords-policy.md) :

```powershell
$appName = "My App"
$securePassword = ConvertTo-SecureString -String "P@ssw0rd!" -AsPlainText -Force
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

Pour chiffrer ou déchiffrer correctement des disques virtuels, des autorisations d’accès à la clé de chiffrement stockée dans le coffre de clés doivent être définies afin que le principal de service Azure Active Directory puisse lire les clés. Définissez des autorisations sur votre Key Vault avec [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) :

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>Créer une machine virtuelle
Pour tester le processus de chiffrement, créez une machine virtuelle avec la commande [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). L’exemple qui suit permet de créer une machine virtuelle nommée *myVM* en utilisant une image *Windows Server 2016 Datacenter*. Lorsque vous êtes invité à fournir vos informations d’identification, entrez un nom d’utilisateur et un mot de passe à utiliser pour votre machine virtuelle :

```powershell
$cred = Get-Credential

New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-virtual-machine"></a>Chiffrement d’une machine virtuelle
Pour chiffrer les disques virtuels, vous allez réunir tous les composants précédents :

1. Spécifiez le principal de service Azure Active Directory et le mot de passe.
2. Spécifiez le coffre de clés qui stockera les métadonnées de vos disques chiffrés.
3. Spécifiez les clés de chiffrement à utiliser pour le chiffrement et le déchiffrement réels.
4. Indiquez si vous souhaitez chiffrer le disque du système d’exploitation, les disques de données ou l’ensemble.

Chiffrez votre machine virtuelle avec [Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) à l’aide de la clé Azure Key Vault et des informations d’identification du principal de service Azure Active Directory. L’exemple qui suit permet de récupérer toutes les informations sur la clé, puis de chiffrer la machine virtuelle nommée *myVM* :

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -AadClientID $app.ApplicationId `
    -AadClientSecret (New-Object PSCredential "user",$securePassword).GetNetworkCredential().Password `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Acceptez l’invite pour poursuivre le chiffrement de la machine virtuelle. La machine virtuelle redémarre pendant le processus. Une fois le processus de chiffrement terminé et la machine virtuelle redémarrée, vérifiez l’état du chiffrement avec [Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) :

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

Le résultat ressemble à l’exemple suivant :

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d’informations sur la gestion d’Azure Key Vault, consultez [Configuration de Key Vault pour des machines virtuelles](key-vault-setup.md).
* Pour plus d’informations sur le chiffrement de disque, notamment la préparation d’une machine virtuelle personnalisée chiffrée à télécharger vers Azure, consultez [Chiffrement de disque Azure](../../security/azure-security-disk-encryption.md).
