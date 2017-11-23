---
title: "Restauration de données sur un serveur Windows ou un ordinateur client Windows à partir d’Azure à l’aide du modèle de déploiement classique | Microsoft Docs"
description: "Découvrez comment restaurer des fichiers à partir d’un serveur/client Windows."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: cb08d5c80954e1c231bf6e8b781b09700ef0959d
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2017
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>Restauration de fichiers sur un serveur Windows ou un ordinateur client Windows à l’aide du modèle de déploiement classique
> [!div class="op_single_selector"]
> * [Portail classique](backup-azure-restore-windows-server-classic.md)
> * [Portail Azure](backup-azure-restore-windows-server.md)
>
>

Cet article explique comment récupérer des données à partir d’un coffre de sauvegarde et les restaurer sur un serveur ou un ordinateur. Depuis mars 2017, vous ne pouvez plus utiliser le portail Classic pour créer des coffres de sauvegarde.

> [!IMPORTANT]
> Vous pouvez désormais mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services. Pour en savoir plus, consultez l’article [Mettre à niveau un coffre de sauvegarde vers un coffre Recovery Services](backup-azure-upgrade-backup-to-recovery-services.md). Microsoft vous recommande de mettre à niveau vos coffres de sauvegarde vers des coffres Recovery Services.<br/> À partir du **30 novembre 2017**, vous ne pouvez pas vous servir de PowerShell pour créer des coffres de sauvegarde. <br/> **À compter du 30 novembre 2017** :
>- Les coffres de sauvegarde restants seront automatiquement mis à niveau vers des coffres Recovery Services.
>- Vous ne pourrez plus accéder à vos données de sauvegarde depuis le portail Classic. Au lieu de cela, vous devrez utiliser le portail Azure pour accéder à ces données au sein de coffres Recovery Services.
>

Pour restaurer des données, vous pouvez utiliser l’Assistant Récupérer des données dans l’agent Microsoft Azure Recovery Services (MARS). Lorsque vous restaurez des données, il est possible de :

* Restaurer des données sur l’ordinateur à partir duquel les sauvegardes ont été effectuées.
* Restaurez les données sur un autre ordinateur.

En janvier 2017, Microsoft a publié une mise à jour de la pré-version de l’agent MARS. Outre des correctifs de bogues, cette mise à jour propose la fonction de restauration instantanée, ce qui vous permet de monter une capture instantanée de point de récupération inscriptible comme volume de récupération. Vous pouvez ensuite explorer le volume de récupération, copier les fichiers sur un ordinateur local et sélectionner les fichiers à restaurer.

