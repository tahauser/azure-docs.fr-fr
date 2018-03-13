---
title: "Créer une machine virtuelle SQL Server dans Azure PowerShell (classique) | Microsoft Docs"
description: "Fournit une procédure et des scripts PowerShell pour la création d’une machine virtuelle Azure à l’aide des images de la galerie de machines virtuelles SQL Server. Cette rubrique utilise le modèle de déploiement classique."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.openlocfilehash: 66f44e27562f33373e0b67fe6e0ebf9c6bf99e03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Approvisionner une machine virtuelle SQL Server à l’aide d’Azure PowerShell (Classic)

Cet article explique comment créer une machine virtuelle SQL Server dans Azure à l'aide des applets de commande PowerShell.

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../azure-resource-manager/resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.

Pour obtenir la version Resource Manager de cette rubrique, consultez [Approvisionner une machine virtuelle SQL Server à l’aide d’Azure PowerShell (Resource Manager)](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Installation et configuration de PowerShell :
1. Si vous n'avez pas de compte Azure, visitez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Téléchargez et installez les commandes de la version la plus récente d’Azure PowerShell](/powershell/azure/overview).
3. Démarrez Windows PowerShell, puis connectez-le à votre abonnement Azure avec la commande **Add-AzureAccount** .

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Déterminer votre région Azure cible

Votre machine virtuelle SQL Server est hébergée dans un service cloud qui se trouve dans une région Azure spécifique. Les étapes suivantes vous aident à déterminer votre région, le compte de stockage et le service cloud qui seront utilisés pour le reste du didacticiel.

1. Déterminez le centre de données que vous souhaitez utiliser pour héberger votre machine virtuelle SQL Server. La commande PowerShell suivante affiche une liste des noms de régions disponibles.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. Une fois que vous avez identifié l’emplacement par défaut, définissez une variable nommée **$dcLocation** pour cette région. Par exemple, la commande suivante définit la région sur « Est des États-Unis » :

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Configuration de votre compte d'abonnement et de stockage

1. Déterminez l’abonnement Azure que vous allez utiliser pour la nouvelle machine virtuelle.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Attribuez la variable **$subscr** à votre abonnement Azure cible. Définissez cet abonnement comme votre abonnement Azure actuel.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Recherchez ensuite des comptes de stockage. Le script suivant affiche tous les comptes de stockage qui existent dans votre région choisie :

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Si vous avez besoin d’un compte de stockage, commencez par utiliser la commande New-AzureStorageAccount pour créer un nom de compte de stockage (intégralement en minuscules), comme dans l’exemple suivant : `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Attribuez le nom de compte de stockage cible à **$staccount**. Utilisez ensuite **Set-AzureSubscription** pour définir l’abonnement et le compte de stockage actif.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Sélectionner une image de machine virtuelle SQL Server

1. Consultez la liste des images de machines virtuelles SQL Server disponibles dans la galerie. Ces images ont toutes la propriété **ImageFamily** qui commence par « SQL ». La requête suivante affiche la famille d’images disponible pour vous, pour laquelle SQL Server est pré-installé.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. Il est possible que plusieurs images soient publiées dans la famille d’images de machine virtuelle que vous avez trouvée. Utilisez le script suivant pour rechercher le nom de la dernière image de machine virtuelle publiée pour la famille d’images que vous avez sélectionnée (par exemple, **SQL Server 2016 RTM Enterprise sur Windows Server 2012 R2**) :

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Créer la machine virtuelle

Pour finir, créez la machine virtuelle avec PowerShell :

1. Créez un service cloud pour héberger la nouvelle machine virtuelle. Notez que vous pouvez également utiliser un service cloud existant. Créez une variable **$svcname** portant le nom abrégé du service cloud.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Indiquez le nom et la taille de la machine virtuelle. Pour plus d’informations sur les tailles de machines virtuelles, voir [Tailles de machines virtuelles pour Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Indiquez le compte d’administrateur local et son mot de passe.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Exécutez le script suivant pour créer la machine virtuelle.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Pour consulter d’autres options de configuration ainsi que des explications supplémentaires, lisez la section **Création de votre jeu de commandes** sur la page [Utilisation d’Azure PowerShell pour créer et pré-configurer des machines virtuelles basées sur Windows](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Exemple de script PowerShell

Le script suivant est un exemple de script complet qui crée une machine virtuelle **SQL Server 2016 RTM Enterprise sur Windows Server 2012 R2** . Si vous utilisez ce script, vous devez personnaliser les variables initiales en vous basant sur les étapes précédentes de cette rubrique.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Connexion avec le Bureau à distance

1. Créez les fichiers .RDP dans le dossier Documents de l’utilisateur actuel pour démarrer ces machines virtuelles afin de terminer l’installation :

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. Dans le répertoire Documents, exécutez le fichier RDP. Connectez-vous avec le nom d’utilisateur et le mot de passe du compte d’administrateur fournis précédemment (par exemple, si votre nom d’utilisateur était VMAdmin, entrez « \VMAdmin » comme nom d’utilisateur, puis entrez le mot de passe).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Terminer la configuration de la machine virtuelle SQL Server pour l’accès distant

Une fois que vous vous êtes connecté à la machine à l’aide du Bureau à distance, configurez SQL Server en vous basant sur les instructions de la [procédure de configuration de la connectivité SQL Server dans une machine virtuelle Azure](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez des instructions supplémentaires sur l’approvisionnement des machines virtuelles avec PowerShell dans la [documentation sur les machines virtuelles](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Dans de nombreux cas, l’étape suivante consiste à migrer vos bases de données vers cette nouvelle machine virtuelle SQL Server. Pour obtenir de l’aide sur la migration des bases de données, consultez [Migration d’une base de données vers un serveur SQL Server sur une machine virtuelle Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Si vous voulez également savoir comment utiliser le portail Azure pour créer des machines virtuelles SQL, consultez [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Notez que le didacticiel qui vous guide à travers le portail crée des machines virtuelles en utilisant le modèle Resource Manager recommandé, plutôt que le modèle classique utilisé dans cette rubrique PowerShell.

Outre ces ressources, nous vous recommandons de consulter [les autres rubriques liées à l’exécution de SQL Server dans Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
