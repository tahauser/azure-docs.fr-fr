---
title: "Déployer des modèles avec PowerShell dans Azure Stack | Microsoft Docs"
description: "Apprenez à déployer une machine virtuelle à l’aide d’un modèle Resource Manager et de PowerShell."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: d271b155d65a7dd95a92262da338cf3a272d140b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-in-azure-stack-using-powershell"></a>Déployer des modèles dans Azure Stack à l’aide de PowerShell

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Utilisez PowerShell pour déployer des modèles Azure Resource Manager dans le Kit de développement Azure Stack.  Les modèles Resource Manager déploient et approvisionnent toutes les ressources de l’application en une seule opération coordonnée.

## <a name="run-azurerm-powershell-cmdlets"></a>Exécuter les applets de commande AzureRM PowerShell
Dans cet exemple, vous exécutez un script pour déployer une machine virtuelle dans le Kit de développement Azure Stack à l’aide d’un modèle Resource Manager.  Avant de continuer, vérifiez que vous avez [configuré PowerShell](azure-stack-powershell-configure-user.md)  

Le disque dur virtuel utilisé dans cet exemple de modèle est WindowsServer-2012-R2-Datacenter.

1. Accédez à <http://aka.ms/AzureStackGitHub>, recherchez le modèle **101-simple-windows-vm** et enregistrez-le à l’emplacement suivant : c:\\templates\\ azuredeploy-101-simple-windows-vm.json.
2. Dans PowerShell, exécutez le script de déploiement suivant. Remplacez *username* et *password* par votre nom d’utilisateur et votre mot de passe. Pour les prochaines utilisations, augmentez la valeur du paramètre *$myNum* pour éviter d’écraser votre déploiement.
   
   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
       New-AzureRmResourceGroupDeployment `
           -Name myDeployment$myNum `
           -ResourceGroupName $RGName `
           -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
           -NewStorageAccountName mystorage$myNum `
           -DnsNameForPublicIP mydns$myNum `
           -AdminUsername <username> `
           -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -VmName myVM$myNum `
           -WindowsOSVersion 2012-R2-Datacenter
   ```
3. Ouvrir le portail Azure Stack, cliquez sur **Parcourir**, cliquez sur **Machines virtuelles** et recherchez votre nouvelle machine virtuelle (*myDeployment001*).


## <a name="next-steps"></a>Étapes suivantes
[Déployer des modèles avec Visual Studio](azure-stack-deploy-template-visual-studio.md)

