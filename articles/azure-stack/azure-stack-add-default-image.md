---
title: "Ajouter l’image de machine virtuelle par défaut sur la Place de marché Azure Stack | Microsoft Docs"
description: "Ajoutez l’image de machine virtuelle Windows Server 2016 par défaut sur la Place de marché Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2849E53F-3D58-48A5-8007-3238FC39F630
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/23/2018
ms.author: mabrigg
ms.openlocfilehash: be4a61f185435238db68e4dc43c323a30a754f03
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Ajouter l’image de machine virtuelle Windows Server 2016 sur la Place de marché Azure Stack

Par défaut, aucune image de machine virtuelle n’est disponible sur la Place de marché Azure Stack. Un opérateur Azure Stack doit ajouter une image sur la Place de marché pour la mettre à la disposition des utilisateurs. Vous pouvez ajouter l’image Windows Server 2016 sur la Place de marché Azure Stack à l’aide d’une des méthodes suivantes :

* [Téléchargez une image à partir de la Place de Marché Azure](#add-the-image-by-downloading-it-from-the-azure-marketplace). Choisissez cette méthode si vous êtes dans un scénario connecté et si vous avez inscrit votre instance Azure Stack auprès d’Azure.

* [Ajouter l’image à l’aide de PowerShell](#add-the-image-by-using-powershell). Choisissez cette méthode si vous avez déployé Azure Stack dans un scénario déconnecté ou avec une connectivité limitée.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Ajouter l’image en la téléchargeant à partir de la Place de marché Azure

1. Déployez Azure Stack, puis connectez-vous à votre Kit de développement Azure Stack.

2. Sélectionnez **Plus de services** > **Gestion de la Place de Marché** > **Ajouter à partir de Azure**. 

3. Localisez ou recherchez l’image **Windows Server Datacenter 2016** puis sélectionnez **Télécharger**.

   ![Télécharger l’image à partir d’Azure](media/azure-stack-add-default-image/download-image.png)

Lorsque le téléchargement est terminé, l’image est disponible sous **Gestion de la Place de Marché**. L’image est également disponible sous **Calcul** et elle est disponible pour créer des machines virtuelles.

## <a name="add-the-image-by-using-powershell"></a>Ajouter l’image à l’aide de PowerShell

### <a name="prerequisites"></a>configuration requise 

Effectuez les étapes prérequises suivantes à partir du [kit de développement](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes [connecté via un VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) :

1. Installez les [modules Azure PowerShell compatibles avec Azure Stack](azure-stack-powershell-install.md).  

2. Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md).  

3. Dans la page Windows Server Evaluations, [téléchargez la version d’évaluation de Windows Server 2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016). Quand vous y êtes invité, sélectionnez la version ISO du téléchargement. Enregistrez le chemin d’accès à l’emplacement de téléchargement, car vous en aurez besoin plus tard au cours des étapes données dans cet article. Cette étape nécessite une connectivité Internet.  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>Ajouter l’image à la Place de Marché Azure Stack
   
1. Importez les modules Azure Stack Connect et ComputeAdmin à l’aide des commandes suivantes :

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Connectez-vous à votre environnement Azure Stack. Exécutez l’un des scripts suivants, en fonction du déploiement de votre environnement Azure Stack (à l’aide de Azure Active Directory ou bien avec Active Directory Federation Services). (Remplacez le `tenantName` de Azure AD, le point de terminaison `GraphAudience`, et les valeurs `ArmEndpoint` pour refléter la configuration de votre environnement.)  

   * **Azure Active Directory**. Utilisez l’applet de commande suivante :

    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"
    
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```

   * **Active Directory Federation Services**. Utilisez l’applet de commande suivante :
    
    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"

    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience `
      -EnableAdfsAuthentication:$true

    $TenantID = Get-AzsDirectoryTenantId `
     -ADFS `
     -EnvironmentName "AzureStackAdmin" 

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```
   
3. Ajoutez l’image Windows Server 2016 sur la Place de marché Azure Stack. (Remplacez *fully_qualified_path_to_ISO* avec le chemin d’accès vers l’image ISO de Windows Server 2016 que vous avez téléchargé.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

Pour vous assurer que l’image de machine virtuelle Windows Server 2016 contient la dernière mise à jour cumulative, incluez le paramètre `IncludeLatestCU` lorsque vous exécutez l’applet de commande `New-AzsServer2016VMImage`. Pour plus d’informations sur les paramètres autorisés dans l’applet de commande `New-AzsServer2016VMImage`, consultez [Paramètres](#parameters). La publication de l’image sur la Place de marché Azure Stack prend environ une heure. 

## <a name="parameters-for-new-azsserver2016vmimage"></a>Paramètres de New-AzsServer2016VMImage

### <a name="new-azsserver2016vmimage"></a>New-AzsServer2016VMImage 

Crée et charge un nouveau Server 2016 Core et/ou une image complète et crée un élément du marketplace pour celui-ci.

| parameters | Obligatoire | exemples | Description |
|-----|-----|------|---- |
|ISOPath|OUI| N:\ISO\en_windows_16_x64_dvd | Spécifie le chemin d’accès complet à l’image ISO Windows Server 2016 téléchargée.|
|Net35|Non | True | Installe le runtime .NET 3.5 sur l’image Windows Server 2016. Par défaut, cette valeur est définie sur **true**.|
|Version|Non | Complet |  Spécifie les images de Windows Server 2016 **Core**, **Complète**, ou **les deux**. Par défaut, cette valeur est définie sur **Complète**.|
|VHDSizeInMB|Non | 40,960 | Définit la taille (en Mo) de l’image VHD à ajouter à votre environnement Azure Stack. Par défaut, cette valeur est définie sur 40,960 Mo.|
|CreateGalleryItem|Non | True | Spécifie si un élément de la Place de marché doit être créé pour l’image Windows Server 2016. Par défaut, cette valeur est définie sur **true**.|
|location |Non  | D:\ | Spécifie l’emplacement de publication de l’image Windows Server 2016.|
|IncludeLatestCU|Non | False | Applique la dernière mise à jour cumulative de Windows Server 2016 à la nouvelle image VHD. Vérifiez le script pour vous assurer qu’il pointe vers la dernière mise à jour ou utilisez l’une des deux options suivantes. |
|CUUri |Non  | https://yourupdateserver/winservupdate2016 | Choisit la mise à jour cumulative de Windows Server 2016 à exécuter à partir d’un URI spécifique. |
|CUPath |Non  | C:\winservupdate2016 | Choisit la mise à jour cumulative de Windows Server 2016 à exécuter à partir d’un chemin d’accès local. Ce paramètre est utile si vous avez déployé l’instance Azure Stack dans un environnement déconnecté.|

## <a name="next-steps"></a>étapes suivantes

[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)
