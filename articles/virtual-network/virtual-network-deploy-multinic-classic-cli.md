---
title: "Création d’une machine virtuelle (Classic) avec plusieurs cartes réseau - Azure CLI 1.0 | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle (Classic) avec plusieurs cartes réseau à l’aide de l’interface Azure CLI 1.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5569209d3628003b3f3e169b227e069b920c03f
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-cli-10"></a>Créer une machine virtuelle (Classic) avec plusieurs cartes réseau en utilisant Azure CLI 1.0

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Vous pouvez créer des machines virtuelles (VM) dans Azure et joindre plusieurs cartes d’interface réseau (NIC) à chacune d’elles. Plusieurs cartes réseau permettent la séparation des types de trafic entre les cartes réseau. Par exemple, une carte réseau peut communiquer avec Internet, tandis qu’une autre communique uniquement avec des ressources internes non connectées à Internet. La possibilité de séparer le trafic réseau entre plusieurs cartes réseau est requise pour de nombreuses appliances virtuelles réseau, telles que Application Delivery Network et les solutions d’optimisation WAN.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment effectuer ces étapes à l’aide du [modèle de déploiement Resource Manager](../virtual-machines/linux/multiple-nics.md).

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Les étapes suivantes utilisent un groupe de ressources nommé *IaaSStory* pour les serveurs web et un groupe de ressources nommé *IaaSStory-BackEnd* pour les serveurs de base de données.

## <a name="prerequisites"></a>configuration requise
Avant de créer les serveurs de base de données, vous devez créer le groupe de ressources *IaaSStory* avec toutes les ressources nécessaires pour ce scénario. Pour créer ces ressources, exécutez les étapes suivantes. Pour créer un réseau virtuel, suivez les étapes de l’article [Créer un réseau virtuel](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Déployer les machines virtuelles principales
Les machines virtuelles principales dépendent de la création des ressources suivantes :

* **Compte de stockage pour les disques de données**. Pour optimiser les performances, les disques de données sur les serveurs de base de données utilisent la technologie de disque SSD, qui requiert un compte de stockage Premium. Assurez-vous que l’emplacement Azure de déploiement prend en charge le stockage Premium.
* **Cartes réseau**. Chaque machine virtuelle a deux cartes réseau, une pour l’accès à la base de données et l’autre pour la gestion.
* **Groupe à haute disponibilité**. Tous les serveurs de base de données sont ajoutés à un groupe à haute disponibilité, afin de garantir qu’au moins une des machines virtuelles est en cours d’exécution lors de la maintenance.

### <a name="step-1---start-your-script"></a>Étape 1 : démarrer votre script
Vous pouvez télécharger le script d'interpréteur de commandes complet utilisé [ici](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Exécutez les étapes suivantes pour adapter le script à votre environnement :

1. Modifiez les valeurs des variables suivantes selon votre groupe de ressources existant déployé ci-dessus dans les [Conditions préalables](#Prerequisites).

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. Modifiez les valeurs des variables suivantes en fonction des valeurs que vous souhaitez utiliser pour le déploiement de votre serveur principal.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Étape 2 : création des ressources nécessaires pour vos machines virtuelles
1. Créez un service cloud pour toutes les machines virtuelles principales. Notez l'utilisation de la variable `$backendCSName` pour le nom du groupe de ressources, et `$location` pour la région Azure.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. Créez un compte de stockage Premium pour les disques du système d'exploitation et les disques de données à utiliser par vos machines virtuelles.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>Étape 3 : création de machines virtuelles avec plusieurs cartes d’interface réseau
1. Lancez une boucle pour créer plusieurs machines virtuelles, selon les variables `numberOfVMs` .

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. Pour chaque machine virtuelle, spécifiez le nom et l'adresse IP de chacune des deux cartes réseau.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. Créez la machine virtuelle. Notez l'utilisation du paramètre `--nic-config` contenant une liste de toutes les cartes réseau avec adresse IP, sous-réseau et nom.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. Pour chaque machine virtuelle, créez deux disques de données.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>Étape 4 : exécution du script
Maintenant que vous avez téléchargé et modifié le script selon vos besoins, exécutez le script pour créer les machines virtuelles principales de base de données avec plusieurs cartes réseau.

1. Enregistrez votre script et exécutez-le à partir de votre terminal **Bash** . Vous verrez la sortie initiale, comme illustré ci-dessous.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Après quelques minutes, l'exécution s'arrête et le reste de la sortie s'affiche, comme illustré ci-dessous.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>Étape 5 : configuration du routage au sein du système d’exploitation de la machine virtuelle

Le serveur DHCP Azure affecte une passerelle par défaut à la première (principale) interface réseau associée à la machine virtuelle. Azure n’affecte pas de passerelle par défaut aux interfaces réseau additionnelles (secondaires) associées à la machine virtuelle. Par conséquent, vous ne pouvez pas communiquer avec les ressources hors du sous-réseau dans lequel se trouve, par défaut, une interface réseau secondaire. Toutefois, les interfaces réseau secondaires peuvent communiquer avec des ressources hors de leur sous-réseau. Pour configurer le routage des interfaces réseau secondaires, consultez [Routage au sein du système d’exploitation d’une machine virtuelle avec plusieurs interfaces réseau](virtual-network-network-interface-vm.md).
