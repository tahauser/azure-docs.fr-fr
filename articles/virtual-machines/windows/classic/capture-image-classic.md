---
title: "Capture d’une image de machine virtuelle Microsoft Azure | Microsoft Docs"
description: "Capturer l’image d’une machine virtuelle Microsoft Azure créée avec le modèle de déploiement classique"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: a5986eac-4cf3-40bd-9b79-7c811806b880
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: ee6371faa9c3cc72104ba0eb05d17226679fa420
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="capture-an-image-of-an-azure-windows-virtual-machine-created-with-the-classic-deployment-model"></a>Capturer l’image d’une machine virtuelle Microsoft Azure créée avec le modèle de déploiement classique
> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations sur le modèle Azure Resource Manager, voir [Capturer une image managée d’une machine virtuelle généralisée dans Azure](../capture-image-resource.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Cet article vous montre comment capturer une machine virtuelle Azure exécutant Windows de façon à l’utiliser comme image pour créer d’autres machines virtuelles. Cette image contient le disque du système d’exploitation et les disques de données éventuellement associés à la machine virtuelle. Comme elle ne comporte pas de configurations de mise en réseau, vous devrez établir des configurations réseau quand vous créez les autres machines virtuelles à partir de l’image.

Azure stocke l’image sous **Images de machine virtuelle (classic)**, un service **Compute** répertorié lorsque vous affichez tous les services Azure. Il s’agit de l’emplacement où sont stockées les images que vous avez éventuellement téléchargées. Pour plus d’informations sur les images, consultez la page [À propos des images pour les machines virtuelles](about-images.md?toc=%2fazure%2fvirtual-machines%2fWindows%2fclassic%2ftoc.json).

## <a name="before-you-begin"></a>Avant de commencer
Ces étapes partent du principe que vous avez déjà créé une machine virtuelle Azure, configuré le système d’exploitation et attaché les disques de données. Si vous ne l’avez pas encore fait, consultez les articles suivants pour obtenir plus d’informations sur la création et la préparation de la machine virtuelle :

* [Création d’une machine virtuelle à partir d’une image](createportal.md)
* [Comment attacher un disque de données à une machine virtuelle](attach-disk.md)
* Assurez-vous que les rôles serveur sont pris en charge avec Sysprep. Pour plus d’informations, consultez [Prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!WARNING]
> Ce processus supprime la machine virtuelle d’origine une fois celle-ci capturée.
>
>

Avant de capturer une image d’une machine virtuelle Azure, nous vous recommandons de sauvegarder la machine virtuelle cible. Les machines virtuelles Azure peuvent être sauvegardées à l’aide d’Azure Backup. Pour plus d’informations, voir [Sauvegarde des machines virtuelles Azure](../../../backup/backup-azure-arm-vms.md). D’autres solutions sont disponibles auprès de partenaires certifiés. Pour savoir ce qui est actuellement disponible, faites une recherche dans Azure Marketplace.

## <a name="capture-the-virtual-machine"></a>Capture de la machine virtuelle
1. Dans le [portail Azure](http://portal.azure.com), **connectez-vous** à la machine virtuelle. Pour obtenir des instructions, consultez la rubrique [Comment se connecter à une machine virtuelle exécutant Windows Server][How to sign in to a virtual machine running Windows Server].
2. Ouvrez une fenêtre d’invite de commandes en tant qu’administrateur.
3. Accédez au répertoire `%windir%\system32\sysprep`, puis exécutez sysprep.exe.
4. La boîte de dialogue **Outil de préparation système** apparaît. Effectuez les actions suivantes :

   * Dans **Action de nettoyage du système**, sélectionnez **Entrer en mode OOBE (OOBE)** et vérifiez que la case à cocher **Généraliser** est activée. Pour plus d’informations sur l’utilisation de Sysprep, consultez la page [Introduction à l’utilisation de Sysprep][How to Use Sysprep: An Introduction].
   * Dans **Options d’arrêt**, sélectionnez **Arrêter**.
   * Cliquez sur **OK**.

   ![Exécutez Sysprep](./media/capture-image/SysprepGeneral.png)
5. Sysprep arrête la machine virtuelle, ce qui a pour effet de modifier l’état de celle-ci en **Arrêté**dans le portail Azure.
6. Dans le portail Azure, cliquez sur **Machines virtuelles (classic)**, puis sélectionnez la machine virtuelle à capturer. Le groupe **Images de machine virtuelle (classiques)** est répertorié sous **Calculer** lorsque vous affichez **Tous les services**.

7. Dans la barre de commandes, cliquez sur **Capture**.

   ![Capture machine virtuelle](./media/capture-image/CaptureVM.png)

   La boîte de dialogue **Capture the Virtual Machine** s’affiche.

8. Dans **Image Name**, entrez le nom de la nouvelle image. Dans **Image label**, tapez une étiquette pour la nouvelle image.

9. Cliquez sur **J’ai exécuté Sysprep sur la machine virtuelle**. Cette case à cocher fait référence aux actions avec Sysprep dans les étapes 3 à 5. Une image _doit_ être généralisée en exécutant Sysprep avant d’ajouter une image Windows Server à votre jeu d’images personnalisées.

10. Une fois la capture terminée, la nouvelle image est disponible dans **Place de marché**, dans le conteneur **Compute**, **Images de machine virtuelle (classic)**.

    ![Capture d’image réussie](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Étapes suivantes
L’image est prête à être utilisée pour créer des machines virtuelles. Pour ce faire, vous allez créer une machine virtuelle en sélectionnant l’élément de menu **Tous les services** en bas du menu Services, puis **Images de machine virtuelle (classiques)** dans le groupe **Calculer**. Pour obtenir des instructions, consultez [Création d’une machine virtuelle à partir d’une image](createportal.md).

[How to sign in to a virtual machine running Windows Server]:connect-logon.md
[How to Use Sysprep: An Introduction]: http://technet.microsoft.com/library/bb457073.aspx
[Run Sysprep.exe]: ./media/virtual-machines-capture-image-windows-server/SysprepCommand.png
[Enter Sysprep.exe options]: ./media/capture-image/SysprepGeneral.png
[The virtual machine is stopped]: ./media/virtual-machines-capture-image-windows-server/SysprepStopped.png
[Capture an image of the virtual machine]: ./media/capture-image/CaptureVM.png
[Enter the image name]: ./media/virtual-machines-capture-image-windows-server/Capture.png
[Image capture successful]: ./media/virtual-machines-capture-image-windows-server/CaptureSuccess.png
[Use the captured image]: ./media/virtual-machines-capture-image-windows-server/MyImagesWindows.png
