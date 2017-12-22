---
title: "Créer une machine virtuelle Windows avec PowerShell | Microsoft Docs"
description: "Créez des machines virtuelles Windows avec Azure PowerShell et le modèle de déploiement classique."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 42c0d4be-573c-45d1-b9b0-9327537702f7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: af672a873b33cbd0b6151fd564e84c96f0b6e1e3
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-windows-virtual-machine-with-powershell-and-the-classic-deployment-model"></a>Créer une machine virtuelle Windows avec PowerShell et le modèle de déploiement Classic
> [!div class="op_single_selector"]
> * [Portail Azure - Windows](tutorial.md)
> * [PowerShell - Windows](create-powershell.md)
> 
> 

<br>

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](../../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ces étapes vous montrent comment personnaliser un jeu de commandes Azure PowerShell en vue de créer et de préconfigurer une machine virtuelle Azure basée sur Windows à l'aide d'une approche modulaire. À l'aide de cette procédure, vous pouvez créer rapidement un jeu de commandes pour une nouvelle machine virtuelle basée sur Windows et étendre un déploiement existant, ou créer plusieurs jeux de commandes qui génèrent rapidement un environnement personnalisé de développement/test ou destiné aux professionnels de l'informatique.

Ces étapes utilisent une méthode de cases à remplir pour créer des jeux de commandes Azure PowerShell. Cette méthode peut être utile si vous découvrez PowerShell ou simplement si vous souhaitez connaître les valeurs à indiquer pour une configuration réussie. Les utilisateurs avancés de PowerShell peuvent prendre les commandes et indiquer leurs propres valeurs pour les variables (lignes commençant par « $ »).

Si ce n’est pas encore fait, installez Azure PowerShell sur votre ordinateur local à l’aide des instructions décrites dans [Installation et configuration d’Azure PowerShell](/powershell/azure/overview) . Puis ouvrez une invite de commandes Windows PowerShell.

## <a name="step-1-add-your-account"></a>Étape 1 : Ajouter votre compte
1. À l’invite PowerShell, tapez **Add-AzureAccount**, puis cliquez sur **Entrée**. 
2. Tapez l’adresse de messagerie associée à votre abonnement Azure, puis cliquez sur **Continuer**. 
3. Tapez le mot de passe de votre compte. 
4. Cliquez sur **Se connecter**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Étape 2 : Configurer votre abonnement et votre compte de stockage
Pour configurer votre abonnement et votre compte de stockage Azure, exécutez ces commandes à l’invite de commandes Windows PowerShell. Remplacez tous les éléments entre guillemets, y compris les caractères < et >, par les noms appropriés.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Le nom de l’abonnement apparaît dans la propriété SubscriptionName du résultat de la commande **Get-AzureSubscription** . Le nom du compte de stockage correct apparaît dans la propriété Label de la sortie de la commande **Get-AzureStorageAccount** une fois que vous avez exécuté la commande **Select-AzureSubscription**.

## <a name="step-3-determine-the-imagefamily"></a>Étape 3 : déterminer la valeur ImageFamily
Vous devez ensuite déterminer la valeur ImageFamily ou Étiquette pour l'image spécifique correspondant à la machine virtuelle Azure que vous voulez créer. Vous pouvez obtenir la liste des valeurs ImageFamily disponibles à l'aide de cette commande.

    Get-AzureVMImage | select ImageFamily -Unique

Voici des exemples de valeurs ImageFamily pour les ordinateurs basés sur Windows :

* Windows Server 2012 R2 Datacenter
* Windows Server 2008 R2 SP1
* Windows Server 2016 Technical Preview 4
* SQL Server 2012 SP1 Enterprise sur Windows Server 2012

Si vous trouvez l’image que vous recherchez, ouvrez une nouvelle instance de l’éditeur de texte de votre choix ou de l’environnement d’écriture de scripts intégré de PowerShell (ISE). Copiez le texte suivant dans le nouveau fichier texte ou PowerShell ISE, en remplaçant la valeur ImageFamily.

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Dans certains cas, le nom de l'image se trouve dans la propriété Étiquette au lieu de la valeur ImageFamily. Si vous ne trouvez pas l'image souhaitée à l'aide de la propriété ImageFamily, triez les images par propriété Étiquette à l'aide de cette commande.

    Get-AzureVMImage | select Label -Unique

Si vous trouvez l'image appropriée avec cette commande, ouvrez une nouvelle instance de l'éditeur de texte de votre choix ou PowerShell ISE. Copiez le texte suivant dans le nouveau fichier texte ou PowerShell ISE, en remplaçant la valeur Label.

    $label="<Label value>"
    $image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## <a name="step-4-build-your-command-set"></a>Étape 4 : générer votre jeu de commandes
Créez le reste de votre jeu de commandes en copiant le jeu de blocs approprié ci-dessous dans votre nouveau fichier texte ou ISE, puis en renseignant les valeurs des variables et en supprimant les caractères < et >. Pour avoir une idée du résultat final, consultez les deux [exemples](#examples) figurant à la fin de cet article.

Pour commencer, choisissez l'un des deux blocs de commandes suivants (obligatoire).

Option 1 : spécifiez un nom et une taille de machine virtuelle.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Option 2 : spécifiez un nom, une taille et un nom de groupe à haute disponibilité.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

Pour plus d’informations sur les valeurs InstanceSize des machines virtuelles des séries D, DS et G, voir l’article [Tailles de machines virtuelles et de services cloud pour Microsoft Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

> [!NOTE]
> Si vous avez conclu un accord d’entreprise avec Software Assurance et souhaitez tirer parti de Windows Server [Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/), ajoutez le paramètre **-LicenseType** à l’applet de commande **New-AzureVMConfig**, en passant la valeur **Windows_Server** pour le cas d’utilisation standard.  Vérifiez que vous utilisez une image que vous avez téléchargée ; vous ne pouvez pas utiliser une image standard de la galerie avec Hybrid Use Benefit.
> 
> 

Pour un ordinateur Windows autonome, vous pouvez éventuellement spécifier le compte et le mot de passe de l'administrateur local.

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

Choisissez un mot de passe fort. Pour en vérifier la force, consultez la page [Password Checker : Utilisation de mots de passe forts](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Pour ajouter l’ordinateur Windows à un domaine Active Directory existant, vous pouvez éventuellement spécifier le compte et le mot de passe de l’administrateur local, le domaine, ainsi que le nom et le mot de passe d’un compte de domaine.

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="<FQDN of the domain that the machine is joining>"
    $domacctdomain="<domain of the account that has permission to add the machine to the domain>"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

Pour connaître les autres options de préconfiguration disponibles pour les machines virtuelles Windows, consultez la syntaxe des jeux de paramètres **Windows** et **WindowsDomain** dans [Add-AzureProvisioningConfig](/powershell/module/azure/add-azureprovisioningconfig).

Vous pouvez éventuellement attribuer à la machine virtuelle une adresse IP spécifique, appelée DIP statique.

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Vous pouvez vérifier la disponibilité d'une adresse IP particulière à l'aide de la commande suivante :

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Vous pouvez éventuellement affecter la machine virtuelle à un sous-réseau spécifique dans un réseau virtuel Azure.

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Vous pouvez éventuellement ajouter un disque de données unique à la machine virtuelle.

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Pour un contrôleur de domaine Active Directory, définissez $hcaching sur « Aucun ».

Vous pouvez éventuellement ajouter la machine virtuelle à un jeu à charge équilibrée existant pour le trafic externe.

    $protocol="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Enfin, choisissez l'un de ces blocs de commande requis pour la création de la machine virtuelle.

Option 1 : créez la machine virtuelle dans un service cloud existant.

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

Le nom abrégé du service cloud est celui qui apparaît dans la liste Cloud Services dans le portail Azure ou dans la liste des groupes de ressources dans le portail Azure.

Option 2 : créez la machine virtuelle dans un service cloud et un réseau virtuel existants.

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## <a name="step-5-run-your-command-set"></a>Étape 5 : exécuter votre jeu de commandes
Passez en revue le jeu de commandes Azure PowerShell que vous avez créé dans votre éditeur de texte ou dans l’environnement d’écriture de scripts intégré de PowerShell (ISE), constitué de plusieurs blocs de commandes de l’étape 4. Vérifiez que vous avez spécifié toutes les variables nécessaires et qu’elles ont les valeurs correctes. Vérifiez également que vous avez supprimé tous les caractères < et >.

Si vous utilisez un éditeur de texte, copiez le jeu de commandes dans le Presse-papiers, puis cliquez avec le bouton droit sur votre invite de commandes Windows PowerShell ouverte. Vous émettez ainsi le jeu de commandes en tant que série de commandes PowerShell et créez votre machine virtuelle Azure. Vous pouvez également exécuter le jeu de commandes dans PowerShell ISE.

Si vous comptez créer cette machine virtuelle de nouveau ou une autre similaire, vous pouvez :

* Enregistrez ce jeu de commandes en tant que fichier de script PowerShell (*.ps1).
* Enregistrez ce jeu de commandes en tant que Runbook Azure Automation dans la section **Comptes Automation** du Portail Azure.

## <a id="examples"></a>Exemples
Voici deux exemples d'utilisation des étapes ci-dessus pour créer des jeux de commandes Azure PowerShell qui créent des machines virtuelles Azure basées sur Windows.

### <a name="example-1"></a>Exemple 1
J'ai besoin d'un jeu de commandes PowerShell permettant de créer la machine virtuelle initiale pour un contrôleur de domaine Active Directory qui :

* utilise l'image Windows Server 2012 R2 Datacenter
* se nomme AZDC1
* est un ordinateur autonome
* comporte un disque de données supplémentaire de 20 Go
* possède l'adresse IP statique 192.168.244.4
* se trouve dans le sous-réseau BackEnd du réseau virtuel AZDatacenter
* se trouve dans le service cloud Azure-TailspinToys.

Voici le jeu de commandes Azure PowerShell correspondant qui permet de créer cette machine virtuelle. Les lignes vides entre chaque bloc offrent une meilleure lisibilité.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### <a name="example-2"></a>Exemple 2
J'ai besoin d'un jeu de commandes PowerShell permettant de créer une machine virtuelle pour un serveur métier qui :

* utilise l'image Windows Server 2012 R2 Datacenter
* se nomme LOB1
* appartient au domaine corp.contoso.com
* comporte un disque de données supplémentaire de 200 Go
* se trouve dans le sous-réseau FrontEnd du réseau virtuel AZDatacenter
* se trouve dans le service cloud Azure-TailspinToys.

Voici le jeu de commandes Azure PowerShell correspondant qui permet de créer cette machine virtuelle.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="LOB1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="corp.contoso.com"
    $domacctdomain="CORP"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=200
    $disklabel="LOBData"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="next-steps"></a>Étapes suivantes
Si vous avez besoin d’un disque de système d’exploitation supérieur à 127 Go, vous pouvez [étendre ce dernier](../../virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

