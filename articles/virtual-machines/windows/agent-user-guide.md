---
title: "Vue d’ensemble d’agent de machine virtuelle Azure | Microsoft Docs"
description: "Vue d’ensemble d’agent de machine virtuelle Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: danielsollondon
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: danis
ms.openlocfilehash: 3be001c2ad63c5872af4740f136438034e1e406b
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="azure-virtual-machine-agent-overview"></a>Vue d’ensemble d’agent de machine virtuelle Azure

L’agent de machine virtuelle Microsoft Azure (Agent VM) est un processus léger sécurisé qui gère l’interaction des machines virtuelles avec le contrôleur de structure Azure. L’agent de machine virtuelle a un rôle essentiel dans l’activation et l’exécution des extensions de machine virtuelle. Extensions de machine virtuelle permettant la configuration post-déploiement de machines virtuelles, comme l’installation et la configuration de logiciels. Les extensions de machine virtuelle permettent également d’utiliser des fonctionnalités de récupération, telles que la réinitialisation du mot de passe d’administration d’une machine virtuelle. Sans l’agent de machine virtuelle Azure, les extensions de machine virtuelle ne peuvent pas être exécutées.

Ce document détaille l’installation, la détection et la suppression de l’agent de machine virtuelle Azure.

## <a name="install-the-vm-agent"></a>Installer l'agent de machine virtuelle

### <a name="azure-gallery-image"></a>Image de la galerie Azure

L’agent de machine virtuelle Azure est installé par défaut sur les machines virtuelles Windows déployées à partir d’une image de la galerie Azure. Lorsque vous déployez une image de la galerie Azure à partir du portail, de PowerShell, de l’interface de ligne de commande ou d’un modèle Azure Resource Manager, l’agent de machine virtuelle Azure est également installé. 

### <a name="manual-installation"></a>Installation manuelle

L’agent de machine virtuelle Windows peut être installé manuellement à l’aide d’un package Windows installer. Une installation manuelle peut être nécessaire lors de la création d’une image de machine virtuelle qui sera déployée dans Azure. Pour installer manuellement l’agent de machine virtuelle Windows, téléchargez le programme d’installation de l’agent de machine virtuelle à partir de cet emplacement : [Téléchargement de l’agent de machine virtuelle Windows Azure](http://go.microsoft.com/fwlink/?LinkID=394789). 

L’agent de machine virtuelle peut être installé en double-cliquant sur le fichier Windows Installer. Pour une installation automatisée ou sans assistance de l’agent de machine virtuelle, exécutez la commande suivante.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Détecter l’agent de machine virtuelle

### <a name="powershell"></a>PowerShell

Le module PowerShell Azure Resource Manager peut être utilisé pour récupérer des informations sur les machines virtuelles Azure. L’exécution de `Get-AzureRmVM` renvoie de nombreuses informations, dont l’état d’approvisionnement de l’agent de machine virtuelle Azure.

```PowerShell
Get-AzureRmVM
```

Ce qui suit n’est qu’un extrait de la sortie `Get-AzureRmVM`. Notez la propriété `ProvisionVMAgent` imbriquée dans `OSProfile`. Cette propriété peut être utilisée pour déterminer si l’agent de machine virtuelle a été déployé sur la machine virtuelle.

```PowerShell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : muUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Le script suivant peut être utilisé pour renvoyer une liste concise des noms des machines virtuelles et l’état de l’agent de machine virtuelle.

```PowerShell
$vms = Get-AzureRmVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Détection manuelle

Lorsque vous vous connectez à une machine virtuelle Windows Azure, le gestionnaire des tâches peut servir à examiner les processus en cours d’exécution. Pour vérifier l’agent de machine virtuelle Azure, ouvrez le Gestionnaire des tâches > cliquez sur l’onglet Détails et recherchez le nom de processus `WindowsAzureGuestAgent.exe`. La présence de ce processus indique que l’agent de machine virtuelle est installé.

## <a name="upgrade-the-vm-agent"></a>Mettre à niveau l’agent de machine virtuelle

L’agent de machine virtuelle Azure est automatiquement mis à niveau. Lorsque de nouvelles machines virtuelles sont déployées sur Azure, elles reçoivent le dernier agent de machine virtuelle. Les images de machine virtuelle personnalisées doivent être manuellement mises à jour pour inclure le nouvel agent de machine virtuelle.
