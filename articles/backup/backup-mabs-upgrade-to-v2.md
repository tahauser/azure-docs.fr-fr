---
title: Installer le serveur de sauvegarde Azure v2 | Microsoft Docs
description: Le serveur de sauvegarde Azure v2 offre des capacités de sauvegarde améliorées pour protéger les machines virtuelles, les fichiers et dossiers, les charges de travail, et plus encore. Découvrez comment installer le serveur de sauvegarde Azure v2 ou effectuer la mise à niveau vers ce dernier.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: masaran;markgal
ms.openlocfilehash: dd7b76d9e06bc82ffd75f12131c2c247da05cc91
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="install-azure-backup-server-v2"></a>Installer le serveur de sauvegarde Azure v2

Le serveur de sauvegarde Azure contribue à protéger vos machines virtuelles, vos charges de travail, vos fichiers et dossiers, et plus encore. Reposant sur le serveur de sauvegarde v1, le serveur de sauvegarde Azure v2 offre des fonctionnalités qui ne sont pas disponibles dans la première version. Pour connaître les différences entre les versions v1 et v2, consultez [Matrice de protection du serveur de sauvegarde Azure](backup-mabs-protection-matrix.md). 

Les fonctionnalités supplémentaires du serveur de sauvegarde v2 constituent une mise à niveau du serveur de sauvegarde v1. Cependant, le serveur de sauvegarde v1 n’est pas requis pour installer le serveur de sauvegarde v2. Si vous souhaitez effectuer la mise à niveau du serveur de sauvegarde v1 vers le serveur de sauvegarde v2, installez le serveur de sauvegarde v2 sur le serveur de protection du serveur de sauvegarde. Vos paramètres de serveur de sauvegarde existants seront conservés.

