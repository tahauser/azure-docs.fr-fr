---
title: Créer une image personnalisée dans Azure DevTest Labs à partir d’un fichier de disque dur virtuel à l’aide de PowerShell | Microsoft Docs
description: Automatiser la création d’une image personnalisée dans Azure DevTest Labs à partir d’un fichier de disque dur virtuel avec PowerShell
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: v-craic
ms.openlocfilehash: 2c542f9d47eb8011a13bf7156430324c53d238ce
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Créer une image personnalisée à partir d’un fichier de disque dur virtuel avec PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Instructions pas à pas

La procédure suivante décrit comment créer une image personnalisée à partir d’un fichier de disque dur virtuel avec PowerShell :

1. À l’invite de PowerShell, connectez-vous à votre compte Azure avec l’appel suivant à l’applet de commande **Login-AzureRmAccount**.  
    
    ```PowerShell
    Login-AzureRmAccount
    ```

1.  Sélectionnez l’abonnement Azure souhaité en appelant l’applet de commande **Select-AzureRmSubscription**. Remplacez l’espace suivant réservé à la variable **$subscriptionId** par un ID d’abonnement Azure valide. 

    ```PowerShell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    ```

1.  Récupérez l’objet de laboratoire en appelant l’applet de commande **Get-AzureRmResource**. Remplacez les espaces suivants réservés aux variables **$labRg** et **$labName** par les valeurs appropriées pour votre environnement. 

    ```PowerShell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```
 
1.  Récupérez les valeurs de clé du compte de stockage et du compte de stockage du laboratoire dans l’objet de laboratoire. 

    ```PowerShell
    $labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
    $labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value
    ```

1.  Remplacez l’espace suivant réservé à la variable **$vhdUri** par l’URI vers le fichier VHD chargé. Vous pouvez obtenir l’URI du fichier de disque dur virtuel dans le panneau d’objet blob du compte de stockage, dans le portail Azure.

    ```PowerShell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Créez l’image personnalisée avec l’applet de commande **New-AzureRmResourceGroupDeployment**. Remplacez les espaces suivants réservés aux variables **$customImageName** et **$customImageDescription** par des noms significatifs pour votre environnement.

    ```PowerShell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Script PowerShell pour créer une image personnalisée à partir d’un fichier de disque dur virtuel

Le script PowerShell suivant peut être utilisé pour créer une image personnalisée à partir d’un fichier de disque dur virtuel. Remplacez les espaces réservés (entre crochets) par les valeurs adaptées à vos besoins. 

```PowerShell
# Log in to your Azure account.  
Login-AzureRmAccount

# Select the desired Azure subscription. 
$subscriptionId = '<Specify your subscription ID here>'
Select-AzureRmSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzureRmResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the lab storage account and lab storage account key values.
$labStorageAccount = Get-AzureRmResource -ResourceId $lab.Properties.defaultStorageAccount 
$labStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $labStorageAccount.ResourceGroupName -Name $labStorageAccount.ResourceName)[0].Value

# Set the URI of the VHD file.  
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image. 
New-AzureRmResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/Samples/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Billets de blog connexes

- [Custom images or formulas? (Images personnalisées ou formules ?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)
- [Copying Custom Images between Azure DevTest Labs (Copie d’images personnalisées entre plusieurs Azure DevTest Labs)](http://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Étapes suivantes

- [Ajout d’une machine virtuelle à votre laboratoire](devtest-lab-add-vm.md)
