---
title: "Sécuriser IIS à l’aide de certificats SSL dans Azure | Microsoft Docs"
description: "Découvrez comment sécuriser le serveur web IIS à l’aide de certificats SSL sur une machine virtuelle Windows dans Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ada0703603df5ae5a324d38cda2b23a060a10992
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="secure-iis-web-server-with-ssl-certificates-on-a-windows-virtual-machine-in-azure"></a>Sécuriser le serveur web IIS à l’aide de certificats SSL sur une machine virtuelle Windows dans Azure
Pour sécuriser les serveurs web, vous pouvez utiliser un certificat SSL (Secure Sockets Layer) et chiffrer ainsi le trafic web. Ces certificats SSL peuvent être stockés dans Azure Key Vault et autoriser les déploiements sécurisés de certificats sur les machines virtuelles Windows dans Azure. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un Azure Key Vault
> * Générer ou télécharger un certificat dans Key Vault
> * Créer une machine virtuelle et installer le serveur web IIS
> * Injecter le certificat dans la machine virtuelle et configurer IIS à l’aide d’une liaison SSL

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.3 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 


## <a name="overview"></a>Vue d'ensemble
Azure Key Vault protège les clés de chiffrement et les secrets, tels que les certificats ou les mots de passe. Key Vault rationalise le processus de gestion de certificats et vous permet de garder le contrôle sur les clés d’accès à ces certificats. Vous pouvez créer un certificat auto-signé à l’intérieur de Key Vault ou charger un certificat approuvé existant que vous avez déjà.

Au lieu d’utiliser une image de machine virtuelle personnalisée qui inclut des certificats intégrés, vous injectez des certificats dans une machine virtuelle en cours d’exécution. Ce processus garantit que les certificats les plus récents sont installés sur un serveur web pendant le déploiement. Si vous renouvelez ou remplacez un certificat, vous n’êtes pas non plus obligé de créer une image de machine virtuelle personnalisée. Les certificats les plus récents sont automatiquement injectés à la création des machines virtuelles supplémentaires. Pendant tout le processus, les certificats ne quittent jamais la plateforme Azure, ni ne sont exposés dans un script, un historique de ligne de commande ou un modèle.


## <a name="create-an-azure-key-vault"></a>Créer un Azure Key Vault
Avant de créer un coffre de clés et des certificats, créez un groupe de ressources avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé *myResourceGroupSecureWeb* à l’emplacement *East US* :

```azurepowershell-interactive
$resourceGroup = "myResourceGroupSecureWeb"
$location = "East US"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

Ensuite, créez un coffre de clés avec [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Chaque Key Vault requiert un nom unique en minuscules. Remplacez `mykeyvault` dans l’exemple suivant par le nom unique de votre propre Key Vault :

```azurepowershell-interactive
$keyvaultName="mykeyvault"
New-AzureRmKeyVault -VaultName $keyvaultName `
    -ResourceGroup $resourceGroup `
    -Location $location `
    -EnabledForDeployment
```

## <a name="generate-a-certificate-and-store-in-key-vault"></a>Générer un certificat et le stocker dans Key Vault
Dans un environnement de production, vous devez importer un certificat valide, signé par un fournisseur approuvé, à l’aide de la commande [Import-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate). Pour ce didacticiel, l’exemple suivant vous montre comment générer un certificat auto-signé avec la commande [Add-AzureKeyVaultCertificate](/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificate) qui utilise la stratégie de certificat par défaut à partir de [New-AzureKeyVaultCertificatePolicy](/powershell/module/azurerm.keyvault/new-azurekeyvaultcertificatepolicy). 

```azurepowershell-interactive
$policy = New-AzureKeyVaultCertificatePolicy `
    -SubjectName "CN=www.contoso.com" `
    -SecretContentType "application/x-pkcs12" `
    -IssuerName Self `
    -ValidityInMonths 12

Add-AzureKeyVaultCertificate `
    -VaultName $keyvaultName `
    -Name "mycert" `
    -CertificatePolicy $policy 
