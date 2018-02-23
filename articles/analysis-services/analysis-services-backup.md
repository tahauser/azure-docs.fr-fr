---
title: "Sauvegarde et restauration de la base de données Azure Analysis Services | Microsoft Docs"
description: "Explique comment sauvegarder et restaurer une base de données Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: 52db3916392c3ff304311717b12a163213197717
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="backup-and-restore"></a>Sauvegarde et restauration

La sauvegarde de bases de données de modèles tabulaires dans Azure Analysis Services est relativement semblable à celle des Analysis Services locaux. La principale différence réside dans le lieu où vous enregistrez vos fichiers de sauvegarde. Les fichiers de sauvegarde doivent être enregistrés dans un conteneur dans un [compte de stockage Azure](../storage/common/storage-create-storage-account.md). Vous pouvez utiliser un compte de stockage et un conteneur que vous avez déjà, ou bien en créer de nouveaux lors de la configuration des paramètres de stockage de votre serveur.

> [!NOTE]
> La création d’un compte de stockage peut entraîner un nouveau service facturable. Pour en savoir plus, consultez [Tarification Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Les sauvegardes sont enregistrées avec l’extension .abf. Pour les modèles tabulaires en mémoire, les données et les métadonnées de modèle sont enregistrées. Pour les modèles tabulaires DirectQuery, seules les métadonnées de modèle sont stockées. Les sauvegardes peuvent être compressées et chiffrées, en fonction des options que vous avez choisies. 



## <a name="configure-storage-settings"></a>Configurer les paramètres de stockage
Avant de procéder à la sauvegarde, vous devez configurer les paramètres de stockage de votre serveur.


### <a name="to-configure-storage-settings"></a>Pour configurer les paramètres de stockage
1.  Dans le portail Azure > **Paramètres**, cliquez sur **Sauvegarde**.

    ![Sauvegardes dans Paramètres](./media/analysis-services-backup/aas-backup-backups.png)

2.  Cliquez sur **Activé**, puis cliquez sur **Paramètres de stockage**.

    ![Activer](./media/analysis-services-backup/aas-backup-enable.png)

3. Sélectionnez un compte de stockage ou créez-en un nouveau.

4. Sélectionnez un conteneur ou créez-en un nouveau.

    ![Sélectionner un conteneur](./media/analysis-services-backup/aas-backup-container.png)

5. Enregistrez vos paramètres de sauvegarde.

    ![Enregistrer les paramètres de sauvegarde](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Sauvegarde

### <a name="to-backup-by-using-ssms"></a>Pour effectuer une sauvegarde à l’aide de SSMS

1. Dans SSMS, effectuez un clic droit sur une base de données > **Sauvegarder**.

2. Dans **Sauvegarder une base de données** > **Fichier de sauvegarde**, cliquez sur **Parcourir**.

3. Dans la boîte de dialogue **Enregistrer le fichier sous**, vérifiez le chemin d’accès au dossier, puis saisissez le nom du fichier de sauvegarde. 

4. Dans la boîte de dialogue **Sauvegarder une base de données**, sélectionnez les options.

    **Autoriser l’écrasement des fichiers** - Sélectionnez cette option pour remplacer les fichiers de sauvegarde portant le même nom. Si cette option n’est pas sélectionnée, le fichier que vous tentez d’enregistrer ne peut pas porter le même nom qu’un fichier se trouvant déjà au même emplacement.

    **Appliquer la compression** - Sélectionnez cette option pour compresser le fichier de sauvegarde. Les fichiers de sauvegarde compressés permettent d’économiser de l’espace disque, mais nécessite une utilisation un peu plus intensive du processeur. 

    **Chiffrer le fichier de sauvegarde** - Sélectionnez cette option pour chiffrer le fichier de sauvegarde. Cette option nécessite un mot de passe fourni par l’utilisateur pour sécuriser le fichier de sauvegarde. Le mot de passe empêche la lecture des données de sauvegarde à des fins autres que les opérations de restauration. Si vous choisissez de chiffrer les sauvegardes, conservez le mot de passe en lieu sûr.

5. Cliquez sur **OK** pour créer et enregistrer le fichier de sauvegarde.


### <a name="powershell"></a>PowerShell
Utilisez l’applet de commande [Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet).

## <a name="restore"></a>Restore
Lors de la restauration, votre fichier de sauvegarde doit être dans le compte de stockage que vous avez configuré pour votre serveur. Si vous devez déplacer un fichier de sauvegarde d’un emplacement local vers votre compte de stockage, utilisez [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) ou l’utilitaire de ligne de commande [AzCopy](../storage/common/storage-use-azcopy.md). 



> [!NOTE]
> Si vous procédez à une restauration à partir d’un serveur local, vous devez supprimer tous les utilisateurs de domaine des rôles du modèle, puis les rajouter aux rôles en tant qu’utilisateurs Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Pour effectuer une restauration à l’aide de SSMS

1. Dans SSMS, effectuez un clic droit sur une base de données > **Restaurer**.

2. Dans la boîte de dialogue **Sauvegarder une base de données**, dans **Fichier de sauvegarde**, cliquez sur **Parcourir**.

3. Dans la boîte de dialogue **Rechercher les fichiers de base de données**, sélectionnez le fichier à restaurer.

4. Dans la boîte de dialogue **Restaurer la base de données**, sélectionnez la base de données.

5. Spécifiez les options. Les options de sécurité doivent correspondre aux options de sauvegarde utilisées lors de la sauvegarde.


### <a name="powershell"></a>PowerShell

Utilisez l’applet de commande [Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet).


## <a name="related-information"></a>Informations connexes

[Des comptes de stockage Azure](../storage/common/storage-create-storage-account.md)  
[Haute disponibilité](analysis-services-bcdr.md)     
[Gérer Azure Analysis Services](analysis-services-manage.md)
