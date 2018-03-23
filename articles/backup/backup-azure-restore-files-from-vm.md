---
title: 'Sauvegarde Azure : récupérer des fichiers et des dossiers à partir d’une sauvegarde de machine virtuelle Azure | Microsoft Docs'
description: Récupérer des fichiers à partir d’un point de récupération de machine virtuelle Azure
services: backup
documentationcenter: dev-center-name
author: pvrk
manager: shivamg
keywords: récupération au niveau élément ; récupération de fichiers à partir d’une sauvegarde de machine virtuelle Azure ; restaurer des fichiers à partir d’une machine virtuelle Azure
ms.assetid: f1c067a2-4826-4da4-b97a-c5fd6c189a77
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/20/2017
ms.author: pullabhk;markgal
ms.openlocfilehash: 0fce38db010a77ac61145ef63f616e0e466c95e2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="recover-files-from-azure-virtual-machine-backup"></a>Récupérer des fichiers à partir d’une sauvegarde de machine virtuelle Azure

La sauvegarde Azure offre la possibilité de restaurer des [machines virtuelles et des disques Azure](./backup-azure-arm-restore-vms.md) à partir de sauvegardes de machines virtuelles Azure, également appelées points de restauration. Cet article explique comment récupérer des fichiers et des dossiers à partir d’une sauvegarde de machine virtuelle Azure. La restauration des fichiers et des dossiers est disponible uniquement pour les machines virtuelles Azure déployées à l’aide du modèle Resource Manager et protégées dans un coffre Recovery Services.

> [!Note]
> Cette fonctionnalité est disponible pour les machines virtuelles Azure déployées suivant le modèle Resource Manager et protégées dans un coffre Recovery Services.
> La récupération de fichiers à partir d’une sauvegarde de machine virtuelle chiffrée n’est pas prise en charge.
>

## <a name="mount-the-volume-and-copy-files"></a>Monter le volume et copier les fichiers

Pour restaurer des fichiers ou dossiers à partir du point de restauration, accédez à la machine virtuelle et choisissez le point de restauration. 

