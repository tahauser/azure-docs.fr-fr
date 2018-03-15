---
title: "Résolution des problèmes de VHD joints sur des machines virtuelles Azure Linux | Microsoft Docs"
description: "Comment résoudre des problèmes tels que des redémarrages inattendus de machines virtuelles Linux ou des problèmes de suppression d’un compte de stockage contenant des VHD joints."
keywords: "connexion ssh refusée, erreur ssh, ssh azure, échec de connexion SSH"
services: virtual-machines-linux
author: iainfoulds
manager: jeconnoc
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/28/2018
ms.author: iainfou
ms.openlocfilehash: 10f5e2f4a48d335c6b7d05b5eb5b577765675c0e
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="troubleshoot-attached-vhds-on-azure-linux-virtual-machines"></a>Résolution des problèmes de VHD joints sur des machines virtuelles Azure Linux

Les machines virtuelles Azure s’appuient sur des disques durs virtuels (VHD) pour le disque du système d’exploitation et tout disque de données joint. Les VHD sont stockés en tant qu’objets blob de pages dans un ou plusieurs comptes de stockage Azure. Cet article décrit comment résoudre les problèmes courants pouvant survenir avec des VHD. 

  * [Redémarrages inattendus de machines virtuelles avec des VHD joints]
  * [Erreurs de suppression de stockage dans un déploiement Resource Manager]

## <a name="you-are-experiencing-unexpected-reboots"></a>Redémarrages inattendus de machines virtuelles avec des VHD joints

Si une machine virtuelle Azure est associée à un grand nombre de VHD dans le même compte de stockage, vous risquez de dépasser les cibles d’évolutivité pour un compte de stockage individuel, ce qui provoquera une panne de la machine virtuelle. Consultez les métriques par minute pour le compte de stockage (**TotalRequests**/**TotalIngress**/**TotalEgress**) et vérifiez la présence de pics dépassant les cibles d’évolutivité pour un compte de stockage. La section « [Les métriques indiquent une augmentation de la valeur PercentThrottlingError] » vous aidera à déterminer si une limitation a été appliquée à votre compte de stockage.

En général, chaque opération d’entrée ou de sortie individuelle sur un disque dur virtuel à partir d’une machine virtuelle traduit les opérations **Get Page** ou **Put Page** sur l’objet blob de la page sous-jacente. Vous pouvez donc utiliser les opérations d’E/S par seconde (IOPS) estimées pour votre environnement pour déterminer le nombre de disques durs virtuels que vous pouvez avoir dans un compte de stockage unique, sur base du comportement spécifique de votre application. Microsoft recommande qu’un compte de stockage ne comporte pas plus de 40 disques. Pour obtenir des détails sur les cibles d’extensibilité des comptes de stockage, en particulier le taux de demandes total et la bande passante totale pour le type de compte de stockage que vous utilisez, consultez [Cibles de performance et d’extensibilité Stockage Azure](../../storage/common/storage-scalability-targets.md).

Si vous dépassez les cibles d’évolutivité pour votre compte de stockage, placez vos VHD dans plusieurs comptes de stockage afin de réduire l’activité dans chaque compte individuel.

## <a name="storage-delete-errors-in-rm"></a>Erreurs de suppression de stockage dans un déploiement Resource Manager

Cette section fournit des conseils pour résoudre les erreurs suivantes lorsque vous essayez de supprimer un compte de stockage Azure, un conteneur ou un blob dans un déploiement Azure Resource Manager.

>**Échec de la suppression du compte de stockage « StorageAccountName ». Erreur : Le compte de stockage ne peut pas être supprimé en raison de ses artefacts en cours d’utilisation.**

>**Failed to delete # out of # container(s):<br>vhds (Impossible de supprimer # conteneur(s) sur # : disques durs virtuels) :il existe actuellement un bail sur le conteneur et aucun ID de bail n’a été spécifié dans la demande.**

>**Failed to delete # out of # blobs:<br>BlobName.vhd (Impossible de supprimer # blob(s) sur # : BlobName.vhd) : il existe actuellement un bail sur le blob et aucun ID de bail n’a été spécifié dans la demande.**

