---
title: Gestion des partages de fichiers Azure avec le portail Azure
description: Découvrez comment utiliser le portail Azure pour gérer Azure Files.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 588d260bb939c8f6439ca66828296ea455f1524a
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-the-azure-portal"></a>Gestion des partages de fichiers Azure avec le portail Azure 
[Azure Files](storage-files-introduction.md) est le système de fichiers cloud facile à utiliser de Microsoft. Les partages de fichiers Azure peuvent être montés dans Windows, Linux et macOS. Ce guide vous explique les bases de l’utilisation du partage de fichiers avec le [portail Azure](https://portal.azure.com/). Découvrez comment :

> [!div class="checklist"]
> * Créer un groupe de ressources et un compte de stockage
> * Crée un partage de fichiers Azure 
> * Créer un répertoire
> * Charger un fichier 
> * Téléchargement d’un fichier
> * Créer et utiliser un instantané de partage

Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-storage-account"></a>Créez un compte de stockage.
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-a-file-share"></a>Créer un partage de fichiers
Pour créer un partage de fichiers :

1. Sélectionnez le compte de stockage dans votre tableau de bord.
2. Dans la page du compte de stockage, dans la section **Services**, sélectionnez **Fichiers**.
    ![Capture d’écran de la section des services du compte de stockage ; sélectionnez le service Fichiers](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. Dans le menu situé en haut de la page **Service de fichiers**, cliquez sur **+ Partage de fichiers**. La page **Nouveau partage de fichier** s’affiche.
4. Dans **Nom**, saisissez *myshare*.
5. Cliquez sur **OK** pour créer le partage de fichiers Azure.

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulation du contenu du partage de fichiers Azure
Maintenant que vous avez créé un partage de fichiers Azure, vous pouvez monter le partage de fichiers avec SMB sur [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) ou [macOS](storage-how-to-use-files-mac.md). Vous pouvez également manipuler votre partage de fichiers Azure avec le portail Azure. Toutes les requêtes faites via le portail Azure sont faites avec l’API REST File, ce qui vous permet de créer, modifier et supprimer des fichiers et répertoires sur des clients sans accès SMB.

### <a name="create-directory"></a>Créer un répertoire
Pour créer un répertoire nommé *myDirectory* à la racine de votre partage de fichiers Azure :

1. Dans la page **Service de fichiers**, sélectionnez le partage de fichiers **myshare**. La page de votre partage de fichiers s’ouvre.
2. Dans le menu situé en haut de la page, sélectionnez **+ Ajouter un répertoire**. La page **Nouveau répertoire** s’affiche.
3. Saisissez *myDirectory* et cliquez sur **OK**.

### <a name="upload-a-file"></a>Charger un fichier 
Pour charger un fichier, vous devez tout d’abord créer ou sélectionner un fichier à charger. Vous pouvez le faire selon la méthode de votre choix. Une fois que vous avez sélectionné le fichier que vous voulez charger :

1. Cliquez sur le répertoire **myDirectory**. Le volet **myDirectory** s’ouvre.
2. Dans le menu situé en haut, cliquez sur **Télécharger**. Le panneau **Télécharger des fichiers** s’ouvre.  
    ![Capture d’écran du panneau Télécharger des fichiers](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Cliquez sur l’icône de dossier pour ouvrir une fenêtre permettant de parcourir vos fichiers locaux. 
4. Sélectionnez un fichier, puis sur **Ouvrir**. 
5. Dans la page **Télécharger des fichiers**, vérifiez le nom du fichier, puis cliquez sur **Télécharger**.
6. Une fois terminé, le fichier apparaît dans la liste sur la page **myDirectory**.

### <a name="download-a-file"></a>Téléchargement d’un fichier
Vous pouvez télécharger une copie du fichier que vous avez chargé en cliquant avec le bouton droit sur le fichier. Après avoir cliqué sur le bouton de téléchargement, l’expérience exacte dépend du système d’exploitation et du navigateur que vous utilisez.

## <a name="create-and-modify-share-snapshots"></a>Créer et modifier des instantanés de partage
Avec un partage de fichiers Azure, vous pouvez aussi créer des instantanés de partage. Un instantané conserve un point dans le temps pour un partage de fichiers Azure. Les instantanés de partage sont similaires aux technologies de systèmes d’exploitation que vous connaissez peut-être déjà comme :
- Le [service VSS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) pour les systèmes de fichiers Windows comme NTFS et ReFS
- Les instantanés du [Gestionnaire de Volume logique (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) pour les systèmes Linux
- Les instantanés du [système de fichiers Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) pour macOS. 

Pour créer un instantané de partage :

1. Ouvrez la page pour le partage de fichiers en ouvrant le compte de stockage à partir de votre tableau de bord > **Fichiers** > **myshare**. 
2. Dans la page pour le partage de fichiers, cliquez sur le bouton **Instantané** dans le menu situé en haut de la page, puis sélectionnez **Créer une capture instantanée**.  
    ![Capture d’écran illustrant comment trouver le bouton Créer une capture instantanée](media/storage-how-to-use-files-portal/create-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Répertorier et parcourir les instantanés de partage
Une fois l’instantané créé, vous pouvez cliquer à nouveau sur **Instantané**, puis sélectionner **View snapshots** (Afficher les instantanés) pour répertorier les instantanés pour le partage. Le volet affiche les instantanés pour ce partage. Cliquez sur un instantané de partage pour le parcourir.

### <a name="restore-from-a-share-snapshot"></a>Restaurer à partir d’un instantané de partage
Pour illustrer la restauration d’un fichier d’un instantané de partage, nous devons d’abord supprimer un fichier du partage de fichiers Azure. Accédez au dossier *myDirectory*, cliquez avec le bouton droit sur le fichier chargé et cliquez sur **Supprimer**. Ensuite, pour restaurer ce fichier depuis l’instantané de partage :

1. Cliquez sur **Instantanés** dans le menu supérieur et sélectionnez **View snapshots** (Afficher les instantanés). 
2. Cliquez sur l’instantané que vous avez créé précédemment, le contenu s’ouvre dans une nouvelle page. 
3. Cliquez sur **myDirectory** dans l’instantané. Le fichier que vous avez supprimé doit apparaître. 
4. Cliquez avec le bouton droit sur le fichier supprimé et sélectionnez **Restaurer**.
5. Une fenêtre contextuelle vous offrant le choix entre restaurer le fichier en tant que copie ou remplacer le fichier d’origine s’affiche. Étant donné que nous avons supprimé le fichier d’origine, nous pouvons sélectionner **Overwrite original file** (Remplacer le fichier d'origine) pour restaurer le fichier tel qu’il était avant sa suppression. Cliquez sur **OK** pour restaurer le fichier dans le partage de fichiers Azure.  
    ![Capture d’écran de la boîte de dialogue de restauration du fichier](media/storage-how-to-use-files-portal/restore-snapshot-1.png)

6. Une fois la restauration du fichier terminée, fermez la page de l’instantané et revenez à **myshare** > **myDirectory**. Le fichier doit se trouver dans son emplacement d’origine.

### <a name="delete-a-share-snapshot"></a>Supprimer un instantané de partage
Pour supprimer un instantané de partage, [accédez à la liste des instantanés de partage](#list-and-browse-a-share-snapshot). Cochez la case située en regard du nom de l’instantané de partage, puis sélectionnez le bouton **Supprimer**.

![Capture d’écran de la suppression d’un instantané de partage](media/storage-how-to-use-files-portal/delete-snapshot-1.png)

## <a name="clean-up-resources"></a>Supprimer des ressources
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
- [Gestion des partages de fichiers avec Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Gestion des partages de fichiers avec Azure CLI](storage-how-to-use-files-cli.md)
- [Gestion des partages de fichiers avec l’Explorateur Stockage Azure](storage-how-to-use-files-storage-explorer.md)
- [Planification d’un déploiement Azure Files](storage-files-planning.md)