---
title: "Restaurer des données dans Azure sur un serveur Windows ou un ordinateur Windows | Microsoft Docs"
description: "Découvrez comment restaurer des données stockées dans Azure sur un serveur Windows ou un ordinateur Windows."
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 742f4b9e-c0ab-4eeb-8e22-ee29b83c22c4
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/4/2018
ms.author: saurse;trinadhk;markgal;
ms.openlocfilehash: 3444b13972ab9e5c435fc009e8ddb51bcafb1a41
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Restauration de fichiers sur un serveur Windows ou un ordinateur client Windows à l’aide du modèle de déploiement Resource Manager

Cet article explique comment restaurer des données depuis un coffre de sauvegarde. Pour restaurer des données, vous pouvez utiliser l’Assistant Récupérer des données dans l’agent Microsoft Azure Recovery Services (MARS). Lorsque vous restaurez des données, il est possible de :

* Restaurer des données sur l’ordinateur à partir duquel les sauvegardes ont été effectuées.
* Restaurez les données sur un autre ordinateur.

En janvier 2017, Microsoft a publié une mise à jour de la pré-version de l’agent MARS. Outre des correctifs de bogues, cette mise à jour propose la fonction de restauration instantanée, ce qui vous permet de monter une capture instantanée de point de récupération inscriptible comme volume de récupération. Vous pouvez ensuite explorer le volume de récupération, copier les fichiers sur un ordinateur local et sélectionner les fichiers à restaurer.

> [!NOTE]
> La [Mise à jour de la Sauvegarde Azure de janvier 2017](https://support.microsoft.com/en-us/help/3216528?preview) est nécessaire si vous souhaitez utiliser la restauration instantanée pour restaurer des données. De plus, les données de sauvegarde doivent être protégées dans des coffres pour les pays listés dans l’article relatif au support. Consultez la [Mise à jour de la Sauvegarde Azure de janvier 2017](https://support.microsoft.com/en-us/help/3216528?preview) pour obtenir la liste la plus récente des pays qui prennent en charge la restauration instantanée. La restauration instantanée n’est actuellement **pas** disponible dans tous les pays.
>

Utilisez la restauration instantanée avec les coffres Recovery Services dans le portail Azure. Si vous avez stocké des données dans des coffres de sauvegarde, ces derniers ont été convertis en coffres Recovery Services. Si vous souhaitez utiliser la restauration instantanée, téléchargez la mise à jour MARS et suivez les procédures mentionnant cette opération.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

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
    > Si vous ne cliquez pas sur Démonter, le volume de récupération reste monté pendant 6 heures à compter du montage. Toutefois, la durée du montage est étendue jusqu’à un maximum de 24 heures en cas de copie de fichier en cours. Aucune opération de sauvegarde ne s’exécute tant que le volume est monté. Toute opération de sauvegarde planifiée pour s’exécuter au moment où le volume de récupération est monté s’exécutera une fois qu’il sera démonté.
    >


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

    ![action](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

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
    > Si vous ne cliquez pas sur Démonter, le volume de récupération reste monté pendant 6 heures à compter du montage. Toutefois, la durée du montage est étendue jusqu’à un maximum de 24 heures en cas de copie de fichier en cours. Aucune opération de sauvegarde ne s’exécute tant que le volume est monté. Toute opération de sauvegarde planifiée pour s’exécuter au moment où le volume de récupération est monté s’exécutera une fois qu’il sera démonté.
    >

## <a name="troubleshooting"></a>Résolution de problèmes
Si Azure Backup ne monte pas efficacement le volume de montage, même lorsque vous cliquez plusieurs fois sur **Monter**, ou ne parvient pas à le monter et génère une ou plusieurs erreurs, suivez la procédure ci-dessous pour entamer une récupération normale.

1.  Si le processus de montage s’exécute depuis plusieurs minutes, annulez-le.

2.  Vérifiez que vous disposez de la dernière version de l’agent de sauvegarde Azure. Pour en savoir plus sur la version de l’agent de sauvegarde Azure, cliquez sur la zone **À propos de l’agent Microsoft Azure Recovery Services** du volet **Actions** de la console de sauvegarde Microsoft Azure, et assurez-vous que le numéro de **version** est égal ou supérieur à celui qu’indique [cet article](https://go.microsoft.com/fwlink/?linkid=229525). Vous pouvez télécharger la dernière version [ici](https://go.microsoft.com/fwLink/?LinkID=288905).

3.  Cliquez sur **Gestionnaire de périphériques** -> **Contrôleurs de stockage** et vérifiez que vous pouvez localiser **l’initiateur Microsoft iSCSI**. Si la réponse est oui, accédez directement à l’étape 7 ci-dessous. 

4.  Si vous ne pouvez pas localiser le service Initiateur iSCSI de Microsoft indiqué à l’étape 3, vérifiez si, sous **Gestionnaire de périphériques** -> **Contrôleurs de stockage**, l’entrée **Appareil inconnu** s’affiche, associée à l’ID matériel **ROOT\ISCSIPRT**.

5.  Cliquez avec le bouton droit sur l’entrée **Appareil inconnu** et sélectionnez **Mettre à jour le pilote**.

6.  Mettez à jour le pilote, en sélectionnant l’option **Rechercher automatiquement un pilote logiciel mis à jour**. Suite à la mise à jour, l’entrée **Appareil inconnu** est remplacée par **Initiateur Microsoft iSCSI**, comme indiqué ci-dessous. 

    ![Chiffrement](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7.  Cliquez sur **Gestionnaire des tâches** -> **Services (local)** -> **Service Initiateur iSCSI de Microsoft**. 

    ![Chiffrement](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)
    
8.  Redémarrez le service Initiateur iSCSI de Microsoft en cliquant avec le bouton droit sur ce dernier, en cliquant sur **Arrêter**, puis en cliquant à nouveau sur le bouton droit et en sélectionnant **Démarrer**.

9.  Recommencez la récupération à l’aide de la restauration instantanée. 

Si elle échoue encore, redémarrez votre serveur/client. Si un redémarrage n’est pas souhaitable, ou si la récupération échoue même après le redémarrage du serveur, essayez de l’exécuter au moyen d’un autre ordinateur et contactez le Support Azure en accédant au [portail Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), puis en soumettant une demande de support.

## <a name="next-steps"></a>Étapes suivantes
* Maintenant que vous avez restauré vos fichiers et vos dossiers, vous pouvez [gérer vos sauvegardes](backup-azure-manage-windows-server.md).
