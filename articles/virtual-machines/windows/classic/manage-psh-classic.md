---
title: "Gérer vos machines virtuelles à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Découvrez les commandes que vous pouvez utiliser pour automatiser les tâches de gestion des machines virtuelles."
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: timlt
editor: 
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
ms.openlocfilehash: 88ab031778957f20dd42955c2c1311d731e2f588
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Gérer vos machines virtuelles à l’aide d’Azure PowerShell
> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour connaître les commandes PowerShell courantes avec le modèle Resource Manager, consultez [cet article](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Il est possible d’automatiser les nombreuses tâches quotidiennes liées à la gestion de vos machines virtuelles en utilisant les applets de commande Azure PowerShell. Cet article donne des exemples de commandes pour réaliser des tâches simples et contient des liens vers des articles indiquant les commandes à utiliser pour des tâches plus complexes.

> [!NOTE]
> Si vous n’avez pas installé et configuré Azure PowerShell, vous pouvez obtenir des instructions dans l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Utilisation des exemples de commandes
Vous devrez remplacer une partie du texte des commandes par un texte approprié à votre environnement. Les symboles < et > indiquent le texte à remplacer. Lorsque vous remplacez le texte, supprimez les symboles, mais laissez les guillemets en place.

## <a name="get-a-vm"></a>Obtenir une machine virtuelle
Il s’agit d’une tâche de base que vous utiliserez souvent. Utilisez-la pour obtenir des informations sur une machine virtuelle, effectuer des tâches sur cette dernière ou pour obtenir un résultat à stocker dans une variable.

Pour obtenir des informations sur la machine virtuelle, exécutez cette commande en remplaçant tous les éléments entre guillemets notamment les caractères < et > :

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Pour stocker le résultat dans une variable $vm, exécutez :

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Ouvrir une session sur une machine virtuelle Windows
Exécutez ces commandes :

> [!NOTE]
> Vous pouvez obtenir le nom du service cloud et de la machine virtuelle à partir de l’affichage de la commande **Get-AzureVM** .
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "<emplacement du lecteur et du dossier pour stocker le fichier RDP téléchargé, par exemple : C:\temp >" $localFile = $localPath + "\" + $vmname + ".rdp" Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch
> 
> 

## <a name="stop-a-vm"></a>Arrêter une machine virtuelle
Exécutez cette commande :

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Utilisez ce paramètre pour conserver l’adresse IP virtuelle du service cloud s’il s’agit de la dernière machine virtuelle de ce service. <br><br> Si vous utilisez le paramètre StayProvisioned, vous êtes toujours facturé pour cette machine virtuelle.
> 
> 

## <a name="start-a-vm"></a>Démarrer une machine virtuelle
Exécutez cette commande :

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Association d’un disque de données
Cette tâche nécessite de réaliser quelques étapes. Tout d’abord, utilisez l’applet de commande ****Add-AzureDataDisk**** pour ajouter le disque à l’objet $vm. Utilisez ensuite l’applet de commande **Update-AzureVM** pour mettre à jour la configuration de la machine virtuelle.

Vous devez également décider d’associer un nouveau disque ou un disque existant, qui contient des données. Dans le cas d’un nouveau disque, la commande entraîne la création du fichier .vhd et son association.

Pour associer un nouveau disque, exécutez cette commande :

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Pour associer un disque existant, exécutez cette commande :

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Pour attacher des disques de données à partir d’un fichier .vhd existant dans le stockage d’objets blob, exécutez la commande suivante :

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Créer une machine virtuelle Windows
Pour créer une nouvelle machine virtuelle Windows dans Azure, consultez [Utilisation d’Azure PowerShell pour créer et préconfigurer des machines virtuelles Windows](create-powershell.md). Cette rubrique vous guide lors de la création d’un jeu de commandes Azure PowerShell permettant de créer une machine virtuelle Windows pouvant être préconfigurée avec :

* une appartenance au domaine Active Directory ;
* des disques supplémentaires ;
* une appartenance à un jeu d’équilibrage de la charge ;
* une adresse IP statique.