1. Se connecter au [Portail Azure](http://portal.Azure.com) dans le menu de gauche, cliquez sur **Machines virtuelles**. Dans la liste des machines virtuelles, sélectionnez la machine virtuelle pour ouvrir son tableau de bord. 

2. Dans le menu de la machine virtuelle, cliquez sur **Sauvegarder** pour ouvrir le tableau de bord de sauvegarde.

    ![Ouvrir l’élément de sauvegarde du coffre Recovery Services.](./media/backup-azure-restore-files-from-vm/open-vault-from-vm.png)

3. Dans le menu du tableau de bord de sauvegarde, cliquez sur **Récupération de fichiers** pour ouvrir son menu.

    ![Menu de récupération de fichiers](./media/backup-azure-restore-files-from-vm/file-recovery-blade.png)

4. Dans le menu déroulant **Sélectionner le point de récupération**, sélectionnez le point de récupération qui contient les fichiers dont vous avez besoin. Le dernier point de récupération est sélectionné par défaut.

5. Pour télécharger le logiciel utilisé pour copier des fichiers à partir du point de récupération, cliquez sur **Download Executable** (Télécharger le fichier exécutable) pour une machine virtuelle Azure Windows ou **Download Script** (Télécharger le script) pour une machine virtuelle Azure Linux. 

    ![Mot de passe généré](./media/backup-azure-restore-files-from-vm/download-executable.png)

    Azure télécharge le fichier exécutable ou le script sur l’ordinateur local.

    ![Message de téléchargement du fichier exécutable ou du script](./media/backup-azure-restore-files-from-vm/run-the-script.png)

    Pour exécuter le fichier exécutable ou le script en tant qu’administrateur, il est recommandé de le sauvegarder sur votre ordinateur.

6. Le fichier exécutable ou le script est protégé par mot de passe et nécessite un mot de passe. Dans le menu **Récupération de fichiers**, cliquez sur le bouton Copier pour charger le mot de passe en mémoire.

    ![Mot de passe généré](./media/backup-azure-restore-files-from-vm/generated-pswd.png)

7. À partir de l’emplacement de téléchargement (généralement le dossier Téléchargements), cliquez avec le bouton droit sur le fichier exécutable ou le script et exécutez-le en tant qu’administrateur. Lorsque vous y êtes invité, tapez le mot de passe ou collez le mot de passe en mémoire et appuyez sur Entrée. Une fois le mot de passe valide entré, le script se connecte au point de récupération.

    ![Menu de récupération de fichiers](./media/backup-azure-restore-files-from-vm/executable-output.png)

    Si vous exécutez le script sur un ordinateur avec un accès restreint, vérifiez l’accès aux éléments suivants :

    - download.microsoft.com
    - [points de terminaison Azure utilisés pour les sauvegardes de machines virtuelles Azure](backup-azure-arm-vms-prepare.md#establish-network-connectivity)
    - port sortant 3260

    Pour Linux, le script requiert les composants « open-iscsi » et « lshw » pour vous connecter au point de récupération. Si les composants n’existent pas sur l’ordinateur depuis lequel le script est exécuté, le script demande l’autorisation d’installer les composants. Autorisez l’installation des composants nécessaires.
    
    L’accès à download.microsoft.com est nécessaire pour télécharger les composants servant à créer un canal sécurisé entre l’ordinateur sur lequel est exécuté le script et les données du point de récupération.         

    Vous pouvez exécuter le script sur n’importe quelle machine dotée du même système d’exploitation (ou d’un système d’exploitation compatible) que la machine virtuelle sauvegardée. Consultez le [tableau de compatibilité des systèmes d’exploitation](backup-azure-restore-files-from-vm.md#system-requirements) pour connaître les systèmes d’exploitation compatibles. Si la machine virtuelle Azure protégée utilise des espaces de stockage Windows (pour les machines virtuelles Azure Windows) ou le LVM/des baies RAID (pour les machines virtuelles Linux), vous ne pouvez pas exécuter le script exécutable sur cette machine virtuelle. Au lieu de cela, exécutez le fichier exécutable ou le script sur n’importe quelle autre machine avec un système d’exploitation compatible.
 

### <a name="identifying-volumes"></a>Identification des volumes

#### <a name="for-windows"></a>Pour Windows

Lorsque vous exécutez l’exécutable, le système d’exploitation monte les nouveaux volumes et attribue des lettres de lecteur. Vous pouvez utiliser l’Explorateur Windows ou l’Explorateur de fichiers pour parcourir ces lecteurs. Les lettres de lecteur affectées aux volumes peuvent ne pas être les mêmes que celles de la machine virtuelle d’origine. Le nom de volume est cependant conservé. Par exemple, si le volume sur la machine virtuelle d’origine était « Disque de données (E:`\` », ce volume peut être connecté sur l’ordinateur local en tant que « Disque de données (n’importe quelle lettre:`\`) ». Parcourez tous les volumes mentionnés dans la sortie du script jusqu’à trouver vos fichiers/votre dossier.  
       
   ![Menu de récupération de fichiers](./media/backup-azure-restore-files-from-vm/volumes-attached.png)
           
#### <a name="for-linux"></a>Pour Linux

Sous Linux, les volumes de point de récupération sont montés sur le dossier dans lequel le script est exécuté. Les disques attachés, les volumes et les chemins d’accès de montage correspondants sont affichés en conséquence. Ces chemins de montage sont visibles aux utilisateurs ayant un accès root. Parcourez les volumes mentionnés dans la sortie du script.

  ![Menu Récupération de fichier Linux](./media/backup-azure-restore-files-from-vm/linux-mount-paths.png)
  

## <a name="closing-the-connection"></a>Fermeture de la connexion

Après avoir identifié les fichiers et les avoir copiés dans un emplacement de stockage local, supprimez (ou démontez) les lecteurs supplémentaires. Pour démonter les lecteurs, dans le menu **Récupération de fichier** du portail Azure, cliquez sur **Démonter les disques**.

![Démonter les disques](./media/backup-azure-restore-files-from-vm/unmount-disks3.png)

Une fois les disques démontés, vous recevez un message vous informant que l’opération a bien été effectuée. L’actualisation de la connexion de sorte que vous pouvez supprimer les disques peut prendre quelques minutes.

Sous Linux, une fois la connexion au point de récupération interrompue, le système d’exploitation ne supprime pas les chemins d’accès de montage correspondants automatiquement. Les chemins d’accès de montage existent en tant que volumes « orphelins » et ils sont visibles, mais génèrent une erreur lorsque vous accédez aux fichiers ou écrivez dessus. Ils peuvent être supprimés manuellement. Le script, lorsqu’il est exécuté, identifie de tels volumes existants à partir des points de récupération précédents et les nettoie après accord.

## <a name="special-configurations"></a>Configurations spéciales

### <a name="dynamic-disks"></a>Disques dynamiques

Si la machine virtuelle Azure protégée dispose de volumes ayant une ou plusieurs des caractéristiques suivantes, vous ne pouvez pas exécuter le script exécutable sur la même machine virtuelle. 

  - Volumes qui s’étendent sur plusieurs disques (volumes fractionnés et agrégés par bandes)
  - Volumes à tolérance de pannes (volumes RAID-5 ou en miroir) sur des disques dynamiques 

Au lieu de cela, exécutez le script exécutable sur n’importe quel autre ordinateur disposant d’un système d’exploitation compatible.

### <a name="windows-storage-spaces"></a>Espaces de stockage Windows

Les espaces de stockage Windows sont une technologie Windows qui vous permet de virtualiser le stockage. Avec les espaces de stockage Windows, vous pouvez regrouper des disques standards en pools de stockage. Ensuite, vous utilisez l’espace disponible dans ces pools de stockage pour créer des disques virtuels, appelés espaces de stockage.

Si la machine virtuelle Azure protégée utilise des espaces de stockage Windows, vous ne pouvez pas exécuter le script exécutable sur la même machine virtuelle. Au lieu de cela, exécutez le script exécutable sur n’importe quelle autre machine avec un système d’exploitation compatible.

### <a name="lvmraid-arrays"></a>Baies LVM/RAID

Sous Linux, le gestionnaire de volumes logiques (LVM) et/ou les baies RAID logicielles permettent de gérer des volumes logiques sur plusieurs disques. Si la machine virtuelle Linux protégée utilise LVM et/ou des baies RAID, vous ne pouvez pas exécuter le script sur la même machine virtuelle. Exécutez plutôt le script sur une autre machine avec un système d’exploitation compatible et qui prend en charge le système de fichiers de la machine virtuelle protégée.

La sortie du script suivante affiche les disques LVM et/ou des baies RAID et les volumes avec le type de partition.

   ![Menu de sortie de LVM Linux](./media/backup-azure-restore-files-from-vm/linux-LVMOutput.png)
   
Pour mettre ces partitions en ligne, exécutez les commandes dans les sections suivantes. 

**Pour les partitions LVM**

Pour répertorier les noms de groupes de volumes sous un volume physique.
```
$ pvs <volume name as shown above in the script output> 
```
Pour répertorier tous les volumes logiques, noms et chemins d’accès dans un groupe de volumes.

```
$ lvdisplay <volume-group-name from the pvs command’s results> 
```

Pour monter les volumes logiques sur le chemin d’accès de votre choix.

```
$ mount <LV path> </mountpath>
```



**Pour les tableaux RAID**

La commande suivante affiche des détails sur tous les disques RAID.

```
$ mdadm –detail –scan
```
 Le disque RAID pertinent s’affiche en tant que `/dev/mdm/<RAID array name in the protected VM>`

Utilisez la commande mount si le disque RAID dispose de volumes physiques.
```
$ mount [RAID Disk Path] [/mountpath]
```

Si le disque RAID contient un autre gestionnaire de volume logique configuré, utilisez la procédure précédente pour les partitions LVM, mais utilisez le nom de volume à la place du nom de disque RAID

## <a name="system-requirements"></a>Conditions requises pour le système

### <a name="for-windows"></a>Pour Windows

Le tableau suivant indique la compatibilité entre les systèmes d’exploitation des serveurs et des ordinateurs. Lors de la récupération de fichiers, vous ne pouvez pas restaurer des fichiers sur une version antérieure ou ultérieure du système d’exploitation. Par exemple, vous ne pouvez pas restaurer un fichier à partir d’une machine virtuelle sous Windows Server 2016 sur un ordinateur sous Windows Server 2012 ou Windows 8. Vous pouvez restaurer des fichiers à partir d’une machine virtuelle sur un système d’exploitation de serveur identique, ou sur un système d’exploitation client compatible.   

|Système d’exploitation serveur | Système d’exploitation client compatible  |
| --------------- | ---- |
| Windows Server 2016    | Windows 10 |
| Windows Server 2012 R2 | Windows 8.1 |
| Windows Server 2012    | Windows 8  |
| Windows Server 2008 R2 | Windows 7   |

### <a name="for-linux"></a>Pour Linux

Sur Linux, le système d’exploitation de l’ordinateur utilisé pour restaurer des fichiers doit prendre en charge le système de fichiers de la machine virtuelle protégée. Lorsque vous sélectionnez un ordinateur pour exécuter le script, vérifiez que l’ordinateur possède un système d’exploitation compatible et utilise l’une des versions identifiées dans le tableau suivant :

|Système d’exploitation Linux | Versions  |
| --------------- | ---- |
| Ubuntu | 12.04 et versions ultérieures |
| CentOS | 6.5 et versions ultérieures  |
| RHEL | 6.7 et versions ultérieures |
| Debian | 7 et versions ultérieures |
| Oracle Linux | 6.4 et versions ultérieures |
| SLES | 12 et versions ultérieures |
| openSUSE | 42.2 et versions ultérieures |

Le script requiert également les composants Python et bash pour exécuter et établir une connexion sécurisée avec le point de récupération.

|Composant | Version  |
| --------------- | ---- |
| bash | 4 et versions ultérieures |
| python | 2.6.6 et versions ultérieures  |
| TLS | La version 1.2 devrait être prise en charge  |

## <a name="troubleshooting"></a>Résolution de problèmes

Si vous rencontrez des problèmes lors de la récupération de fichiers à partir de machines virtuelles, consultez le tableau suivant pour plus d’informations.

| Message d’erreur/Scénario | Cause probable | Action recommandée |
| ------------------------ | -------------- | ------------------ |
| Sortie du script exécutable : *Exception lors de la connexion à la cible* |Le script n’est pas en mesure d’accéder au point de récupération. | Vérifiez si l’ordinateur remplit les conditions d’accès précédentes. |  
|   Sortie du script exécutable : *La cible a déjà été connectée via une session iSCSI.* | Le script a déjà été exécuté sur le même ordinateur et les lecteurs ont été connectés. | Les volumes du point de récupération ont déjà été connectés. Ils NE peuvent PAS être montés avec les mêmes lettres de lecteur que celles de la machine virtuelle d’origine. Parcourez tous les volumes disponibles dans l’Explorateur de fichiers pour votre fichier. |
| Sortie du script exécutable : *Ce script n’est pas valide, car les disques ont été démontés via le portail/ont dépassé la limite de 12 h. Téléchargez un nouveau script à partir du portail.* | Les disques ont été démontés à partir du portail ou la limite de 12 h a été dépassée |    Ce fichier exécutable en particulier n’est plus valide et ne peut pas être exécuté. Si vous souhaitez accéder aux fichiers de ce point de récupération, visitez le portail pour un obtenir un nouveau fichier exécutable.|
| Sur l’ordinateur sur lequel est exécuté le fichier .exe : les nouveaux volumes ne sont pas démontés après avoir cliqué sur le bouton Démonter |    L’initiateur iSCSI de l’ordinateur ne répond pas/actualise sa connexion à la cible et met à jour le cache. |    Attendez quelques minutes après avoir cliqué sur le bouton Démonter. Si les nouveaux volumes ne sont toujours pas démontés, parcourez tous les volumes. Cela force l’initiateur à actualiser la connexion et le volume est démonté avec un message d’erreur indiquant que le disque n’est pas disponible.|
| Sortie du script exécutable : Le script est exécuté avec succès, mais le message « New volumes attached » (Nouveaux volumes connectés) ne s’affiche pas dans la sortie du script | Il s’agit d’une erreur temporaire.   | Les volumes seraient déjà connectés. Ouvrez l’Explorateur pour parcourir les volumes. Si vous utilisez toujours le même ordinateur pour exécuter les scripts, essayez de le redémarrer et la liste devrait s’afficher lors des prochaines exécutions du fichier exécutable. |
| Pour Linux : impossible d’afficher les volumes souhaités | Le système d’exploitation de la machine sur laquelle est exécuté le script peut ne pas reconnaître le système de fichiers sous-jacent de la machine virtuelle protégée | Vérifiez si le point de récupération est cohérent en cas d’incident ou cohérent avec les fichiers. S’il est cohérent avec les fichiers, exécutez le script sur une autre machine dont le système d’exploitation reconnaît le système de fichiers de la machine virtuelle protégée |
| Pour Windows : impossible d’afficher les volumes souhaités | Les disques ont peut-être été attachés, mais les volumes n’ont pas été configurés. | À partir de l’écran de gestion de disque, identifiez les disques supplémentaires relatifs au point de récupération. Si l’état de l’un de ces disques est hors connexion, essayez de le mettre en ligne en cliquant avec le bouton droit sur le disque, puis sur « En ligne ».|