> [!NOTE]
> La [Mise à jour de la Sauvegarde Azure de janvier 2017](https://support.microsoft.com/en-us/help/3216528?preview) est nécessaire si vous souhaitez utiliser la restauration instantanée pour restaurer des données. De plus, les données de sauvegarde doivent être protégées dans des coffres pour les pays listés dans l’article relatif au support. Consultez la [Mise à jour de la Sauvegarde Azure de janvier 2017](https://support.microsoft.com/en-us/help/3216528?preview) pour obtenir la liste la plus récente des pays qui prennent en charge la restauration instantanée. La restauration instantanée n’est actuellement **pas** disponible dans tous les pays.
>

La restauration instantanée est disponible pour une utilisation dans des coffres Recovery Services dans le portail Azure et dans des coffres de sauvegarde dans le portail classique. Si vous souhaitez utiliser la restauration instantanée, téléchargez la mise à jour MARS et suivez les procédures mentionnant cette opération.


## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Utiliser la restauration instantanée pour récupérer des données sur le même ordinateur

Si vous avez supprimé accidentellement un fichier et que vous voulez le restaurer sur le même ordinateur (à partir duquel la sauvegarde est effectuée), les étapes suivantes vous aident à récupérer les données.

1. Ouvrez le composant logiciel enfichable **Microsoft Azure Backup** . Si vous ne savez pas où le composant logiciel enfichable a été installé, recherchez **Sauvegarde Microsoft Azure** sur l’ordinateur ou le serveur.

    L’application de bureau devrait apparaître dans les résultats de recherche.

2. Cliquez sur **Récupérer des données** pour lancer l’Assistant.

    ![Récupérer des données](./media/backup-azure-restore-windows-server/recover.png)

3. Sur le volet **Prise en main**, pour restaurer les données sur le même serveur ou ordinateur, sélectionnez **Ce serveur (`<server name>`)** et cliquez sur **Suivant**.

    ![Choisissez l’option Ce serveur pour restaurer les données sur le même ordinateur](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. Sur le volet **Sélectionnez le mode de récupération**, choisissez **Fichiers et dossiers individuels**, puis cliquez sur **Suivant**.

    ![Parcourir les fichiers](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. Sur le volet **Sélectionner le volume et la date**, sélectionnez le volume qui contient les fichiers et/ou les dossiers à restaurer.

    Dans le calendrier, sélectionnez un point de récupération. Vous pouvez effectuer la restauration à partir du point de récupération de votre choix dans le temps. Les dates **en gras** indiquent la disponibilité d’au moins un point de récupération. Une fois que vous avez sélectionné une date, si plusieurs points de récupération sont disponibles, cliquez sur l’un d’entre eux dans le menu déroulant **Temps**.

    ![Volume et date](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Une fois que vous avez choisi le point de récupération à restaurer, cliquez sur **Monter**.

    La Sauvegarde Azure monte le point de récupération local et l’utilise comme volume de récupération.

7. Sur le volet **Rechercher et récupérer des fichiers**, cliquez sur **Parcourir** pour ouvrir l’Explorateur Windows et rechercher les fichiers et dossiers que vous souhaitez.

    ![Options de récupération](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. Dans l’Explorateur Windows, copiez les fichiers et/ou les dossiers que vous souhaitez restaurer et collez-les à l’emplacement local de votre choix sur l’ordinateur ou le serveur. Vous pouvez ouvrir ou diffuser en continu les fichiers directement à partir du volume de récupération et vérifier que les versions récupérées sont les bonnes.

    ![Copiez et collez les fichiers et dossiers du volume monté vers l’emplacement local](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Lorsque vous avez terminé la restauration des fichiers et/ou des dossiers, sur le volet **Rechercher et récupérer des fichiers**, cliquez sur **Démonter**. Puis cliquez sur **Oui** pour confirmer que vous souhaitez démonter le volume.

    ![Démontez le volume et confirmez](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Si vous ne cliquez pas sur Démonter, le Volume de récupération reste monté pendant six heures à compter du montage. Aucune opération de sauvegarde ne s’exécute tant que le volume est monté. Toute opération de sauvegarde planifiée pour s’exécuter au moment où le volume de récupération est monté s’exécutera une fois qu’il sera démonté.
    >


## <a name="recover-data-to-the-same-machine"></a>Récupération des données sur le même ordinateur
Si vous avez supprimé accidentellement un fichier et que vous voulez le restaurer sur le même ordinateur (à partir duquel la sauvegarde est effectuée), les étapes suivantes vous aident à récupérer les données.

1. Ouvrez le composant logiciel enfichable **Microsoft Azure Backup** .
2. Cliquez sur **Récupérer des données** pour lancer le flux de travail.

    ![Récupérer des données](./media/backup-azure-restore-windows-server-classic/recover.png)
3. Sélectionnez l’option **Ce serveur (*nomdevotremachine*)** pour restaurer le fichier sauvegardé sur le même ordinateur.

    ![Même ordinateur](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. Vous pouvez choisir **Naviguer jusqu’aux fichiers** ou **Rechercher des fichiers**.

    Conservez l’option par défaut si vous envisagez de restaurer un ou plusieurs fichiers dont le chemin est connu. Si vous n’êtes pas certain de la structure de dossiers, mais que vous voulez rechercher un fichier, choisissez l’option **Rechercher des fichiers** . Dans cette section, nous choisissons l’option par défaut.

    ![Parcourir les fichiers](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. Sélectionnez le volume à partir duquel vous voulez restaurer le fichier.

    Vous pouvez restaurer le fichier à partir de n’importe quel point dans le temps. Les dates qui s’affichent en **gras** dans le contrôle Calendrier indiquent la disponibilité d’un point de restauration. Une fois qu’une date est sélectionnée, en fonction de votre planification de sauvegarde (et de la réussite d’une opération de sauvegarde), vous pouvez sélectionner un point dans le temps dans la liste déroulante **Heure** .

    ![Volume et date](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. Sélectionnez les éléments à restaurer. Vous pouvez sélectionner plusieurs fichiers/dossiers à restaurer.

    ![Sélectionner des fichiers](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. Spécifiez les paramètres de récupération.

    ![Options de récupération](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)

   * Vous avez la possibilité d’effectuer la restauration à l’emplacement d’origine (dans lequel le fichier/dossier sera remplacé) ou dans un autre emplacement sur le même ordinateur.
   * Si le fichier/dossier que vous voulez restaurer existe dans l’emplacement cible, vous avez la possibilité de créer des copies (deux versions du même fichier), de remplacer les fichiers dans l’emplacement cible ou d’ignorer la récupération des fichiers qui existent dans la cible.
   * Il est vivement recommandé de conserver l’option par défaut qui consiste à restaurer les ACL sur les fichiers récupérés.
8. Une fois ces entrées fournies, cliquez sur **Suivant**. Le flux de travail de récupération, qui consiste à restaurer les fichiers sur cet ordinateur, commence.

## <a name="recover-to-an-alternate-machine"></a>Récupération sur un autre ordinateur
Si votre serveur entier est perdu, vous pouvez toujours récupérer les données d’Azure Backup sur un autre ordinateur. Les étapes suivantes illustrent le flux de travail.  

Les termes ci-après sont utilisés pour cette procédure :

* *Ordinateur source* : ordinateur d’origine à partir duquel la sauvegarde a été effectuée et qui est actuellement indisponible.
* *Ordinateur cible* : ordinateur sur lequel les données sont récupérées.
* *Exemple d’archivage* : archivage de sauvegarde dans lequel *l’ordinateur source* et *l’ordinateur cible* sont enregistrés. <br/>

> [!NOTE]
> Les sauvegardes effectuées à partir d’un ordinateur ne peuvent pas être restaurées sur un ordinateur qui exécute une version antérieure du système d’exploitation. Par exemple, si les sauvegardes sont effectuées à partir d’un ordinateur Windows 7, elles peuvent être restaurées sur un ordinateur Windows 8 ou supérieur. Toutefois l’inverse n’est pas vrai.
>
>

1. Ouvrez le composant logiciel enfichable **Microsoft Azure Backup** sur l’ *ordinateur cible*.
2. Vérifiez que *l’ordinateur cible* et *l’ordinateur source* sont inscrits auprès du même archivage de sauvegarde.
3. Cliquez sur **Récupérer des données** pour lancer le flux de travail.

    ![Récupérer des données](./media/backup-azure-restore-windows-server-classic/recover.png)
4. Sélectionnez **Un autre serveur**

    ![Un autre serveur](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. Fournissez le fichier d’informations d’identification de coffre qui correspond à l’ *exemple d’archivage*. Si le fichier d’informations d’identification de coffre n’est pas valide (ou a expiré), téléchargez un nouveau fichier d’informations d’identification de coffre à partir de l’ *exemple d’archivage* dans le portail Azure Classic. Une fois que le fichier d’informations d’identification de coffre est fourni, l’archivage de sauvegarde correspondant au fichier d’informations d’identification de coffre s’affiche.
6. Sélectionnez l’ *ordinateur source* dans la liste des ordinateurs affichés.

    ![Liste des ordinateurs](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. Sélectionnez l’option **Rechercher des fichiers** ou **Naviguer jusqu’aux fichiers**. Dans cette section, nous utilisons l’option **Rechercher des fichiers** .

    ![action](./media/backup-azure-restore-windows-server-classic/search.png)
8. Dans l’écran suivant, sélectionnez le volume et la date. Recherchez le nom du fichier/dossier que vous souhaitez restaurer.

    ![Éléments de recherche](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. Sélectionnez l’emplacement vers lequel les fichiers doivent être restaurés.

    ![Emplacement de restauration](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. Fournissez la phrase secrète de chiffrement qui a été fournie pendant l’inscription de *l’ordinateur source* dans *l’exemple d’archivage*.

    ![Chiffrement](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. Une fois l’entrée fournie, cliquez sur **Récupérer**pour déclencher la restauration des fichiers de sauvegarde dans la destination fournie.

## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Utiliser la restauration instantanée pour restaurer des données sur un autre ordinateur
Si votre serveur entier est perdu, vous pouvez toujours récupérer les données d’Azure Backup sur un autre ordinateur. Les étapes suivantes illustrent le flux de travail.

Les termes ci-après sont utilisés pour cette procédure :

* *Ordinateur source* : ordinateur d’origine à partir duquel la sauvegarde a été effectuée et qui est actuellement indisponible.
* *Ordinateur cible* : ordinateur sur lequel les données sont récupérées.
* *Exemple d’archivage* – coffre Recovery Services dans lequel *l’ordinateur source* et *l’ordinateur cible* sont enregistrés. <br/>

> [!NOTE]
> Il est impossible de restaurer les sauvegardes sur un ordinateur cible qui fonctionne avec une version antérieure du système d’exploitation. Par exemple, une sauvegarde effectuée à partir d’un ordinateur Windows 7 ne peut pas être restaurée sur un ordinateur sous Windows 8 ou une version ultérieure. Il n’est pas possible de restaurer sur un ordinateur Windows 7 une sauvegarde effectuée à partir d’un ordinateur Windows 8.
>
>

1. Ouvrez le composant logiciel enfichable **Microsoft Azure Backup** sur l’ *ordinateur cible*.

2. Vérifiez que *l’Ordinateur cible* et *l’Ordinateur source* sont inscrits auprès du même coffre Recovery Services.

3. Cliquez sur **Récupérer des données** pour ouvrir **l’Assistant Récupération de données**.

    ![Récupérer des données](./media/backup-azure-restore-windows-server/recover.png)

4. Sur le volet **Prise en main**, sélectionnez **Autre serveur**.

    ![Un autre serveur](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Fournissez le fichier d’informations d’identification correspondant à *l’Exemple de coffre*, puis cliquez sur **Suivant**.

    Si le fichier d’informations d’identification du coffre n’est pas valide (ou a expiré), téléchargez un nouveau fichier d’informations d’identification de coffre à partir de *l’Exemple de coffre* dans le Portail Azure. Une fois que vous avez fourni des informations d’identification de coffre valides, le nom du Coffre de sauvegarde correspondant s’affiche.

6. Sur le volet **Sélectionner un serveur de sauvegarde**, sélectionnez *l’Ordinateur source* dans la liste des ordinateurs affichés et fournissez la phrase secrète. Cliquez ensuite sur **Suivant**.

    ![Liste des ordinateurs](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. Sur le volet **Sélectionner un mode de récupération**, sélectionnez **Fichiers et dossiers individuels** et cliquez sur **Suivant**.

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. Sur le volet **Sélectionner le volume et la date**, sélectionnez le volume qui contient les fichiers et/ou les dossiers à restaurer.

    Dans le calendrier, sélectionnez un point de récupération. Vous pouvez effectuer la restauration à partir du point de récupération de votre choix dans le temps. Les dates **en gras** indiquent la disponibilité d’au moins un point de récupération. Une fois que vous avez sélectionné une date, si plusieurs points de récupération sont disponibles, cliquez sur l’un d’entre eux dans le menu déroulant **Temps**.

    ![Éléments de recherche](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Cliquez sur **Monter** pour monter localement le point de récupération comme volume de récupération sur votre *Ordinateur cible*.

10. Sur le volet **Rechercher et récupérer des fichiers**, cliquez sur **Parcourir** pour ouvrir l’Explorateur Windows et rechercher les fichiers et dossiers que vous souhaitez.

    ![Chiffrement](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. Dans l’Explorateur Windows, copiez les fichiers et/ou les dossiers du volume de récupération et collez-les à l’emplacement de votre *Ordinateur cible*. Vous pouvez ouvrir ou diffuser en continu les fichiers directement à partir du volume de récupération et vérifier que les versions récupérées sont les bonnes.

    ![Chiffrement](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Lorsque vous avez terminé la restauration des fichiers et/ou des dossiers, sur le volet **Rechercher et récupérer des fichiers**, cliquez sur **Démonter**. Puis cliquez sur **Oui** pour confirmer que vous souhaitez démonter le volume.

    ![Chiffrement](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Si vous ne cliquez pas sur Démonter, le Volume de récupération reste monté pendant six heures à compter du montage. Aucune opération de sauvegarde ne s’exécute tant que le volume est monté. Toute opération de sauvegarde planifiée pour s’exécuter au moment où le volume de récupération est monté s’exécutera une fois qu’il sera démonté.
    >


## <a name="next-steps"></a>Étapes suivantes
* [Azure Backup - Forum Aux Questions](backup-azure-backup-faq.md)
* Consultez le [forum Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## <a name="learn-more"></a>En savoir plus
* [Vue d’ensemble d’Azure Backup](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Sauvegarde des machines virtuelles Azure](backup-azure-vms-introduction.md)
* [Sauvegarde des charges de travail Microsoft](backup-azure-dpm-introduction.md)
