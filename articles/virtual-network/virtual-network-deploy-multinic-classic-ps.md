---
title: "Créer une machine virtuelle (Classic) avec plusieurs cartes réseau - Azure PowerShell | Microsoft Docs"
description: "Apprenez à créer une machine virtuelle (Classic) avec plusieurs cartes réseau à l’aide de PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-service-management
ms.assetid: 6e50f39a-2497-4845-a5d4-7332dbc203c5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 824af3f0f5f6d42341f6f1dc94104f6009b568b8
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-powershell"></a>Créer une machine virtuelle (classique) avec plusieurs cartes réseau à l’aide de PowerShell

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Vous pouvez créer des machines virtuelles (VM) dans Azure et joindre plusieurs cartes d’interface réseau (NIC) à chacune d’elles. Plusieurs cartes réseau permettent la séparation des types de trafic entre les cartes réseau. Par exemple, une carte réseau peut communiquer avec Internet, tandis qu’une autre communique uniquement avec des ressources internes non connectées à Internet. La possibilité de séparer le trafic réseau entre plusieurs cartes réseau est requise pour de nombreuses appliances virtuelles réseau, telles que Application Delivery Network et les solutions d’optimisation WAN.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment effectuer ces étapes à l’aide du [modèle de déploiement Resource Manager](../virtual-machines/windows/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Les étapes suivantes utilisent un groupe de ressources nommé *IaaSStory* pour les serveurs web et un groupe de ressources nommé *IaaSStory-BackEnd* pour les serveurs de base de données.

## <a name="prerequisites"></a>configuration requise

Avant de créer les serveurs de base de données, vous devez créer le groupe de ressources *IaaSStory* avec toutes les ressources nécessaires pour ce scénario. Pour créer ces ressources, exécutez les étapes suivantes. Pour créer un réseau virtuel, suivez les étapes de l’article [Créer un réseau virtuel](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-back-end-vms"></a>Créer les machines virtuelles principales
Les machines virtuelles principales dépendent de la création des ressources suivantes :

* **Sous-réseau principal**. Les serveurs de base de données font partie d'un sous-réseau distinct, pour répartir le trafic. Le script ci-dessous s'attend à ce que le sous-réseau se trouve dans un réseau virtuel nommé *WTestVnet*.
* **Compte de stockage pour les disques de données**. Pour optimiser les performances, les disques de données sur les serveurs de base de données utilisent la technologie de disque SSD, qui requiert un compte de stockage Premium. Assurez-vous que l'emplacement Azure de déploiement prend en charge le stockage Premium.
* **Groupe à haute disponibilité**. Tous les serveurs de base de données sont ajoutés à un groupe à haute disponibilité, afin de garantir qu’au moins une des machines virtuelles est en cours d’exécution lors de la maintenance.

### <a name="step-1---start-your-script"></a>Étape 1 : démarrer votre script
Vous pouvez télécharger le script PowerShell complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Suivez les étapes ci-dessous pour modifier le script afin qu’il fonctionne dans votre environnement.

1. Modifiez les valeurs des variables suivantes selon votre groupe de ressources existant déployé ci-dessus dans les [Conditions préalables](#Prerequisites).

    ```powershell
    $location              = "West US"
    $vnetName              = "WTestVNet"
    $backendSubnetName     = "BackEnd"
    ```

2. Modifiez les valeurs des variables suivantes en fonction des valeurs que vous souhaitez utiliser pour le déploiement de votre serveur principal.

    ```powershell
    $backendCSName         = "IaaSStory-Backend"
    $prmStorageAccountName = "iaasstoryprmstorage"
    $avSetName             = "ASDB"
    $vmSize                = "Standard_DS3"
    $diskSize              = 127
    $vmNamePrefix          = "DB"
    $dataDiskSuffix        = "datadisk"
    $ipAddressPrefix       = "192.168.2."
    $numberOfVMs           = 2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Étape 2 : création des ressources nécessaires pour vos machines virtuelles
Vous devez créer un service cloud et un compte de stockage pour les disques de données pour toutes les machines virtuelles. Vous devez également spécifier une image et un compte d'administrateur local pour les machines virtuelles. Pour créer ces ressources, exécutez les étapes suivantes :

1. Créez un nouveau service cloud.

    ```powershell
    New-AzureService -ServiceName $backendCSName -Location $location
    ```

2. Créez un compte de stockage Premium.

    ```powershell
    New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
    -Location $location -Type Premium_LRS
    ```
3. Définissez le compte de stockage créé ci-dessus en tant que compte de stockage actif pour votre abonnement.

    ```powershell
    $subscription = Get-AzureSubscription | where {$_.IsCurrent -eq $true}  
    Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
    -CurrentStorageAccountName $prmStorageAccountName
    ```

4. Sélectionnez une image de machine virtuelle.

    ```powershell
    $image = Get-AzureVMImage `
    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
    | sort PublishedDate -Descending `
    | select -ExpandProperty ImageName -First 1
    ```

5. Définissez les informations d’identification de compte d’administrateur local.

    ```powershell
    $cred = Get-Credential -Message "Enter username and password for local admin account"
    ```

### <a name="step-3---create-vms"></a>Étape 3 : création de machines virtuelles
Vous devez utiliser une boucle pour créer le nombre de machines virtuelles que vous voulez et créer les cartes réseau et les machines virtuelles nécessaires au sein de la boucle. Pour créer ces cartes réseau et ces machines virtuelles, exécutez les étapes suivantes.

1. Lancez une boucle `for` pour répéter les commandes afin de créer une machine virtuelle et deux cartes réseau, autant de fois que nécessaire, selon la valeur de la variable `$numberOfVMs`.

    ```powershell
    for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){
    ```

2. Créez un objet `VMConfig` spécifiant l'image, la taille et la haute disponibilité de la machine virtuelle.

    ```powershell
    $vmName = $vmNamePrefix + $suffixNumber
    $vmConfig = New-AzureVMConfig -Name $vmName `
        -ImageName $image `
        -InstanceSize $vmSize `
        -AvailabilitySetName $avSetName
    ```

3. Configurez la machine virtuelle en tant que machine virtuelle Windows.

    ```powershell
    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
        -AdminUsername $cred.UserName `
        -Password $cred.GetNetworkCredential().Password
    ```

4. Définissez la carte réseau par défaut et attribuez-lui une adresse IP statique.

    ```powershell
    Set-AzureSubnet         -SubnetNames $backendSubnetName -VM $vmConfig
    Set-AzureStaticVNetIP   -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig
    ```

5. Ajoutez une deuxième carte réseau pour chaque machine virtuelle.

    ```powershell
    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
    -SubnetName $backendSubnetName `
    -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
    -VM $vmConfig
    ```
    
6. Pour chaque machine virtuelle, créez deux disques de données.

    ```powershell
    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk1Name `
    -LUN 0

    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
    Add-AzureDataDisk -CreateNew -VM $vmConfig `
    -DiskSizeInGB $diskSize `
    -DiskLabel $dataDisk2Name `
    -LUN 1
    ```

7. Créez toutes les machines virtuelles et mettez fin à la boucle.

    ```powershell
    New-AzureVM -VM $vmConfig `
    -ServiceName $backendCSName `
    -Location $location `
    -VNetName $vnetName
    }
    ```

### <a name="step-4---run-the-script"></a>Étape 4 : exécution du script
Maintenant que vous avez téléchargé et modifié le script selon vos besoins, exécutez ce script pour créer les machines virtuelles de base de données principales avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de l’invite de commandes **PowerShell** ou **PowerShell ISE**. Vous verrez la sortie initiale, comme illustré ci-dessous.

        OperationDescription    OperationId                          OperationStatus

        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        
        WARNING: No deployment found in service: 'IaaSStory-Backend'.
2. Complétez les informations nécessaires dans l'invite d'informations d'identification et cliquez sur **OK**. Voici les résultats renvoyés.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Étape 5 : configuration du routage au sein du système d’exploitation de la machine virtuelle

Le serveur DHCP Azure affecte une passerelle par défaut à la première (principale) interface réseau associée à la machine virtuelle. Azure n’affecte pas de passerelle par défaut aux interfaces réseau additionnelles (secondaires) associées à la machine virtuelle. Par conséquent, vous ne pouvez pas communiquer avec les ressources hors du sous-réseau dans lequel se trouve, par défaut, une interface réseau secondaire. Toutefois, les interfaces réseau secondaires peuvent communiquer avec des ressources hors de leur sous-réseau. Pour configurer le routage des interfaces réseau secondaires, consultez [Routage au sein du système d’exploitation d’une machine virtuelle avec plusieurs interfaces réseau](virtual-network-network-interface-vm.md).
