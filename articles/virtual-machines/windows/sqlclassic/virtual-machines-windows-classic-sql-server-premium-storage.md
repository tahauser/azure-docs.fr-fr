---
title: "Utilisation de Stockage Premium Azure avec SQL Server | Microsoft Docs"
description: "Cet article utilise des ressources créées avec le modèle de déploiement classique et fournit des conseils sur l'utilisation du stockage d’Azure Premium Storage avec SQL Server s'exécutant sur des machines virtuelles Azure."
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: jroth
ms.openlocfilehash: f637e3c744d61f6fda755c162609d7cc9f4619c7
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Utilisation du stockage Premium Azure avec SQL Server sur des machines virtuelles
## <a name="overview"></a>Vue d’ensemble
[stockage Premium Azure](../premium-storage.md) est la nouvelle génération de stockage qui offre une faible latence et un débit d'E/S élevé. Il est particulièrement adapté aux principales charges de travail E/S intensives telles que SQL Server sur [des machines virtuelles](https://azure.microsoft.com/services/virtual-machines/)IaaS.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Cet article fournit une planification et des conseils pour la migration d'une machine virtuelle exécutant SQL Server afin d'utiliser le stockage Premium. Cela inclut l'infrastructure Azure (mise en réseau, stockage) et les étapes de la machine virtuelle Windows hôte. L’exemple de l’ [annexe](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) montre une migration complète de bout en bout de machines virtuelles plus importantes afin de tirer parti du stockage SSD local amélioré avec PowerShell.

Il est important de comprendre le processus complet d'utilisation du stockage Premium Azure avec SQL Server sur des machines virtuelles IAAS, notamment :

* Identification de la configuration requise pour utiliser le stockage Premium.
* Exemples de déploiement de SQL Server sur IaaS vers un stockage Premium pour les nouveaux déploiements.
* Exemples de migration de déploiements existants, à la fois de serveurs autonomes et de déploiements à l’aide de groupes de disponibilité SQL Always On.
* Approches de migration possibles.
* Exemple complet présentant les étapes Azure, Windows et SQL Server pour la migration d’une implémentation Always On existante.

Pour plus de détails sur l’utilisation de SQL Server dans les machines virtuelles Azure, consultez la rubrique [SQL Server dans les machines virtuelles Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

**Auteur :** Daniel Sol **Réviseurs techniques :** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Configuration requise pour le stockage Premium
Il existe plusieurs conditions préalables à l'utilisation du stockage Premium.

### <a name="machine-size"></a>Taille de la machine
Pour utiliser le stockage Premium, vous devrez utiliser des machines virtuelles (VM) de série DS. Si vous n'avez jamais utilisé de machines de série DS dans votre service cloud, vous devez supprimer la machine virtuelle existante, conserver les disques connectés, puis créer un nouveau service cloud avant de recréer la machine virtuelle avec une taille de rôle DS*. Pour plus d’informations sur l’utilisation des tailles des machines virtuelles, consultez la rubrique [Tailles des machines virtuelles et des services cloud pour Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="cloud-services"></a>Services cloud
Vous pouvez uniquement utiliser des machines virtuelles DS* avec un stockage Premium si elles ont été créées dans un nouveau service cloud. Si vous utilisez SQL Server Always On dans Azure, l’écouteur Always On fera référence à l’adresse IP de l’équilibreur de charge interne (ILB) ou de l’équilibreur de charge externe (ELB) Azure associée à un service cloud. Cet article explique comment effectuer la migration tout en conservant la disponibilité dans ce scénario.

> [!NOTE]
> Une série DS* doit être la première machine virtuelle déployée sur le nouveau service cloud.
>
>

### <a name="regional-vnets"></a>Réseaux virtuels régionaux
Pour les machines virtuelles DS*, le réseau virtuel (VNET) qui héberge vos machines virtuelles doit être régional. Cela « étend » le réseau virtuel pour permettre de configurer des machines virtuelles plus importantes dans d'autres clusters et établir la communication entre elles. Dans la capture d'écran suivante, l'emplacement mis en surbrillance montre les réseaux virtuels régionaux, tandis que le premier résultat montre un réseau virtuel « étroit ».

![RegionalVNET][1]

Vous pouvez soumettre un ticket de support Microsoft pour migrer vers un réseau virtuel régional ; Microsoft apportera une modification, finalisera la migration vers les réseaux virtuels régionaux, puis modifiera la propriété AffinityGroup dans la configuration du réseau. Exportez d’abord la configuration du réseau dans PowerShell, puis remplacez la propriété **AffinityGroup** de l’élément **VirtualNetworkSite** par une propriété **Location**. Spécifiez `Location = XXXX` où `XXXX` est une région Azure. Puis importez la nouvelle configuration.

Par exemple, si l'on considère la configuration de réseau virtuel suivante :

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

Pour convertir cette configuration en un réseau virtuel régional en Europe de l'Ouest, modifiez la configuration comme suit :

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

### <a name="storage-accounts"></a>Comptes de stockage
Vous devrez créer un nouveau compte de stockage configuré pour le stockage Premium. Notez que l'utilisation du stockage Premium est définie au niveau du compte de stockage et non des disques durs virtuels individuels ; mais lorsque vous utilisez une machine virtuelle de série DS*, vous pouvez connecter des disques durs virtuels à partir de comptes de stockage Premium et Standard. Cette méthode est recommandée si vous ne souhaitez pas placer le disque dur virtuel du système d'exploitation sur le compte de stockage Premium.

La commande **New-AzureStorageAccountPowerShell** suivante avec le **Type** « Premium_LRS » crée un compte de stockage Premium :

    $newstorageaccountname = "danpremstor"
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### <a name="vhds-cache-settings"></a>Paramètres de cache des disques durs virtuels
La principale différence avec la création de disques faisant partie d'un compte de stockage Premium est le paramètre de cache des disques. Pour les disques de volume de données SQL Server, il est recommandé d’utiliser le paramètre de cache de lecture**Read Caching**. Pour les volumes de journaux de transactions, le paramètre de cache des disques doit être défini sur**None**. Il s'agit d'une différence par rapport aux recommandations pour les comptes de stockage Standard.

Une fois les disques durs virtuels connectés, le paramètre de cache ne peut pas être modifié. Vous devez déconnecter puis reconnecter le disque dur virtuel avec un paramètre de cache mis à jour.

### <a name="windows-storage-spaces"></a>Espaces de stockage Windows
Vous pouvez utiliser les [espaces de stockage Windows](https://technet.microsoft.com/library/hh831739.aspx) comme vous l'avez fait avec le système de stockage Standard précédent pour migrer une machine virtuelle qui utilise déjà des espaces de stockage. L'exemple de l' [annexe](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) (étape 9 et suivantes) montre le code Powershell pour extraire et importer une machine virtuelle comportant plusieurs disques durs virtuels connectés.

Des pools de stockage avaient été utilisés avec un compte de stockage Azure Standard pour améliorer le débit et de réduire la latence. Vous trouverez peut-être utile de tester des pools de stockage avec un stockage Premium pour les nouveaux déploiements, mais notez que ces tests compliquent la configuration du stockage.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>Identification des disques virtuels Azure à mapper aux pools de stockage
Comme il existe différents paramètres de cache pour les disques durs virtuels connectés, vous pouvez décider de copier les disques durs virtuels sur un compte de stockage Premium. Toutefois, lorsque vous les reconnectez à la nouvelle machine virtuelle de série DS, vous devrez peut-être modifier les paramètres du cache. Il est plus simple d'appliquer les paramètres de cache du stockage Premium recommandés si vous utilisez des disques durs virtuels distincts pour les fichiers de données SQL et les fichiers journaux (plutôt qu'un seul disque dur virtuel regroupant le tout).

> [!NOTE]
> Si vous avez des fichiers de données SQL Server et des fichiers journaux sur le même volume, l'option de mise en cache que vous choisissez dépend des modèles d'accès E/S pour vos charges de travail de base de données. Seuls des tests permettent d'identifier l'option de mise en cache la mieux adaptée à ce scénario.
>
>

Toutefois, si vous utilisez des espaces de stockage Windows composé de plusieurs disques durs virtuels, vous devrez examiner vos scripts d’origine pour identifier quels disques durs virtuels figurent dans un pool spécifique afin de définir ensuite les paramètres du cache pour chaque disque.

Si vous n'avez pas de script d'origine disponible pour afficher les disques durs virtuels mappés au pool de stockage, vous pouvez utiliser les étapes suivantes pour déterminer le mappage de pool de stockage/disque.

Pour chaque disque, procédez comme suit :

1. Affichez la liste des disques connectés à la machine virtuelle à l'aide de la commande **Get-AzureVM** :

    Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
2. Notez le nom du disque et le LUN.

    ![DisknameAndLUN][2]
3. Bureau à distance dans la machine virtuelle. Accédez ensuite à **Gestion de l’ordinateur** | **Gestionnaire de périphériques** | **Lecteurs de disque**. Examinez les propriétés de chacun  des « disques virtuels Microsoft ».

    ![VirtualDiskProperties][3]
4. Ici, le numéro LUN désigne le numéro LUN que vous spécifiez lors de la connexion du disque dur virtuel à la machine virtuelle.
5. Pour le « disque virtuel Microsoft », accédez à l’onglet **Détails**, puis dans la liste **Propriété**, accédez à **Clé pilote**. Dans la zone **Valeur**, notez le **décalage**, c’est-à-dire 0002 dans la capture d’écran suivante. La valeur 0002 indique l'élément PhysicalDisk2 auquel le pool de stockage fait référence.

    ![VirtualDiskPropertyDetails][4]
6. Pour chaque pool de stockage, videz les disques associés :

    Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]

