---
title: "Ajouter une image de machine virtuelle à Azure Stack | Microsoft Docs"
description: "Ajouter une image de machine virtuelle Windows ou Linux personnalisée de votre organisation à utiliser par les locataires."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/17/2018
ms.author: mabrigg
ms.openlocfilehash: 0ba0bc4e8350a65a95dc41788c93d5c89fc48334
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2018
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Mettre une image de machine virtuelle personnalisée à la disposition des utilisateurs dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Dans Azure Stack, les opérateurs peuvent mettre des images de machine virtuelle personnalisées à la disposition de leurs utilisateurs. Ces images peuvent être référencées par des modèles Azure Resource Manager ou vous pouvez les ajouter à l’interface utilisateur de la Place de marché Azure en tant qu’élément sur la Place de marché.

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>Ajouter une image de machine virtuelle sur la Place de marché à l’aide de PowerShell

Effectuez les étapes prérequises suivantes à partir du [kit de développement](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou à partir d’un client externe Windows si vous êtes [connecté via un VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) :

1. [Installez PowerShell pour Azure Stack](azure-stack-powershell-install.md).  

2. Téléchargez les [outils nécessaires pour utiliser Azure Stack](azure-stack-powershell-download.md).  

3. Préparez une image de disque dur virtuel du système d’exploitation Windows ou Linux au format VHD (n’utilisez pas le format VHDX).

   * Pour obtenir des instructions sur la préparation des images Windows, consultez [Télécharger une image de machine virtuelle Windows dans Azure pour les déploiements Resource Manager](../virtual-machines/windows/upload-generalized-managed.md).

   * Pour les images Linux, consultez [Déployer des machines virtuelles Linux sur Azure Stack](azure-stack-linux.md). Effectuez les étapes pour préparer l’image ou bien utilisez une image Linux Azure Stack existante comme décrit dans l’article.    

   Azure Stack prend en charge le format VHD de disque fixe. Le format fixe structure linéairement le disque logique dans le fichier de façon que l’offset du disque X soit stocké à l’offset de l’objet blob X. Un petit pied de page à la fin de l’objet blob décrit les propriétés du disque dur virtuel. Pour vérifier si votre disque est fixe, utilisez la commande PowerShell [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps).  

   > [!IMPORTANT]
   >  Azure Stack ne prend pas en charge les disques durs virtuels de disque dynamique. Le redimensionnement d’un disque dynamique attaché à une machine virtuelle laissera la machine virtuelle dans un état d’échec. Pour résoudre ce problème, supprimez la machine virtuelle sans supprimer le disque de la machine virtuelle, un objet blob VHD dans un compte de stockage. Ensuite, convertissez le disque dur virtuel d’un disque dynamique en disque fixe et recréez la machine virtuelle.

Pour ajouter l’image sur la Place de marché Azure Stack, effectuez les étapes suivantes :

1. Importez les modules Connect et ComputeAdmin :

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
          -EnvironmentName AzureStackAdmin

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID
        ```

3. Ajoutez l’image de machine virtuelle en appelant l’applet de commande `Add-AzsVMImage`. Dans l’applet de commande `Add-AzsVMImage`, spécifiez `osType` comme Windows ou Linux. Indiquez l’éditeur (« publisher »), l’offre (« offer »), la référence SKU (« sku ») et la version pour l’image de machine virtuelle. Pour plus d’informations sur les paramètres autorisés, consultez la section [Paramètres](#parameters). Les paramètres sont utilisés par les modèles Azure Resource Manager pour référencer l’image de machine virtuelle. L’exemple suivant appelle le script :

  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```

La commande exécute les actions suivantes :

* Elle effectue l’authentification auprès de l’environnement Azure Stack.
* Elle charge le disque VHD local dans un nouveau compte de stockage temporaire.
* Elle ajoute l’image de machine virtuelle dans le référentiel d’images de machine virtuelle.
* Elle crée un élément de Place de marché.

Pour vérifier que la commande s’est exécutée correctement, accédez à la Place de Marché dans le portail. Vérifiez que l’image de machine virtuelle est disponible dans le catégorie **Machines virtuelles**.

![Image de machine virtuelle correctement ajoutée](./media/azure-stack-add-vm-image/image5.PNG)

## <a name="remove-a-vm-image-by-using-powershell"></a>Supprimer une image de machine virtuelle à l’aide de PowerShell

Si vous n’avez plus besoin de l’image de machine virtuelle que vous avez chargée, vous pouvez la supprimer sur la Place de marché à l’aide de l’applet de commande suivante :

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>parameters

| Paramètre | Description |
| --- | --- |
| **publisher** |Segment du nom de l’éditeur de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Microsoft** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ. |
| **offer** |Segment du nom de l’offre de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **WindowsServer** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ. |
| **sku** |Segment du nom de référence SKU de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. **Datacenter2016** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ. |
| **version** |Version de l’image de machine virtuelle que les utilisateurs indiquent lors du déploiement de l’image. La version suit le format *\#.\#.\#*, **1.0.0** est un exemple. N’incluez aucun espace ou autre caractère spécial dans ce champ. |
| **osType** |Type d’exploitation de l’image, qui doit être **Windows** ou **Linux**. |
| **osDiskLocalPath** |Chemin d’accès local au disque VHD de système d’exploitation que vous chargez comme image de machine virtuelle dans Azure Stack. |
| **dataDiskLocalPaths** |Tableau facultatif des chemins d’accès locaux des disques de données qui peuvent être chargés comme partie de l’image de machine virtuelle. |
| **CreateGalleryItem** |Indicateur booléen qui détermine s’il faut créer un élément sur la Place de marché. Par défaut, il est défini sur **true**. |
| **title** |Nom d’affichage de l’élément de la Place de marché. Par défaut, il se compose de la valeur `Publisher-Offer-Sku` de l’image de machine virtuelle. |
| **description** |Description de l’élément de la Place de marché. |
| **location** |Emplacement de publication de l’image de machine virtuelle. Par défaut, cette valeur est définie sur **local**.|
| **osDiskBlobURI** |(Facultatif) Ce script accepte également un URI de stockage Blob pour `osDisk`. |
| **dataDiskBlobURIs** |(Facultatif) Ce script accepte également un tableau d’URI de stockage Blob pour l’ajout de disques de données à l’image. |

## <a name="add-a-vm-image-through-the-portal"></a>Ajouter une image de machine virtuelle par le biais du portail

> [!NOTE]
> Avec cette méthode, vous devez créer l’élément de la Place de marché séparément.

Les images doivent être en mesure d’être référencées par un URI de stockage Blob. Préparez une image de système d’exploitation Windows ou Linux au format VHD (pas VHDX), puis chargez cette image dans un compte de stockage Azure ou Azure Stack. Si votre image est déjà chargée dans le stockage Blob dans Azure ou Azure Stack, ignorez l’étape 1.

1. [Chargez une image de machine virtuelle Windows dans Azure pour les déploiements Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) ou, pour une image Linux, suivez les instructions fournies dans [Déployer des machines virtuelles Linux dans Azure Stack](azure-stack-linux.md). Avant de télécharger l’image, il est important de tenir compte des facteurs suivants :

   * Azure Stack prend en charge le format VHD de disque fixe. Le format fixe structure linéairement le disque logique dans le fichier de façon que l’offset du disque X soit stocké à l’offset de l’objet blob X. Un petit pied de page à la fin de l’objet blob décrit les propriétés du disque dur virtuel. Pour vérifier si votre disque est fixe, utilisez la commande PowerShell [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps).  

    > [!IMPORTANT]
   >  Azure Stack ne prend pas en charge les disques durs virtuels de disque dynamique. Le redimensionnement d’un disque dynamique attaché à une machine virtuelle laissera la machine virtuelle dans un état d’échec. Pour résoudre ce problème, supprimez la machine virtuelle sans supprimer le disque de la machine virtuelle, un objet blob VHD dans un compte de stockage. Ensuite, convertissez le disque dur virtuel d’un disque dynamique en un disque fixe et recréez la machine virtuelle.

   * Il est préférable de charger une image dans le stockage Blob Azure Stack plutôt que dans le stockage Blob Azure, car la publication d’une image dans le référentiel d’images Azure Stack est plus rapide.

   * Quand vous chargez [l’image de machine virtuelle Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), pensez à substituer l’étape **Se connecter à Azure** par l’étape [Configurer l’environnement PowerShell de l’opérateur Azure Stack](azure-stack-powershell-configure-admin.md).  

   * Prenez note de l’URI de stockage Blob dans lequel vous chargez l’image. L’URI de stockage Blob est au format suivant : *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd.

   * Pour rendre le stockage Blob accessible de manière anonyme, accédez au conteneur d’objets blob du compte de stockage dans lequel l’image de machine virtuelle VHD a été chargée. Sélectionnez **Blob** puis choisissez **Stratégie d’accès**. Si vous le souhaitez, vous pouvez à la place générer une signature d’accès partagé pour le conteneur et l’inclure dans l’URI de l’objet blob.

   ![Accéder aux objets blob du compte de stockage](./media/azure-stack-add-vm-image/image1.png)

   ![Définir un accès public pour les objets blob](./media/azure-stack-add-vm-image/image2.png)

2. Connectez-vous au Azure Stack en tant qu’opérateur. Dans le menu, sélectionnez sur **Autres services** > **Fournisseurs de ressources**. Ensuite, sélectionnez **Compute** > **Images de machine virtuelle** > **Ajouter**.

3. Dans le panneau **Ajouter une image de machine virtuelle**, saisissez l’éditeur, l’offre, la référence SKU et la version de l’image de machine virtuelle. Ces segments de nom référencent l’image de machine virtuelle dans les modèles Resource Manager. Sélectionnez la valeur **type de système d’exploitation** approprié. Pour **URI de l’objet blob OS Disk**, saisissez l’URI de l’objet Blob où l’image a été téléchargée. Cliquez ensuite sur **Créer** pour commencer la création d’une image de machine virtuelle.

   ![Démarrer la création de l’image](./media/azure-stack-add-vm-image/image4.png)

   Une fois que l’image de machine virtuelle a été créée, son état passe à **Réussi**.

4. Pour rendre une image de machine virtuelle plus rapidement disponible dans l’interface utilisateur, c’est une bonne idée de [créer un élément de Place de marché](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Étapes suivantes

[Approvisionner une machine virtuelle](azure-stack-provision-vm.md)