```


## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle
Définissez un nom d’utilisateur administrateur et un mot de passe pour la machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

Vous pouvez maintenant créer la machine virtuelle avec [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). L’exemple suivant permet de créer une machine virtuelle nommée *myVM* dans l’emplacement *EastUS*. Si elles n’existent pas déjà, les ressources réseau requises sont créées. Pour autoriser le trafic web sécurisé, le cmdlet ouvre également le port *443*.

```azurepowershell-interactive
# Create a VM
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred `
    -OpenPorts 443

# Use the Custom Script Extension to install IIS
Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server -IncludeManagementTools"}'
```

Il faut quelques minutes pour que la machine virtuelle soit créée. La dernière étape utilise l’extension de script personnalisé Azure pour installer le serveur web IIS avec [Set-AzureRmVmExtension](/powershell/module/azurerm.compute/set-azurermvmextension).


## <a name="add-a-certificate-to-vm-from-key-vault"></a>Ajouter un certificat à la machine virtuelle à partir de Key Vault
Pour ajouter le certificat à partir de Key Vault sur une machine virtuelle, obtenez l’ID du certificat avec [Get-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret). Ajoutez le certificat à la machine virtuelle avec [Add-AzureRmVMSecret](/powershell/module/azurerm.compute/add-azurermvmsecret) :

```azurepowershell-interactive
$certURL=(Get-AzureKeyVaultSecret -VaultName $keyvaultName -Name "mycert").id

$vm=Get-AzureRMVM -ResourceGroupName $resourceGroup -Name "myVM"
$vaultId=(Get-AzureRmKeyVault -ResourceGroupName $resourceGroup -VaultName $keyVaultName).ResourceId
$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $vaultId -CertificateStore "My" -CertificateUrl $certURL

Update-AzureRmVM -ResourceGroupName $resourceGroup -VM $vm
```


## <a name="configure-iis-to-use-the-certificate"></a>Configurer IIS pour utiliser le certificat
Utilisez de nouveau l’extension de script personnalisé à l’aide de [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) pour mettre à jour la configuration d’IIS. Cette mise à jour applique le certificat injecté à partir de Key Vault dans IIS et configure la liaison web :

```azurepowershell-interactive
$PublicSettings = '{
    "fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/secure-iis.ps1"],
    "commandToExecute":"powershell -ExecutionPolicy Unrestricted -File secure-iis.ps1"
}'

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName "IIS" `
    -VMName "myVM" `
    -Location $location `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -SettingString $publicSettings
```


### <a name="test-the-secure-web-app"></a>Tester l’application web sécurisée
Obtenez l’adresse IP publique de votre machine virtuelle avec [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). L’exemple suivant obtient l’adresse IP de `myPublicIP` créée précédemment :

```azurepowershell-interactive
Get-AzureRmPublicIPAddress -ResourceGroupName $resourceGroup -Name "myPublicIPAddress" | select "IpAddress"
```

Vous pouvez maintenant ouvrir un navigateur web et entrer `https://<myPublicIP>` dans la barre d’adresse. Pour accepter l’avertissement de sécurité si vous avez utilisé un certificat auto-signé, sélectionnez **Détails**, puis **Atteindre la page web** :

![Accepter l’avertissement de sécurité du navigateur web](./media/tutorial-secure-web-server/browser-warning.png)

Votre site IIS sécurisé apparaît maintenant comme dans l’exemple suivant :

![Afficher le site IIS sécurisé en cours d’exécution](./media/tutorial-secure-web-server/secured-iis.png)


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez sécurisé un serveur web IIS à l’aide d’un certificat SSL stocké dans Azure Key Vault. Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un Azure Key Vault
> * Générer ou télécharger un certificat dans Key Vault
> * Créer une machine virtuelle et installer le serveur web IIS
> * Injecter le certificat dans la machine virtuelle et configurer IIS à l’aide d’une liaison SSL

Suivez ce lien pour consulter des exemples de scripts de machine virtuelle prédéfinis.

> [!div class="nextstepaction"]
> [Exemples de scripts de machine virtuelle Windows](./powershell-samples.md)