Vous pouvez installer le serveur de sauvegarde v2 sur Windows Server 2012 R2 ou Windows Server 2016. Pour tirer parti des nouvelles fonctionnalités telles que System Center 2016 Data Protection Manager Modern Backup Storage, vous devez installer le serveur de sauvegarde v2 sur Windows Server 2016. Avant d’installer le serveur de sauvegarde v2 ou d’effectuer la mise à niveau vers ce dernier, prenez connaissance des [conditions préalables à l’installation](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Le serveur de sauvegarde Azure présente la même base de code que System Center Data Protection Manager. Le serveur de sauvegarde v1 équivaut à Data Protection Manager 2012 R2 et le serveur de sauvegarde v2 à Data Protection Manager 2016. Cet article fait parfois référence à la documentation de Data Protection Manager.
>
>

## <a name="upgrade-backup-server-to-v2"></a>Mettre à niveau le serveur de sauvegarde vers la version 2
Pour mettre à niveau le serveur de sauvegarde v1 vers le serveur de sauvegarde v2, assurez-vous que votre installation présente les mises à jour requises :

- [Mettez à jour les agents de protection](backup-mabs-upgrade-to-v2.md#update-the-data-protection-manager-protection-agent) sur les serveurs protégés.
- Mettez à niveau Windows Server 2012 R2 vers Windows Server 2016.
- Mettez à niveau l’outil d’administration à distance du serveur de sauvegarde Azure sur tous les serveurs de production.
- Assurez-vous que les sauvegardes sont définies pour continuer sans redémarrer votre serveur de production.


### <a name="upgrade-steps-for-backup-server-v2"></a>Procédure de mise à niveau vers le serveur de sauvegarde v2

1. Dans le Centre de téléchargement, [téléchargez le programme d’installation de la mise à niveau](https://go.microsoft.com/fwlink/?LinkId=626082).

2. Après avoir extrait l’Assistant d’installation, assurez-vous que la case à cocher **Exécuter setup.exe** est activée, puis sélectionnez **Terminer**.

  ![Programme d’installation - Exécuter l’installation](./media/backup-mabs-upgrade-to-v2/run-setup.png)

3. Dans l’Assistant d’installation du serveur de sauvegarde Microsoft Azure, sous **Installer**, sélectionnez **Serveur Sauvegarde Microsoft Azure**.

  ![Programme d’installation - Sélectionner l’installation](./media/backup-mabs-upgrade-to-v2/mabs-installer-s1.png)

4. Dans la page **Bienvenue**, lisez les avertissements, puis sélectionnez **Suivant**.

  ![Programme d’installation - Page Bienvenue](./media/backup-mabs-upgrade-to-v2/mabs-installer-s2.png)

5. L’Assistant d’installation effectue des vérifications préalables pour s’assurer que votre environnement peut être mis à niveau. Dans la page **Vérifications des conditions requises**, sélectionnez **Vérifier**.

  ![Programme d’installation - Page Vérifications des conditions requises](./media/backup-mabs-upgrade-to-v2/mabs-installer-s3-perform-checks.png)

6. Votre environnement doit passer les vérifications des conditions requises avec succès. Si votre environnement échoue à ces vérifications, notez les problèmes et corrigez-les. Ensuite, sélectionnez **Revérifier**. Une fois les vérifications des conditions requises passées avec succès, sélectionnez **Suivant**.

  ![Programme d’installation - Bouton Revérifier](./media/backup-mabs-upgrade-to-v2/mabs-installer-s4-pass-checks.png)

7. Dans la page **Paramètres SQL**, sélectionnez l’option appropriée pour votre installation de SQL, puis choisissez **Vérifier et installer**.

  ![Programme d’installation - Page Paramètres SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5-sql-settings.png)

  Les vérifications peuvent prendre quelques minutes. Une fois les vérifications terminées, cliquez sur **Suivant**.

  ![Programme d’installation - Bouton Vérifier et installer de la page Paramètres SQL](./media/backup-mabs-upgrade-to-v2/mabs-installer-s5a-check-and fix-settings.png)

8. Dans la page **Paramètres d’installation**, apportez les modifications requises à l’emplacement d’installation du serveur de sauvegarde ou à l’emplacement de la zone temporaire. Sélectionnez **Suivant**.

  ![Programme d’installation - Page Paramètres d’installation](./media/backup-mabs-upgrade-to-v2/mabs-installer-s6-installation-settings.png)

9. Pour terminer l’Assistant d’installation, sélectionnez **Terminer**.

  ![Programme d’installation - Terminer](./media/backup-mabs-upgrade-to-v2/run-setup.png)



## <a name="add-storage-for-modern-backup-storage"></a>Ajouter un stockage pour Modern Backup Storage

Pour améliorer l’efficacité du stockage de sauvegarde, la prise en charge des volumes a été ajoutée à la version 2 du serveur de sauvegarde. Comme le serveur de sauvegarde v1, le serveur de sauvegarde v2 prend en charge les disques.

### <a name="add-volumes-and-disks"></a>Ajouter des volumes et des disques
Si vous exécutez le serveur de sauvegarde v2 sur Windows Server 2016, vous pouvez utiliser des volumes pour stocker les données de sauvegarde. Les volumes assurent des économies en stockage et des sauvegardes plus rapides. Comme les volumes sont une nouveauté du serveur de sauvegarde, vous devez les ajouter. 

Lorsque vous ajoutez un volume au serveur de sauvegarde, vous pouvez lui donner un nom convivial. Cliquez sur la colonne **Nom convivial** du volume auquel vous souhaitez donner un nom. Si nécessaire, vous pourrez modifier le nom ultérieurement. Vous pouvez également utiliser PowerShell pour ajouter un nom convivial aux volumes ou le modifier.

Pour ajouter un volume dans la console Administrateur :

1. Dans la console Administrateur du serveur de sauvegarde Azure, sélectionnez **Gestion** > **Stockage sur disque** > **Ajouter**.

    ![Ouvrir l’Assistant Ajouter un stockage sur disque](./media//backup-mabs-upgrade-to-v2/open-add-disk-storage-wizard.png)

    L’Assistant Ajouter un stockage sur disque s’ouvre.

2. Dans la page **Ajouter un stockage sur disque**, dans la zone **Volumes disponibles**, sélectionnez un volume, puis choisissez **Ajouter**.
3. Dans la zone **Volumes sélectionnés**, entrez un nom convivial pour le volume, puis sélectionnez **OK**.

      ![Assistant Ajouter un stockage sur disque - Ajouter un volume](./media/backup-mabs-upgrade-to-v2/add-volume.png)

  Si vous souhaitez ajouter un disque, ce disque doit appartenir à un groupe de protection présentant un stockage existant. Ces disques peuvent uniquement être utilisés pour ces groupes de protection. Si aucune source du serveur de sauvegarde ne présente de protection existante, le disque n’est pas répertorié.

  Pour plus d’informations sur l’ajout de disques, consultez [Ajout de disques pour augmenter le stockage existant](http://docs.microsoft.com/system-center/dpm/upgrade-to-dpm-2016#adding-disks-to-increase-legacy-storage). Vous ne pouvez pas donner de nom convivial à un disque.


### <a name="assign-workloads-to-volumes"></a>Assigner des charges de travail à des volumes

Dans le serveur de sauvegarde, vous indiquez quelles charges de travail sont assignées à quels volumes. Par exemple, vous pouvez définir des volumes coûteux prenant en charge un nombre élevé d’opérations d’entrée/sortie par seconde (IOPS) pour stocker uniquement les charges de travail qui nécessitent des sauvegardes de volumes importants fréquentes. Un exemple est SQL Server avec les journaux des transactions.

#### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Pour mettre à jour les propriétés d’un volume du pool de stockage dans le serveur de sauvegarde, utilisez le cmdlet PowerShell Update-DPMDiskStorage.

Syntaxe :

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```

Toutes les modifications apportées à l’aide de PowerShell sont répercutées dans l’interface utilisateur.


## <a name="protect-data-sources"></a>Protéger les sources de données
Pour commencer à protéger les sources de données, créez un groupe de protection. Les étapes suivantes mettent en évidence les modifications ou les ajouts de l’Assistant Nouveau groupe de protection.

Pour créer un groupe de protection :

1. Dans la console Administrateur du serveur de sauvegarde, sélectionnez **Protection**.

2. Dans la barre d’outils, sélectionnez **Nouveau**.

    L’Assistant Créer un nouveau groupe de protection s’ouvre.

  ![Assistant Création d’un nouveau groupe de protection](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-1.png)

3. Sur la page d’**accueil**, sélectionnez **Suivant**.
4. Dans la page **Sélectionner le type de groupe de protection**, sélectionnez le type de groupe de protection que vous souhaitez créer, puis choisissez **Suivant**.

  ![Page Sélectionner le type de groupe de protection](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-2.png)

5. Dans la page **Sélectionner les membres du groupe**, dans le volet **Membres disponibles**, les membres présentant des agents de protection sont répertoriés. Pour cet exemple, sélectionnez les volumes D:\ et E:\ et ajoutez-les au volet **Membres sélectionnés**. Sélectionnez **Suivant**.

  ![Page Sélectionner les membres du groupe](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-3.png)

6. Dans la page **Sélectionner la méthode de protection des données**, entrez le **nom du groupe de protection**, sélectionnez la méthode de protection, puis choisissez **Suivant**. Si vous recherchez une protection à court terme, vous devez sélectionner la méthode de protection **Disque**.

  ![Page Sélectionner la méthode de protection des données](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-4.png)

7. Dans la page **Spécifier les objectifs à court terme**, sélectionnez les détails de la **durée de rétention** et de la **fréquence de synchronisation**. Ensuite, sélectionnez **Suivant**. Si vous le souhaitez, vous pouvez modifier les jours et les heures de création des points de récupération en sélectionnant **Modifier**.

  ![Page Spécifier les objectifs à court terme](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-5.png)

8. Dans la page **Vérifier l’allocation de stockage sur disque**, passez en revue les détails concernant les sources de données sélectionnées, leur taille, l’espace à provisionner et le volume de stockage cible.

  ![Page Vérifier l’allocation de stockage sur disque](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-6.png)

  Les volumes de stockage sont basés sur l’allocation de volume de charge de travail (définie à l’aide de PowerShell) et le stockage disponible. Vous pouvez modifier les volumes de stockage en sélectionnant d’autres volumes dans le menu déroulant. Si vous modifiez la valeur de **Stockage cible**, la valeur de **Stockage sur disque disponible** est modifiée dynamiquement pour refléter les valeurs sous **Espace libre** et **Underprovisioned Space** (Espace sous-provisionné).

  Si la taille des sources de données augmente comme prévu, la valeur de la colonne **Underprovisioned Space** (Espace sous-provisionné) dans **Stockage sur disque disponible** reflète la quantité de stockage supplémentaire requise. Utilisez cette valeur pour la planification de vos besoins en stockage afin de garantir des sauvegardes fluides. Si la valeur est égale à zéro, il n’y aura aucun problème de stockage dans un avenir proche. Si la valeur est différente de zéro, le stockage alloué est insuffisant (d’après votre stratégie de protection et la taille des données de vos membres protégés).

  ![Stockage sur disque sous-alloué](./media/backup-mabs-upgrade-to-v2/create-a-protection-group-7.png)

   Pour finir la création de votre groupe de protection, terminez l’Assistant.

## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrer un stockage existant vers Modern Backup Storage
Après avoir installé le serveur de sauvegarde v2 ou effectué la mise à niveau vers ce dernier et mis à niveau le système d’exploitation vers Windows Server 2016, mettez à jour vos groupes de protection pour qu’ils utilisent Modern Backup Storage. Par défaut, les groupes de protection ne sont pas modifiés. Ils continuent de fonctionner comme ils ont été initialement configurés. 

La mise à jour des groupes de protection pour qu’ils utilisent Modern Backup Storage est facultative. Pour mettre à jour le groupe de protection, arrêtez la protection de toutes les sources de données à l’aide de l’option de conservation des données. Ensuite, ajoutez les sources de données à un nouveau groupe de protection.

1. Dans la console Administrateur du serveur de sauvegarde, sélectionnez la fonctionnalité **Protection**. Dans la liste **Membre du groupe de Protection**, cliquez sur le membre, puis sélectionnez **Arrêter la protection du membre**.

  ![Arrêter la protection du membre](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Dans la boîte de dialogue **Supprimer du groupe**, vérifiez l’espace disque utilisé et l’espace libre disponible pour le pool de stockage. La valeur par défaut consiste à conserver les points de récupération sur le disque et à les laisser expirer suivant la stratégie de rétention associée. Cliquez sur **OK**.

  Si vous souhaitez restituer immédiatement l’espace disque utilisé pour le pool de stockage libre, sélectionnez la case à cocher **Supprimer le réplica sur le disque** pour supprimer les données de sauvegarde (et les points de récupération) associées à ce membre.

  ![Boîte de dialogue Supprimer du groupe](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Créez un groupe de protection qui utilise Modern Backup Storage. Incluez les sources de données non protégées.


## <a name="add-disks-to-increase-legacy-storage"></a>Ajouter des disques pour augmenter le stockage existant

Si vous souhaitez utiliser un stockage existant avec le serveur de sauvegarde, vous devrez peut-être ajouter des disques pour augmenter la capacité de stockage. 

Pour ajouter un stockage sur disque :

1. Dans la console Administrateur, sélectionnez **Gestion** > **Stockage sur disque** > **Ajouter**.

    ![Boîte de dialogue Ajouter un stockage sur disque](http://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. Dans la boîte de dialogue **Ajouter un stockage sur disque**, sélectionnez **Ajouter des disques**.

5. Dans la liste des disques disponibles, sélectionnez les disques à ajouter, choisissez **Ajouter**, puis sélectionnez **OK**.

## <a name="update-the-data-protection-manager-protection-agent"></a>Mettre à jour l’agent de protection Data Protection Manager

Le serveur de sauvegarde tire parti de l’agent de protection System Center Data Protection Manager pour les mises à jour. Si vous mettez à niveau un agent de protection qui n’est pas connecté au réseau, vous ne pouvez pas utiliser la console Administrateur de Data Protection Manager pour effectuer une mise à niveau d’agent connecté. Vous devez mettre à niveau l’agent de protection dans un environnement de domaine non actif. Tant que l’ordinateur client n’est pas connecté au réseau, la console Administrateur de Data Protection Manager indique que la mise à jour de l’agent de protection est en attente.

Les sections suivantes expliquent comment mettre à jour les agents de protection pour des ordinateurs clients qui sont connectés et des ordinateurs clients qui ne sont pas connectés.

### <a name="update-a-protection-agent-for-a-connected-client-computer"></a>Mettre à jour un agent de protection pour un ordinateur client connecté

1. Dans la console Administrateur du serveur de sauvegarde, sélectionnez **Gestion** > **Agents**.

2. Dans le volet d’affichage, sélectionnez les ordinateurs clients dont vous souhaitez mettre à jour l’agent de protection.

  > [!NOTE]
  > La colonne **Mises à jour d’agent** indique lorsqu’une mise à jour de l’agent de protection est disponible pour chaque ordinateur protégé. Dans le volet **Actions**, l’action **Mettre à jour** n’est proposée que lorsqu’un ordinateur protégé est sélectionné et que des mises à jour sont disponibles.
  >
  >

3. Pour installer les agents de protection à jour sur les ordinateurs sélectionnés, dans le volet **Actions**, sélectionnez **Mettre à jour**.

### <a name="update-a-protection-agent-on-a-client-computer-that-is-not-connected"></a>Mettre à jour un agent de protection pour un ordinateur client qui n’est pas connecté

1. Dans la console Administrateur du serveur de sauvegarde, sélectionnez **Gestion** > **Agents**.

2. Dans le volet d’affichage, sélectionnez les ordinateurs clients dont vous souhaitez mettre à jour l’agent de protection.

  > [!NOTE]
   > La colonne **Mises à jour d’agent** indique lorsqu’une mise à jour de l’agent de protection est disponible pour chaque ordinateur protégé. Dans le volet **Actions**, l’action **Mettre à jour** n’est proposée pour un ordinateur protégé sélectionné que lorsque des mises à jour sont disponibles.
  >
  >

3. Pour installer les agents de protection à jour sur les ordinateurs sélectionnés, sélectionnez **Mettre à jour**.

4. Pour un ordinateur client qui n’est pas connecté au réseau, la colonne **État de l’agent** indique **Mise à jour en attente** tant que l’ordinateur n’est pas connecté au réseau.

  Une fois qu’un ordinateur client est connecté au réseau, la colonne **Mises à jour d’agent** de cet ordinateur indique l’état **Mise à jour**.
  
### <a name="move-legacy-protection-groups-from-old-version-and-sync-the-new-version-with-azure"></a>Déplacer des groupes de protection hérités à partir de l’ancienne version et synchroniser la nouvelle version avec Azure

Une fois le serveur de sauvegarde Azure et le système d’exploitation mis à jour, vous êtes prêt à protéger les nouvelles sources de données à l’aide de Modern Backup Storage. Toutefois, les sources de données déjà protégées continueront d’être protégées selon l’ancienne procédure, car elles se trouvaient sur le serveur de sauvegarde Azure, alors que toute nouvelle protection utilisera Modern Backup Storage.

La migration des sources de données depuis le mode de protection hérité vers Modern Backup Storage comprend les étapes ci-dessous.

• Ajoutez le ou les nouveaux volumes au pool de stockage DPM et assignez des balises de source de données et des noms conviviaux si vous le souhaitez.
• Pour chaque source de données en mode hérité, arrêtez la protection et conservez les données protégées.  Ainsi, les anciens points de récupération peuvent être récupérés après la migration.

• Créez un groupe de protection et sélectionnez les sources de données qui doivent être stockées à l’aide du nouveau format.
• DPM effectue une copie du réplica à partir du stockage de sauvegarde existant dans le volume Modern Backup Storage localement.
Remarque : Cela est considéré comme un travail d’opération de postrécupération • Tous les nouveaux points de synchronisation et de récupération sont ensuite stockés dans Modern Backup Storage.
• Les anciens points de récupération sont nettoyés quand ils expirent, libérant ainsi l’espace disque.
• Une fois tous les volumes existants supprimés de l’ancien stockage, le disque peut être enlevé de la sauvegarde Azure et du système.
• Créez une sauvegarde de la base de données DPM Azure.

Partie 2 :- Éléments importants > Le nouveau serveur doit porter le même nom que le serveur de sauvegarde Azure d’origine. Vous ne pouvez pas modifier le nom du nouveau serveur de sauvegarde Azure si vous souhaitez utiliser l’ancien pool de stockage et la base de données DPM pour conserver des points de récupération. Une sauvegarde de la base de données DPM est nécessaire en prévision de sa restauration.

1) Arrêtez le serveur de sauvegarde Azure d’origine ou mettez-le hors connexion.
2) Réinitialisez le compte de la machine dans Active Directory.
3) Installez Server 2016 sur une nouvelle machine et attribuez à celle-ci le même nom que le serveur de sauvegarde Azure d’origine.
4) Joignez le domaine.
5) Installez le serveur de sauvegarde Azure, version 2 (déplacez les disques du pool de stockage de DPM depuis l’ancien serveur et effectuez l’importation).
6) Restaurez la base de données DPM obtenue à la fin de la partie 2.
7) Attachez le stockage à partir du serveur de sauvegarde d’origine au nouveau serveur.
8) Depuis SQL, restaurez la base de données DPM.
9) À partir de la ligne de commande d’administration sur le nouveau serveur, accédez au répertoire d’installation de Sauvegarde Microsoft Azure, puis au dossier bin.

Exemple de chemin : C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\
vers la sauvegarde Azure Exécutez DPMSYNC -SYNC

10) Exécutez DPMSYNC-SYNC. Remarque : Si vous avez ajouté de nouveaux disques au pool de stockage DPM au lieu de déplacer les anciens, exécutez DPMSYNC -Reallocatereplica.

## <a name="new-powershell-cmdlets-in-v2"></a>Nouveaux cmdlets PowerShell dans la version 2

Lorsque vous installez le serveur de sauvegarde Azure v2, deux nouveaux cmdlets sont disponibles : 
* [Mount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787159.aspx)
* [Dismount-DPMRecoveryPoint](https://technet.microsoft.com/library/mt787158.aspx)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment préparer votre serveur ou commencer à protéger une charge de travail :
- [Préparer les charges de travail du serveur de sauvegarde](backup-azure-microsoft-azure-backup.md)
- [Utiliser le Serveur de sauvegarde pour sauvegarder un serveur VMware](backup-azure-backup-server-vmware.md)
- [Utiliser le serveur de sauvegarde pour sauvegarder SQL Server](backup-azure-sql-mabs.md)
- [Utiliser Modern Backup Storage avec le serveur de sauvegarde](backup-mabs-add-storage.md)