Vous pouvez désormais utiliser ces informations pour associer des disques durs virtuels connectés aux disques physiques dans les pools de stockage.

Une fois que vous avez mappé les disques durs virtuels aux disques physiques dans les pools de stockage, vous pouvez les déconnecter et les copier vers un compte de stockage Premium, puis les connecter en utilisant le paramètre de cache approprié. Consultez les étapes 8 à 12 de l' [annexe](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). Ces étapes indiquent comment extraire dans un fichier CSV une configuration de disque de disque dur virtuel connecté à une machine virtuelle, copier les disques durs virtuels, modifier les paramètres de cache de la configuration de disque, puis redéployer la machine virtuelle comme machine virtuelle de série DS avec tous les disques connectés.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>Bande passante de stockage de la machine virtuelle et débit de stockage du disque dur virtuel
Les performances de stockage dépendent de la taille de la machine virtuelle DS* spécifiée et des tailles des disques durs virtuels. Les machines virtuelles offrent différentes tolérances pour le nombre de disques durs virtuels peuvent être connectés et la bande passante maximale prise en charge (Mo/s). Pour connaître les numéros de bande passante spécifiques, consultez la rubrique [Tailles des machines virtuelles et des services cloud pour Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Les disques de plus grande taille augmentent le nombre d'opérations d'E/S par seconde. Vous devez en tenir compte lorsque vous étudiez votre chemin de migration. Pour plus d’informations, [consultez le tableau des opérations d’E/S et des types de disque](../premium-storage.md#scalability-and-performance-targets).

Enfin, notez que les machines virtuelles prennent en charge différentes bandes passantes maximales pour tous les disques connectés. Sous une charge élevée, vous risquez de saturer la bande passante de disque maximale disponible pour cette taille de rôle de machine virtuelle. Par exemple un disque Standard_DS14 prendra en charge jusqu'à 512 Mo/s ; par conséquent, avec trois disques P30, vous pourriez saturer la bande passante de disque de la machine virtuelle. Mais dans cet exemple, la limite de débit peut être dépassée selon la combinaison d'opérations d'E/S en lecture et écriture.

## <a name="new-deployments"></a>Nouveaux déploiements
Les deux sections suivantes montrent comment déployer des machines virtuelles SQL Server dans un stockage Premium. Comme mentionné précédemment, il est inutile de placer le disque du système d'exploitation sur un stockage Premium. Vous pouvez choisir de le faire si vous avez l'intention de placer des charges de travail d'E/S intensives sur le disque dur virtuel du système d'exploitation.

Le premier exemple montre l'utilisation d'images de galerie Azure existantes. Le deuxième exemple montre l'utilisation d'une image de machine virtuelle personnalisée stockée dans un compte de stockage Standard existant.

> [!NOTE]
> Ces exemples supposent que vous avez déjà créé un réseau virtuel régional.
>
>

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>Création d'une nouvelle machine virtuelle avec un stockage Premium et une image de galerie
L'exemple suivant montre comment placer le disque dur virtuel du système d'exploitation sur un stockage Premium et comment connecter les disques durs virtuels de stockage Premium. Toutefois, vous pouvez également placer le disque du système d'exploitation sur un compte de stockage Standard puis connecter les disques durs virtuels qui résident sur un compte de stockage Premium. Ces deux scénarios sont présentés.

    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Set up subscription
    Set-AzureSubscription -SubscriptionName $mysubscription
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### <a name="step-1-create-a-premium-storage-account"></a>Étape 1 : création d'un compte de stockage Premium
    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  


#### <a name="step-2-create-a-new-cloud-service"></a>Étape 2 : création d'un nouveau service cloud
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>Étape 3 : réservation d'une adresse IP virtuelle de service cloud (facultatif)
    #check exisitng reserved VIP
    Get-AzureReservedIP

    $reservedVIPName = “sqlcloudVIP”
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location

#### <a name="step-4-create-a-vm-container"></a>Étape 4 : création d'un conteneur de machines virtuelles
    #Generate storage keys for later
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    ##Generate storage acc contexts
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>Étape 5 : placement du disque dur virtuel du système d'exploitation sur Standard ou Premium stockage
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in

    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams"

    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### <a name="step-6-create-vm"></a>Étape 6 : création d'une machine virtuelle
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
    $image = $galleryImage.ImageName

    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"

    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"

    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account

    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

    #Check VHD storage account, these should be in $newxiostorageaccountname
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk


### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>Création d’une machine virtuelle pour utiliser Premium Storage avec une image personnalisée
Ce scénario vous montre où sont placées les images personnalisées existantes qui résident sur un compte de stockage Standard. Comme mentionné, si vous souhaitez placer le disque dur virtuel du système d'exploitation sur un stockage Premium, vous devez copier l'image existante sur le compte de stockage Standard et la transférer vers un stockage Premium pour pouvoir l'utiliser. Si vous disposez d’une image locale, vous pouvez également utiliser cette méthode pour la copier directement sur le compte de stockage Premium.

#### <a name="step-1-create-storage-account"></a>Étape 1 : création d'un compte de stockage
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Standard Storage account
    $origstorageaccountname = "danstdams"

#### <a name="step-2-create-cloud-service"></a>Étape 2 : création d'un service cloud
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location


#### <a name="step-3-use-existing-image"></a>Étape 3 : utilisation d’une image existante
Vous pouvez utiliser une image existante. Vous pouvez [prendre l’image d’une machine existante](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Notez que cette machine n’a pas besoin d’être une machine DS\*. Une fois l’image créée, appliquez les étapes suivantes, qui montrent comment la copier dans le compte Stockage Premium à l’aide de l’applet de commande PowerShell **Start-AzureStorageBlobCopy**.

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#### <a name="step-4-copy-blob-between-storage-accounts"></a>Étape 4: copie d'objets Blob entre des comptes de stockage
    #Get Image VHD
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'

    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### <a name="step-5-regularly-check-copy-status"></a>Étape 5 : vérification régulière de l'état de la copie :
    $blob | Get-AzureStorageBlobCopyState

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>Étape 6 : ajout d’un disque d’image au référentiel de disque Azure dans l’abonnement
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
    $newimageName = "prem"+"dansoldonorsql2k14"

    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation

> [!NOTE]
> Il est possible que même si l'état indique que l'opération a réussi, une erreur de bail de disque s'affiche. Dans ce cas, attendez 10 minutes environ.
>
>

#### <a name="step-7--build-the-vm"></a>Étape 7 : création de la machine virtuelle
Ici, vous créez la machine virtuelle à partir de votre image et connectez deux disques durs virtuels de stockage Premium :

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"

    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"

    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"

    #create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS3"

    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”


    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Déploiements existants qui n’utilisent pas les groupes de disponibilité Always On
> [!NOTE]
> Pour des déploiements existants, voir tout d’abord la section [Conditions préalables](#prerequisites-for-premium-storage) de cette rubrique.
>
>

Il existe différents points à prendre en compte concernant les déploiements SQL Server qui utilisent ou non des groupes de disponibilité Always On. Si vous n’utilisez pas Always On et que vous disposez d’un serveur SQL Server autonome, vous pouvez effectuer une mise à niveau vers Premium Storage à l’aide d’un nouveau service cloud et d’un compte de stockage. Examinez les options suivantes :

* **Créez une nouvelle machine virtuelle SQL Server**. Vous pouvez créer une nouvelle machine virtuelle SQL qui utilise un compte de stockage Premium comme décrit dans les nouveaux déploiements. Ensuite, sauvegardez et restaurez votre configuration SQL Server et vos bases de données utilisateur. L'application devra être mise à jour pour référencer la nouvelle configuration SQL Server si elle accessible de façon interne ou externe. Vous devez copier tous les objets 'out of db' comme si vous effectuiez une migration SQL Server de type Side by Side (SxS). Cela inclut des objets tels que les connexions, les certificats et les serveurs liés.
* **Migrez une machine virtuelle SQL Server existante**. Cette opération exige la déconnexion de la machine virtuelle SQL Server, puis son transfert vers un nouveau service cloud, y compris la copie de tous ses disques durs virtuels connectés au compte de stockage Premium. Lorsque la machine virtuelle est mise en ligne, l'application référencera le nom d'hôte du serveur comme avant. N'oubliez pas que la taille du disque existant affectera les performances. Par exemple, un disque de 400 Go correspond à un disque P20. Si vous savez que vous n'avez pas besoin de telles performances, vous pouvez recréer la machine virtuelle comme une machine virtuelle de série DS et connecter des disques durs virtuels de stockage Premium offrant la taille ou les performances requises. Vous pouvez ensuite déconnecter puis reconnecter les fichiers de la base de données SQL.

> [!NOTE]
> Lors de la copie de disques durs virtuels, tenez compte de la taille car ce paramètre détermine le type de disque de stockage Premium correspondant, et donc les performances du disque. Comme Azure utilisera le disque le plus proche, si vous avez un disque de 400 Go, ce sera un disque de type P20. Selon les besoins d'E/S existants du disque dur virtuel du système d'exploitation, vous n'aurez peut-être pas besoin de migrer vers un compte de stockage Premium.
>
>

Si votre serveur SQL Server est accessible en externe, l'adresse IP virtuelle du service cloud changera. Vous devez également mettre à jour les points de terminaison, les listes de contrôle d’accès (ACL) et les paramètres DNS.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Déploiements existants qui utilisent les groupes de disponibilité Always On
> [!NOTE]
> Pour des déploiements existants, voir tout d’abord la section [Conditions préalables](#prerequisites-for-premium-storage) de cette rubrique.
>
>

Dans cette section, nous examinons la façon dont le groupe de disponibilité Always On interagit avec la mise en réseau Azure. Nous décomposerons ensuite les migrations en deux scénarios : les migrations où une interruption de service est tolérée, et celles où vous devez limiter au maximum les temps d'arrêt.

Les groupes locaux de disponibilité Always On SQL Server utilisent un écouteur local qui inscrit un nom DNS virtuel ainsi qu’une adresse IP partagée entre un ou plusieurs serveurs SQL. Lorsque les clients se connectent, ils sont dirigés via l'IP de l'écouteur vers le serveur SQL principal. À ce stade, il s’agit du serveur qui possède la ressource IP Always On.

![DeploymentsUseAlways On][6]

Dans Microsoft Azure, vous ne pouvez attribuer qu'une seule adresse IP à une carte réseau sur la machine virtuelle ; par conséquent, pour atteindre la même couche d'abstraction qu'en local, Azure utilise l'adresse IP attribuée aux équilibreurs de charge internes/externes (ILB/ELB). La ressource IP partagée entre les serveurs est définie sur la même adresse IP que l'ILB/ELB. Il est publié dans le DNS, et le trafic client est transmis via l'ILB/ELB au réplica SQL Server principal. L’ILB/ELB sait identifier le serveur SQL principal, puisqu’il utilise des sondes pour détecter la ressource IP Always On. Dans l'exemple précédent, il teste chaque nœud comportant un point de terminaison référencé par l'ELB/ILB, et celui qui répond est le serveur SQL principal.

> [!NOTE]
> L'ILB et l'ELB sont affectés à un service cloud Azure particulier et par conséquent, toute migration de cloud dans Azure impliquera probablement le changement de l'adresse IP de l'équilibreur de charge.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>Migration de déploiements Always On autorisant des temps d’arrêt
Il existe deux stratégies de migration de déploiements Always On qui autorisent des temps d’arrêt :

1. **Ajouter plusieurs réplicas secondaires à un cluster Always On existant**
2. **Effectuer la migration vers un nouveau cluster Always On**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. Ajouter plusieurs réplicas secondaires à un cluster Always On existant
Une stratégie consiste à ajouter plusieurs serveurs secondaires au groupe de disponibilité Always On. Vous devez ajouter ces réplicas à un nouveau service cloud et mettre à jour l'écouteur avec la nouvelle adresse IP de l'équilibreur de charge.

##### <a name="points-of-downtime"></a>Points d’arrêt :
* Validation de cluster.
* Test des basculements Always On de nouveaux réplicas secondaires.

Si vous utilisez des pools de stockage Windows au sein de la machine virtuelle pour augmenter le débit d'E/S, ces pools seront mis hors ligne pendant une validation complète de cluster. Le test de validation est requis lorsque vous ajoutez des nœuds au cluster. Le temps nécessaire à l'exécution du test peut varier ; vous devez donc tester le cluster dans votre environnement de test représentatif pour obtenir une durée approximative de la durée de cette opération.

Vous devez prévoir suffisamment de temps pour effectuer un basculement manuel et un test CHAOS sur les nœuds récemment ajoutés pour vérifier que la haute disponibilité Always On fonctionne comme prévu.

![DeploymentUseAlways On2][7]

> [!NOTE]
> Avant d’effectuer la validation, vous devez arrêter toutes les instances de SQL Server qui utilisent les pools de stockage.
>
> ##### <a name="high-level-steps"></a>Procédure générale
>

1. Créez deux serveurs SQL dans le nouveau service cloud avec une connexion au stockage Premium.
2. Copiez les sauvegardes complètes et effectuez une restauration avec **NORECOVERY**.
3. Copiez les objets dépendants 'out of user DB', notamment que les connexions.
4. Créez un nouvel équilibreur de charge interne (ILB) ou utilisez un équilibreur de charge externe (ELB), puis configurez ensuite les points de terminaison d'équilibrage de charge sur les deux nouveaux nœuds.

   > [!NOTE]
   > Vérifiez que la configuration du point de terminaison est correcte pour tous les nœuds avant de continuer.
   >
   >
5. Arrêtez l'accès de l'utilisateur/application à SQL Server (si vous utilisez des pools de stockage).
6. Arrêtez les services du moteur SQL Server sur tous les nœuds (si vous utilisez des pools de stockage).
7. Ajoutez de nouveaux nœuds de cluster et effectuez la validation complète.
8. Une fois la validation réussie, démarrez tous les services SQL Server.
9. Sauvegardez les journaux des transactions et restaurez les bases de données utilisateur.
10. Ajoutez de nouveaux nœuds au groupe de disponibilité Always On et définissez la réplication sur **Synchrone**.
11. Ajoutez la ressource d’adresse IP de l’ILB/ELB du nouveau service cloud par le biais de PowerShell pour Always On en fonction de l’exemple multisite de [l’annexe](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage). Dans le clustering Windows, définissez les **propriétaires possibles** de la ressource d’**adresse IP** sur les nouveaux nœuds. Consultez la section « Ajout d'une ressource d'adresse IP sur le même sous-réseau » de l' [annexe](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
12. Basculement vers un des nouveaux nœuds.
13. Configurez les nouveaux nœuds en tant que partenaires de basculement automatique puis testez les basculements.
14. Supprimez les nœuds d'origine du groupe de disponibilité.

##### <a name="advantages"></a>Avantages
* Les nouveaux serveurs SQL peuvent être testés (SQL Server et application) avant d’être ajoutés à Always On.
* Vous pouvez modifier la taille de la machine virtuelle et personnaliser le stockage exactement selon vos besoins. Mais il serait préférable de conserver tous les chemins vers les fichiers SQL.
* Vous pouvez contrôler le démarrage du transfert des sauvegardes de base de données pour les réplicas secondaires. Cela diffère de l'utilisation de l'applet de commande Azure **Start-AzureStorageBlobCopy** pour copier des disques durs virtuels car il s'agit d'une copie asynchrone.

##### <a name="disadvantages"></a>Inconvénients
* Lorsque vous utilisez des pools de stockage Windows, un temps d'arrêt se produit au niveau du cluster lors de la validation complète du cluster pour les nouveaux nœuds supplémentaires.
* En fonction de la version SQL Server et du nombre de réplicas secondaires existants, vous ne pourrez peut-être pas ajouter plusieurs réplicas secondaires sans supprimer des réplicas secondaires existants.
* Le transfert des données SQL peut prendre du temps lors de la configuration des réplicas secondaires.
* Pendant la migration, l’exécution de nouvelles machines en parallèle entraîne un coût supplémentaire.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. Effectuer la migration vers un nouveau cluster Always On
Une autre stratégie consiste à créer un tout nouveau cluster Always On avec de nouveaux nœuds dans le nouveau service cloud en incitant les clients à l’utiliser.

##### <a name="points-of-downtime"></a>Points d’arrêt
Un temps d’arrêt se produit lorsque vous transférez des applications et des utilisateurs vers le nouvel écouteur Always On. Ce temps d'arrêt dépend des éléments suivants :

* Le temps nécessaire pour restaurer les sauvegardes du journal des transactions final aux bases de données sur les nouveaux serveurs.
* Le temps nécessaire pour mettre à jour les applications clientes afin d’utiliser le nouvel écouteur Always On.

##### <a name="advantages"></a>Avantages
* Vous pouvez tester l'environnement de production réel, SQL Server, et les modifications apportées à la build du système d'exploitation.
* Vous avez la possibilité de personnaliser le stockage et de réduire la taille de la machine virtuelle. Cela pourrait entraîner une réduction des coûts.
* Vous pouvez mettre à jour votre build ou version SQL Server pendant ce processus. Vous pouvez également mettre à niveau le système d'exploitation.
* Le cluster Always On précédent peut servir de cible de restauration solide.

##### <a name="disadvantages"></a>Inconvénients
* Vous devez modifier le nom DNS de l’écouteur si vous souhaitez exécuter les deux clusters Always On simultanément. Cela ajoute une surcharge administrative lors de la migration car les chaînes de l'application cliente doivent refléter le nouveau nom de l'écouteur.
* Vous devez mettre en œuvre un mécanisme de synchronisation entre les deux environnements afin de les garder aussi proches que possible et réduire ainsi les exigences de synchronisation finale avant la migration.
* Pendant la migration, le nouvel environnement est exécuté, ce qui entraîne un coût supplémentaire.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>Migration de déploiements Always On avec un temps d’arrêt minimal
Il existe deux stratégies pour effectuer la migration de déploiements Always On avec un temps d’arrêt minimal :

1. **Utiliser un service secondaire existant : site unique**
2. **Utiliser des réplicas secondaires existants : multi-sites**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. Utilisation d’un service secondaire existant : site unique
Une stratégie limitant les temps d'arrêt consiste à prendre un service cloud existant secondaire et à supprimer le service cloud actuel. Copiez les disques durs virtuels vers le nouveau compte de stockage Premium et créez la machine virtuelle dans le nouveau service cloud. Puis mettez à jour l'écouteur dans le clustering et effectuez le basculement.

##### <a name="points-of-downtime"></a>Points d’arrêt
* Un temps d’arrêt se produit lorsque vous mettez à jour le nœud final avec le point de terminaison d’équilibrage de charge.
* La reconnexion de votre client peut être retardée en fonction de votre configuration client/DNS.
* Un temps d’arrêt supplémentaire survient si vous choisissez de mettre hors ligne le groupe Cluster Always On afin de permuter les adresses IP. Vous pouvez éviter cela en utilisant une dépendance OR et en choisissant les propriétaires possibles pour la ressource d’adresse IP ajoutée. Consultez la section « Ajout d'une ressource d'adresse IP sur le même sous-réseau » de l' [annexe](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).

> [!NOTE]
> Lorsque vous souhaitez que le nœud ajouté joue le rôle de partenaire de basculement Always On, vous devez ajouter un point de terminaison Azure avec une référence au jeu d’équilibrage de la charge. Lorsque vous exécutez la commande **Add-AzureEndpoint** pour cette opération, les connexions actuelles restent ouvertes, mais les nouvelles connexions à l'écouteur ne pourront pas être établies tant que l'équilibreur de charge n'a pas été mis à jour. Dans ce test, l'opération dure de 90 à 120 secondes (à vérifier).
>
>

##### <a name="advantages"></a>Avantages
* Aucun coût supplémentaire pendant la migration.
* Une migration un à un.
* Moins de complexité.
* Permet d'augmenter le nombre d'opérations d'E/S à partir des SKU de stockage Premium. Lorsque les disques sont déconnectés de la machine virtuelle et copiés vers le nouveau service cloud, un outil tiers peut être utilisé pour augmenter la taille du disque dur virtuel et fournir des débits plus importants. Pour augmenter la taille du disque dur virtuel, consultez ce [forum de discussion](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows).

##### <a name="disadvantages"></a>Inconvénients
* Il existe une perte temporaire de haute disponibilité et de récupération d'urgence pendant la migration.
* Comme il s'agit d'une migration 1:1, vous devez utiliser une taille minimale de machine virtuelle qui prendra en charge le nombre de disques durs virtuels, et vous risquez donc de ne plus pouvoir réduire vos machines virtuelles.
* Pour ce scénario, utilisez l'applet de commande Azure **Start-AzureStorageBlobCopy** , qui est asynchrone. Il n'existe aucun contrat SLA à la fin de la copie. Le temps de copie varie selon la durée d'attente dans la file et de la quantité de données à transférer. Le temps de copie augmente si le transfert est destiné à un autre centre de données Azure prenant en charge le stockage Premium dans une autre région. Si vous avez seulement 2 nœuds, prévoyez une atténuation au cas où la copie prend plus de temps que lors du test. Dans ce cas, suivez ces conseils.
  * Ajoutez un 3e nœud SQL Server temporaire pour la haute disponibilité avant la migration, avec un temps d'arrêt convenu.
  * Exécutez la migration en dehors de la maintenance Azure planifiée.
  * Vérifiez que vous avez correctement configuré votre quorum de cluster.  

##### <a name="high-level-steps"></a>Procédure générale
Ce document ne présente pas un exemple complet de bout en bout, toutefois, l' [annexe](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) fournit des informations qui peuvent être exploitées pour effectuer cette opération.

![MinimalDowntime][8]

* Collectez la configuration des disques et supprimez le nœud (ne supprimez pas les disques durs virtuels connectés).
* Création d'un compte de stockage Premium et copie des disques durs virtuels à partir du compte de stockage Standard
* Créez un service cloud et redéployez la machine virtuelle SQL2 dans ce service cloud. Créez la machine virtuelle à l'aide du disque dur virtuel d'origine du système d'exploitation copié et connectez les disques durs virtuels copiés.
* Configurez l'ILB/ELB et ajoutez des points de terminaison.
* Mettez à jour l'écouteur en procédant comme suit :
  * Mettez hors ligne le groupe Always On et mettez à jour l’écouteur Always On avec la nouvelle adresse IP de l’ILB/ELB.
  * Ou ajoutez la ressource d’adresse IP de l’ILB/ELB du nouveau service cloud via PowerShell dans le clustering Windows. Puis affectez les propriétaires possibles de la ressource d'adresse IP au nœud migré, SQL2, définissez ce paramètre comme dépendance OR dans le nom de réseau. Consultez la section « Ajout d'une ressource d'adresse IP sur le même sous-réseau » de l' [annexe](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage).
* Vérifiez la configuration/propagation DNS vers les clients.
* Migrez la machine virtuelle SQL1 et suivez les étapes 2 à 4.
* Si vous utilisez les étapes 5ii, ajoutez SQL1 comme propriétaire possible pour la ressource d'adresse IP ajoutée
* Testez les basculements.

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. Utilisation de réplicas secondaires existants : multi-sites
Si vous disposez de nœuds dans plusieurs centres de données Azure ou que vous évoluez dans un environnement hybride, vous pouvez utiliser une configuration Always On dans cet environnement pour réduire les temps d’arrêt.

L’approche consiste à définir la synchronisation Always On sur Synchrone pour le centre de données Azure local ou secondaire, puis à basculer sur ce serveur SQL. Copiez les disques durs virtuels vers un compte de stockage Premium, puis redéployez la machine dans un nouveau service cloud. Mettez à jour l'écouteur, puis restaurez-le.

##### <a name="points-of-downtime"></a>Points d’arrêt
Le temps d'arrêt inclut le délai de basculement vers l'autre centre de données et inversement. Il dépend de votre configuration client/DNS et la reconnexion du client peut être retardée.
Considérons l’exemple ci-après d’une configuration Always On hybride :

![MultiSite1][9]

##### <a name="advantages"></a>Avantages
* Vous pouvez utiliser l'infrastructure existante.
* Vous avez la possibilité d'effectuer d'abord une pré-mise à niveau du stockage Azure sur le centre de données de récupération d'urgence Azure.
* Le stockage du centre de données de récupération d'urgence Azure peut être reconfiguré.
* Il existe un minimum de deux basculements pendant la migration, à l'exclusion des basculements de test.
* Vous n'avez pas besoin déplacer des données SQL Server avec la sauvegarde et la restauration.

##### <a name="disadvantages"></a>Inconvénients
* En fonction de l'accès client à SQL Server, il peut y avoir une latence plus élevée lorsque SQL Server est en cours d'exécution dans un autre centre de données que l'application.
* Le temps de copie des disques durs virtuels vers un stockage Premium peut être long. Cela peut affecter votre décision de conserver ou non le nœud du groupe de disponibilité. Considérez cela si la journalisation des charges de travail intensives est nécessaire pendant la migration, car le nœud principal doit conserver les transactions non répliquées dans son journal des transactions. Par conséquent, cela peut augmenter considérablement.
* Pour ce scénario, utilisez l'applet de commande Azure **Start-AzureStorageBlobCopy** , qui est asynchrone. Il n'existe aucun contrat SLA à la fin. Le temps des copies varie selon la durée d'attente dans la file et de la quantité de données à transférer. Vous n'avez donc qu'un seul nœud dans votre deuxième centre de données, et vous devez effectuer les opérations d'atténuation au cas où la copie prend plus de temps que lors des tests. Dans ce cas, suivez ces conseils.
  * Ajoutez un 2e nœud SQL Server temporaire pour la haute disponibilité avant la migration, avec un temps d'arrêt convenu.
  * Exécutez la migration en dehors de la maintenance Azure planifiée.
  * Vérifiez que vous avez correctement configuré votre quorum de cluster.

Ce scénario suppose que vous avez documenté votre installation et savez comment le stockage est mappé pour apporter des modifications aux paramètres de mise en cache optimale des disques.

##### <a name="high-level-steps"></a>Procédure générale
![Multisite2][10]

* Définissez le centre de données Azure local/alternatif comme SQL Server principal et partenaire de basculement automatique (AFP).
* Collectez les informations de configuration des disques de SQL2 et supprimez le nœud (ne supprimez pas les disques durs virtuels connectés).
* Créez un compte de stockage Premium et copiez les disques durs virtuels à partir du compte de stockage Standard.
* Créez un nouveau service cloud et créez la machine virtuelle SQL2 avec ses disques de stockage Premium connectés.
* Configurez l'ILB/ELB et ajoutez des points de terminaison.
* Mettez à jour l’écouteur Always On avec la nouvelle adresse IP de l’ILB/ELB, puis testez le basculement.
* Vérifiez la configuration DNS.
* Définissez l'AFP sur SQL2, migrez SQL1 puis suivez les étapes 2 à 5.
* Testez les basculements.
* Repositionnez l'AFP sur SQL1 et SQL2

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>Annexe : migration d’un cluster Always On multisite vers Premium Storage
Le reste de cette rubrique décrit un exemple détaillé de conversion d’un cluster Always On multisite en stockage Premium. L'exemple décrit également la conversion de l'écouteur d'un équilibreur de charge externe (ELB) à un équilibrage de charge interne (ILB).

### <a name="environment"></a>Environnement
* Windows 2k12 / SQL 2k12
* 1 base de données de fichiers sur SP
* 2 x pools de stockage par nœud

![Appendix1][11]

### <a name="vm"></a>MV :
Cet exemple décrit la conversion d'un ELB en ILB. L'ELB étant disponible avant l'ILB, cet exemple montre comment effectuer la conversion pendant la migration.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>Étapes préparatoires : se connecter à l’abonnement
    Add-AzureAccount

    #Set up subscription
    Get-AzureSubscription

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>Étape 1: créer les nouveaux compte de stockage et service cloud
    $mysubscription = "DansSubscription"
    $location = "West Europe"

    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"

    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"

    ##Existing cloud service
    $sourceSvc="dansolSrcAms"

    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms"
    New-AzureService $destcloudsvc -Location $location

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>Étape 2 : augmenter le niveau des échecs autorisés sur les ressources <Optional>
Sur certaines ressources appartenant à votre groupe de disponibilité Always On, il existe des limites concernant le nombre d’erreurs qui peuvent se produire dans une période où le service de cluster tente de redémarrer le groupe de ressources. Il est recommandé d'augmenter cette valeur au cours de cette procédure car si vous n'effectuez pas manuellement les basculements en arrêtant les machines, vous risquez de vous rapprocher de cette limite.

Il est prudent de doubler la tolérance de défaillance ; pour cela, ouvrez le Gestionnaire du cluster de basculement, puis accédez aux propriétés du groupe de ressources Always On :

![Appendix3][13]

Modifiez le nombre maximal d'échecs à 6.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>Étape 3 : ajouter une ressource d’adresse IP au groupe de clusters <Optional>
Si vous n'avez qu'une seule adresse IP pour le groupe de clusters et qu'elle est alignée sur le sous-réseau cloud, prenez garde car si vous mettez hors ligne accidentellement tous les nœuds de cluster du cloud sur ce réseau, la ressource IP du cluster et le nom du réseau cluster ne pourront pas être mis en ligne. Dans ce cas, cela empêcherait les mises à jour d'autres ressources de cluster.

#### <a name="step-4-dns-configuration"></a>Étape 4 : configuration DNS
L’implémentation d’une transition fluide dépend du mode d’utilisation et de configuration du DNS.
Lorsque la fonctionnalité Always On est installée, elle crée un groupe de ressources de cluster Windows ; si vous ouvrez le Gestionnaire du cluster de basculement, vous verrez qu’il comporte au moins trois ressources, et les deux auxquelles ce document fait référence sont :

* Nom de réseau virtuel (VNN) : il s’agit du nom DNS utilisé par le client pour se connecter aux serveurs SQL par le biais d’Always On.
* Ressource d'adresse IP – il s'agit de l'adresse IP associée au VNN ; vous pouvez en utiliser plusieurs, et dans une configuration multi-sites, vous aurez une adresse IP par site/sous-réseau.

Lors de la connexion à SQL Server, le pilote du client SQL Server extrait les enregistrements DNS associés à l’écouteur, et tente de vous connecter à chaque adresse IP Always On associée. Nous abordons ci-dessous certains facteurs pouvant influencer cette opération.

Le nombre d’enregistrements DNS simultanés associés au nom de l’écouteur dépend non seulement du nombre d’adresses IP associées, mais également du paramètre « RegisterAllIpProviders » de clustering de basculement pour la ressource VNN Always On.

Lorsque vous déployez Always On dans Azure, différentes étapes permettent de créer l’écouteur et les adresses IP, et vous devez définir manuellement le paramètre « RegisterAllIpProviders » sur 1, contrairement à un déploiement Always On local, où il est déjà défini sur 1.

Si 'RegisterAllIpProviders' est défini sur 0, vous ne verrez qu'un seul enregistrement DNS dans le DNS associé à l'écouteur :

![Appendix4][14]

Si 'RegisterAllIpProviders' est 1 :

![Appendix5][15]

Le code ci-dessous vide les paramètres VNN et les définit pour vous. Notez que, pour que la modification soit appliquée, vous devez mettre le VNN hors ligne puis le remettre en ligne, et que cette déconnexion de l’écouteur entraîne une interruption de la connectivité client.

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1

Dans une étape de migration ultérieure, vous devrez mettre à jour l’écouteur Always On avec une adresse IP mise à jour faisant référence à un équilibreur de charge, ce qui impliquera la suppression et l’ajout d’une ressource d’adresse IP. Après la mise à jour de l'adresse IP, vous devez vérifier que la nouvelle adresse IP a été mise à jour dans la zone DNS et que les clients ont mis à jour leur cache DNS local.

Si vos clients résident dans un segment de réseau différent et référencent un autre serveur DNS, vous devez tenir compte de ce qui se passe au niveau du transfert de la zone DNS pendant la migration, car le délai de reconnexion de l’application est contraint au moins par le temps de transfert de la zone de toute nouvelle adresse IP pour l’écouteur. Si vous êtes pressé par le temps, vous devez étudier et forcer le test d'un transfert de zone incrémentiel avec vos équipes Windows, et également consigner l'enregistrement de l'hôte DNS en choisissant une durée de vie TTL (Time To Live) inférieure pour permettre aux clients de se mettre à jour. Pour plus d’informations, consultez [Transferts de zone incrémentiels](https://technet.microsoft.com/library/cc958973.aspx) et [Start-DnsServerZoneTransfer](https://technet.microsoft.com/library/jj649917.aspx).

Par défaut, la durée de vie (TTL) d’un enregistrement DNS associé à l’écouteur Always On dans Azure est de 1 200 secondes. Vous pouvez réduire ce paramètre si vous êtes pressé par le temps pendant votre migration afin de permettre aux clients de mettre à jour leurs DNS avec l'adresse IP mise à jour pour l'écouteur. Vous pouvez afficher et modifier la configuration en vidant la configuration du VNN :

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter

    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120

Veuillez noter que la diminution de la valeur 'HostRecordTTL' augmente le trafic DNS.

##### <a name="client-application-settings"></a>Paramètres de l’application cliente
Si votre application cliente SQL prend en charge .Net 4.5 SQLClient, vous pouvez utiliser le mot clé « MULTISUBNETFAILOVER = TRUE » ; il est recommandé d’appliquer ce dernier, car il accélère la connexion au groupe de disponibilité SQL Always On pendant le basculement. Il énumère toutes les adresses IP associées à l’écouteur Always On en parallèle et effectue une tentative de reconnexion TCP plus rapide lors d’un basculement.

Pour plus d'informations sur les paramètres ci-dessus, consultez la rubrique [Mot clé MultiSubnetFailover et fonctionnalités associées](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover). Consultez également [Prise en charge SqlClient pour la haute disponibilité et récupération d’urgence](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx).

#### <a name="step-5-cluster-quorum-settings"></a>Étape 5 : paramètres de quorum de cluster
Comme vous allez arrêter au moins un serveur SQL à la fois, vous devez modifier le paramètre de quorum du cluster. Si vous utilisez le système File Share Witness (FSW) avec deux nœuds, vous devez définir le quorum pour prendre en compte une majorité de nœuds et utiliser le vote dynamique afin qu’un seul nœud reste actif.

    Set-ClusterQuorum -NodeMajority  

Pour plus d'informations sur la gestion et la configuration du quorum de cluster, consultez la rubrique [Configurer et gérer le quorum dans un cluster de basculement Windows Server 2012](https://technet.microsoft.com/library/jj612870.aspx).

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>Étape 6 : extraction des points de terminaison et des ACL existants
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the Always On Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

Enregistrez ces éléments dans un fichier texte.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>Étape 7 : modification les partenaires de basculement et des modes de réplication
Si vous avez plus de 2 serveurs SQL, vous devez définir sur 'Synchrone' le basculement d’un autre serveur secondaire d’un centre de données ou en local et le configurer comme partenaire de basculement automatique (AFP) ; cela garantit une haute disponibilité lorsque vous apportez des modifications. Vous pouvez le faire via TSQL ou via SSMS :

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>Étape 8 : Suppression de la machine virtuelle secondaire du service cloud
Vous devez prévoir de migrer d'abord un nœud de cloud secondaire ; s'il s'agit du nœud principal, vous devez effectuer un basculement manuel.

    $vmNameToMigrate="dansqlams2"

    #Check machine status
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


    #Extract disk configuration

    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>Étape 9 : modification des paramètres de mise en cache du disque dans un fichier CSV et enregistrement
Pour les volumes de données, ces paramètres doivent être définis sur READONLY.

Pour les volumes TLOG, ils doivent être définis sur NONE.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>Étape 10 : copie de disques durs virtuels
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext

    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }



Vous pouvez vérifier l'état de la copie des disques durs virtuels pour le compte de stockage Premium :

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix8][18]

Attendez que toutes ces opérations soient correctement terminées.

Pour plus d'informations pour les objets BLOB individuels :

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext

#### <a name="step-11-register-os-disk"></a>Étape 11 : inscription du disque du système d’exploitation
    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>Étape 12 : importation d’un serveur secondaire dans un nouveau service cloud
Le code ci-dessous utilise également l'option ajoutée ici pour importer la machine et utiliser l'adresse IP virtuelle conservable.

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"

    #Create new Avaiability Set
    $availabilitySet = "cloudmigAVAMS"

    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>Étape 13 : création d'un ILB sur le nouveau service cloud, ajout de points terminaux d'équilibrage de charge et d'ACL
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

    #SET Azure ACLs or Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

#### <a name="step-14-update-always-on"></a>Étape 14 : mise à jour d’Always On
    #Code to be executed on a Cluster Node
    $ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
    $newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"


    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

    #set dependancy and NETBIOS, then remove old IP address

    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

    #Make sure no static records in DNS

![Appendix9][19]

Supprimez maintenant l'ancienne adresse IP du service cloud.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>Étape 15 : vérification de la mise à jour DNS
Vous devriez maintenant vérifier les serveurs DNS sur les réseaux du client SQL Server et vous assurer que le clustering a ajouté l'enregistrement hôte supplémentaire pour l'adresse IP ajoutée. Si ces serveurs DNS n’ont pas été mis à jour, essayez de forcer un transfert de la zone DNS et vérifiez que les clients sur le sous-réseau peuvent résoudre les deux adresses IP Always On, ce qui évite d’attendre la réplication DNS automatique.

#### <a name="step-16-reconfigure-always-on"></a>Étape 16 : reconfiguration d’Always On
À ce stade, attendez que le nœud secondaire qui a été migré soit totalement resynchronisé avec le nœud local, puis basculez vers un nœud de réplication synchrone pour en faire l’AFP.  

#### <a name="step-17-migrate-second-node"></a>Étape 17 : migration du deuxième nœud
    $vmNameToMigrate="dansqlams1"

    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

    #Get disk config

    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
    }

    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
    $addosdisk | add-content -path $file

    #Import disk config
    $diskobjects  = Import-CSV $file

    #Check disk configuration
    $diskobjects

    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName

    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>Étape 18 : modification des paramètres de mise en cache du disque dans un fichier CSV et enregistrement
Pour les volumes de données, ces paramètres doivent être définis sur READONLY.

Pour les volumes TLOG, ils doivent être définis sur NONE.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>Étape 19 : création d'un nouveau compte de stockage indépendant pour le nœud secondaire
    $newxiostorageaccountnamenode2 = "danspremsams2"
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"

    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

#### <a name="step-20-copy-vhds"></a>Étape 20 : copie de disques durs virtuels
    #Ensure you have created the container for these:
    $containerName = 'vhds'

    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }

    #Check for copy progress

    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext


Vous pouvez vérifier l'état de copie de tous les disques durs virtuels : ($disk in $diskobjects) { $lun = $disk.Lun $vhdname = $disk.vhdname $cacheoption = $disk.HostCaching $disklabel = $disk.DiskLabel $diskName = $disk.DiskName

       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
       }

