---
title: "Créer et charger une image de machine virtuelle à l’aide de PowerShell | Microsoft Docs"
description: "Découvrez comment créer et télécharger une image Windows Server (VHD) généralisée à l’aide du modèle de déploiement classique et d’Azure Powershell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: fa672fd7811e68368c311ef6a3f57eac4b240a4a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Création et téléchargement d’un disque dur virtuel Windows Server dans Azure
Cet article vous montre comment télécharger votre propre image de machine virtuelle généralisée afin de l’utiliser comme disque dur virtuel (VHD) pour créer des machines virtuelles. Pour en savoir plus sur les disques et les disques durs virtuels dans Microsoft Azure, consultez [À propos des disques et VHD pour machines virtuelles](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Vous pouvez également [charger](../upload-generalized-managed.md) une machine virtuelle à l’aide du modèle Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="prerequisites"></a>Composants requis
Cet article suppose que vous disposez de :

* **Un abonnement Azure** : si vous n’en avez pas, vous pouvez [ouvrir un compte Azure gratuitement](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* **[Microsoft Azure PowerShell](/powershell/azure/overview)** : le module Microsoft Azure PowerShell est installé et configuré de façon à utiliser votre abonnement.
* **Un fichier .VHD** : système d’exploitation Windows pris en charge stocké dans un fichier .vhd et associé à une machine virtuelle. Vérifiez si les rôles serveur en cours d’exécution sur le disque dur virtuel sont pris en charge par Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

    > [!IMPORTANT]
    > Microsoft Azure ne prend pas en charge le format VHDX. Vous pouvez convertir le disque au format VHD à l’aide de Hyper-V Manager ou de la [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Pour plus de détail, voir [ce billet de blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Étape 1 : Préparer le disque dur virtuel
Avant de télécharger le disque dur virtuel vers Azure, vous devez le généraliser à l’aide de l’outil Sysprep. Cette opération prépare le disque dur virtuel à utiliser en tant qu’image. Pour plus d’informations sur Sysprep, voir [Introduction à l’utilisation de Sysprep](http://technet.microsoft.com/library/bb457073.aspx). Sauvegardez la machine virtuelle avant d’exécuter Sysprep.

Depuis la machine virtuelle sur laquelle le système d’exploitation a été installé, effectuez la procédure suivante :

1. Connectez-vous au système d’exploitation.
2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur. Remplacez le répertoire par **%windir%\system32\sysprep**, puis exécutez `sysprep.exe`.

    ![Ouvrir une fenêtre d’invite de commandes](./media/createupload-vhd/sysprep_commandprompt.png)
3. La boîte de dialogue **Outil de préparation système** apparaît.

   ![Démarrer Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. Dans **Outil de préparation du système**, sélectionnez **Entrer en mode OOBE (Out-of-Box Experience)** et vérifiez que la case à cocher **Généraliser** est activée.
5. Dans **Options d’arrêt**, sélectionnez **Arrêter**.
6. Cliquez sur **OK**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Étape 2 : Créer un compte de stockage et un conteneur
Vous avez besoin d’un compte de stockage dans Azure afin d’avoir un emplacement pour télécharger le fichier .vhd. Cette étape vous montre comment créer un compte, ou obtenir les informations dont vous avez besoin d’un compte existant. Remplacez les variables entre &lsaquo; chevrons &rsaquo; par vos propres informations.

1. Connexion

    ```powershell
    Add-AzureAccount
    ```

2. Définissez votre abonnement Azure.

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. Créez un nouveau compte de stockage. Le nom du compte de stockage devrait être unique et composé de 3 à 24 caractères. Vous pouvez utiliser n’importe quelle combinaison de lettres et de chiffres. Vous devez également spécifier un emplacement comme « États-Unis de l'Est »

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. Définissez le nouveau compte de stockage comme compte par défaut.

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. Créez un conteneur.

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>Étape 3 : téléchargement du fichier .vhd
Utilisez l’applet de commande [Add-AzureVhd](https://docs.microsoft.com/powershell/module/azure/add-azurevhd) pour charger le fichier VHD.

Dans la fenêtre Azure PowerShell que vous avez utilisée à l’étape précédente, tapez la commande suivante et remplacez les variables entre &lsaquo; chevrons &rsaquo; par vos propres informations.

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Étape 4 : ajout de l’image à votre liste d’images personnalisées
Utilisez l’applet de commande [Add-AzureVMImage](https://docs.microsoft.com/powershell/module/azure/add-azurevmimage) pour ajouter l’image à la liste de vos images personnalisées.

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez à présent [créer une machine virtuelle personnalisée](createportal.md) à l’aide de l’image que vous avez chargée.