Les VHD utilisés dans des machines virtuelles Azure sont des fichiers .vhd stockés comme objets blob de pages dans un compte de stockage Standard ou Premium dans Azure.  Vous trouverez plus d’informations sur les disques Azure [ici](../../virtual-machines/windows/about-disks-and-vhds.md). Azure empêche la suppression d’un disque qui est joint à une machine virtuelle pour éviter une altération des données. Il empêche également la suppression de conteneurs et de comptes de stockage qui ont un objet blob de pages joint à une machine virtuelle. 

Le processus de suppression d’un compte de stockage, d’un conteneur ou d’un blob lorsque vous recevez l’une de ces erreurs est le suivant : 
1. [Identifier les blobs joints à une machine virtuelle](#step-1-identify-blobs-attached-to-a-vm)
2. [Supprimer les machines virtuelles avec un **disque de système d’exploitation** joint](#step-2-delete-vm-to-detach-os-disk)
3. [Détacher tous les**disques de données** des autres machines virtuelles](#step-3-detach-data-disk-from-the-vm)

Effectuez une nouvelle tentative de suppression du compte de stockage, du conteneur ou du blob après avoir terminé ces étapes.

### <a name="step-1-identify-blob-attached-to-a-vm"></a>Étape 1 : Identifier les blobs joints à une machine virtuelle

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scénario 1 : Suppression d’un blob - identifier la machine virtuelle jointe
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Toutes les ressources**. Accédez au compte de stockage, sous **Service BLOB** sélectionnez **Conteneurs** et accédez au blob à supprimer.
3. Si le paramètre **État du bail** du blob est défini sur **Alloué**, cliquez avec le bouton droit et sélectionnez **Modifier les métadonnées** pour ouvrir le volet Métadonnées d’objet blob. 

    ![Capture d’écran du portail, avec les blobs du compte de stockage et clic droit sur « Modifier les métadonnées » mis en surbrillance](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. Dans le volet Métadonnées d’objet blob, vérifiez et enregistrez la valeur de **MicrosoftAzureCompute_VMName**. Cette valeur correspond au nom de la machine virtuelle à laquelle le disque dur virtuel est joint. (Consultez la section **Important** si ce champ n’existe pas.)
5. Dans le volet Métadonnées d’objet blob, vérifiez et enregistrez la valeur de **MicrosoftAzureCompute_DiskType**. Cette valeur permet d’identifier si le disque joint est un disque de système d’exploitation ou un disque de données (consultez la section **Important** si ce champ n’existe pas). 

     ![Capture d’écran du portail, avec le volet « Métadonnées d’objet Blob » de stockage ouvert](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Si le type de disque de blob est **OSDisk**, suivez [Étape 2 : Supprimer une machine virtuelle pour détacher le disque de système d’exploitation](#step-2-delete-vm-to-detach-os-disk). Si le type de disque de blob est **DataDisk**, suivez la procédure décrite dans [Étape 3 : détacher un disque de données de la machine virtuelle](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Si **MicrosoftAzureCompute_VMName** et **MicrosoftAzureCompute_DiskType** n’apparaissent pas dans les métadonnées du blob, cela signifie que le blob est explicitement alloué et qu’il n’est pas joint à une machine virtuelle. Les blobs alloués ne peuvent pas être supprimés tant que le bail n’a pas été résilié. Pour résilier le bail, cliquez avec le bouton droit sur le blob et sélectionnez **Résilier le bail**. Les blobs alloués qui ne sont pas joints à une machine virtuelle empêchent la suppression du blob, mais n’empêchent pas la suppression d’un conteneur ou d’un compte de stockage.

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scénario 2 : Suppression d’un conteneur - identifier tous les blobs du conteneur qui sont joints aux machines virtuelles
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Toutes les ressources**. Accédez au compte de stockage, sous **Service BLOB** sélectionnez **Conteneurs** et accédez au conteneur à supprimer.
3. Cliquez pour ouvrir le conteneur, et la liste des blobs qu’il contient s’affiche. Identifiez tous les blobs avec Type d’objet blob = **Objet blob de pages** et État du bail = **Alloué** dans cette liste. Suivez le [scénario 1](#step-1-identify-blobs-attached-to-a-vm) pour identifier la machine virtuelle associée à chacun de ces blobs.

    ![Capture d’écran du portail, avec les blobs du compte de stockage et « État du bail » avec « Alloué » mis en surbrillance](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Suivez [l’étape 2](#step-2-delete-vm-to-detach-os-disk) et [l’étape 3](#step-3-detach-data-disk-from-the-vm) pour supprimer des machines virtuelles avec **OSDisk** et détacher **DataDisk**. 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scénario 3 : Suppression du stockage du compte - identifier tous les blobs du compte de stockage qui sont joints aux machines virtuelles
1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Toutes les ressources**. Accédez au compte de stockage, sous **Service BLOB** sélectionnez **Conteneurs**.

    ![Capture d’écran du portail, avec les conteneurs du compte de stockage et « État du bail » avec « Alloué » mis en surbrillance](./media/troubleshoot-vhds/utd-containers-sm.png)

3. Dans le volet **Conteneurs**, identifiez tous les conteneurs dans lesquels le paramètre **État du bail** est défini sur **Alloué** et suivez le [scénario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) pour chaque conteneur **alloué**.
4. Suivez [l’étape 2](#step-2-delete-vm-to-detach-os-disk) et [l’étape 3](#step-3-detach-data-disk-from-the-vm) pour supprimer des machines virtuelles avec **OSDisk** et détacher **DataDisk**. 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>Étape 2 : Supprimer une machine virtuelle pour détacher le disque de système d’exploitation
Si le disque dur virtuel est un disque de système d’exploitation, vous devez supprimer la machine virtuelle avant de pouvoir supprimer le disque dur virtuel joint. Aucune action supplémentaire ne sera nécessaire pour les disques de données joints à la même machine virtuelle une fois ces étapes effectuées :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle à laquelle le disque dur virtuel est joint.
4. Assurez-vous qu’aucun élément n’utilise activement la machine virtuelle, et que vous n’avez plus besoin de la machine virtuelle.
5. En haut du volet **Détails de la machine virtuelle**, sélectionnez **Supprimer**, puis cliquez sur **Oui** pour confirmer.
6. La machine virtuelle doit être supprimée, mais le disque dur virtuel peut être conservé. Toutefois, le disque dur virtuel ne devrait plus être joint à une machine virtuelle, ni avoir de bail associé. La publication du bail peut nécessiter quelques minutes. Pour vérifier que le bail est résilié, accédez à l’emplacement du blob et dans le volet **Propriétés de l’objet blob**, le paramètre **État du bail** doit être défini sur **Disponible**.

### <a name="step-3-detach-data-disk-from-the-vm"></a>Étape 3 : Détacher un disque de données de la machine virtuelle
Si le disque dur virtuel est un disque de données, détachez le disque dur virtuel de la machine virtuelle pour supprimer le bail :

1. Connectez-vous au [Portail Azure](https://portal.azure.com).
2. Dans le menu Hub, sélectionnez **Machines virtuelles**.
3. Sélectionnez la machine virtuelle à laquelle le disque dur virtuel est joint.
4. Sélectionnez **Disques** dans le volet **Détails de la machine virtuelle**.
5. Sélectionnez le disque de données à supprimer auquel le disque dur virtuel est joint. Vous pouvez déterminer le blob joint dans le disque en vérifiant l’URL du disque dur virtuel.
6. Vous pouvez vérifier l’emplacement du blob en cliquant sur le disque pour vérifier le chemin d’accès dans le champ **URI VHD**.
7. Sélectionnez **Modifier** en haut du volet **Disques**.
8. Cliquez sur **l’icône Détacher** du disque de données à supprimer.

     ![Capture d’écran du portail, avec le volet « Métadonnées d’objet Blob » de stockage ouvert](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Sélectionnez **Enregistrer**. Le disque est désormais détaché de la machine virtuelle, et le VHD n’est plus alloué. La publication du bail peut nécessiter quelques minutes. Pour vérifier que le bail a été résilié, accédez à l’emplacement du blob et dans le volet **Propriétés de l’objet blob**, le paramètre **État du bail** doit être défini sur **Déverrouillé** ou **Disponible**.



[Redémarrages inattendus de machines virtuelles avec des VHD joints]: #you-are-experiencing-unexpected-reboots
[Erreurs de suppression de stockage dans un déploiement Resource Manager]: #storage-delete-errors-in-rm