![Appendix12][22]

Attendez que toutes ces opérations soient correctement terminées.

Pour plus d'informations pour les objets BLOB individuels :

    #Check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2

#### <a name="step-21-register-os-disk"></a>Étape 21 : inscription du disque du système d’exploitation
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current

    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName

    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"

    #Join to existing Avaiability Set

    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

    ForEach ( $attachdatadisk in $datadiskimport)
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

    }

    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>Étape 22 : ajout de points de terminaison et de ACL avec équilibrage de charge
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


    #STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

    #SET ACLs or Azure Network Security Groups & Windows FWs

    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### <a name="step-23-test-failover"></a>Étape 23 : test du basculement
Vous devriez maintenant laisser le nœud migré se synchroniser avec le nœud Always On local, le placer en mode de réplication synchrone et attendre qu’il soit synchronisé. Effectuez ensuite un basculement du nœud local vers le premier nœud migré, c'est-à-dire l'AFP. Une fois l'opération réussie, changez le dernier nœud migré en AFP.

Vous devez tester les basculements entre tous les nœuds de test et effectuer des tests CHAOS pour vous assurer que les basculements se sont déroulés comme prévu et en temps opportun.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>Étape 24 : réinitialisation des paramètres du quorum de cluster / TTL DNS / Partenaires de basculement / Paramètres de synchronisation
##### <a name="adding-ip-address-resource-on-same-subnet"></a>Ajout de ressource d'adresse IP sur le même sous-réseau
Si vous disposez seulement de 2 serveurs SQL et que vous souhaitez en effectuer la migration vers un nouveau service cloud tout en les conservant sur le même sous-réseau, vous pouvez éviter de mettre hors ligne l’écouteur pour supprimer l’adresse IP Always On d’origine afin d’ajouter la nouvelle adresse IP. Si vous migrez les machines virtuelles vers un autre sous-réseau, inutile d'effectuer cette opération car un autre réseau de cluster référencera ce sous-réseau.

Une fois que vous avez sélectionné le serveur secondaire migré et ajouté la nouvelle ressource d'adresse IP pour le nouveau service cloud avant d'effectuer le basculement du serveur principal, vous devez suivre ces étapes dans le gestionnaire de basculement du cluster :

Pour ajouter l'adresse IP, consultez l'étape 14 de l' [annexe](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage).

1. Pour la ressource d’adresse IP actuelle, changez le propriétaire possible en « Serveur SQL principal existant », « dansqlams4 » dans l’exemple ci-dessous :

    ![Appendix13][23]
2. Pour la nouvelle ressource d’adresse IP, changez le propriétaire possible en « Serveur SQL secondaire migré », « dansqlams5 » dans l’exemple ci-dessous :

    ![Appendix14][24]
3. Une fois ces opérations terminées, vous pouvez effectuer le basculement, et lorsque le dernier nœud est migré, les propriétaires possibles doivent être modifiés pour ajouter ce nœud comme propriétaire possible :

    ![Appendix15][25]

## <a name="additional-resources"></a>Ressources supplémentaires
* [Stockage Premium Azure](../premium-storage.md)
* [Machines virtuelles](https://azure.microsoft.com/services/virtual-machines/)
* [SQL Server dans des machines virtuelles Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
