---
title: "Notes de publication de StorSimple série 8000 Update 4 | Microsoft Docs"
description: "Décrit les nouvelles fonctionnalités, les problèmes et les solutions de contournement associés à StorSimple série 8000 Update 4."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: f1c0734b07287d95a2f5031e010f0e407d3a816f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/25/2018
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>Notes de publication de StorSimple série 8000 Update 4
> [!NOTE]
> Le portail Azure Classic pour StorSimple est déconseillé. Vos instances de StorSimple Device Manager seront automatiquement déplacées vers le nouveau portail Azure à la date de désapprobation planifiée. Vous serez prévenu de ce déplacement par un e-mail et une notification du portail. Ce document sera également bientôt retiré. Si vous avez des questions concernant le déplacement, consultez [FAQ : Déplacement vers le portail Azure](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivantes décrivent les nouvelles fonctionnalités et identifient les problèmes critiques non résolus relatifs à StorSimple série 8000 Update 4. Elles contiennent également une liste des mises à jour du logiciel StorSimple incluses dans cette version. 

Update 4 peut être appliquée à n’importe quel appareil StorSimple exécutant la version Release (GA) ou toute version entre Update 0.1 et Update 3.1. La version d’appareil associée à Update 4 est 6.3.9600.17820.

Lisez les informations contenues dans les notes de publication avant de déployer la mise à jour dans votre solution StorSimple.

> [!IMPORTANT]
> * Update 4 comprend le logiciel de l’appareil, le microprogramme USM, le pilote et le microprogramme LSI, le microprogramme du disque, Storport et Spaceport, la mise à jour de sécurité et d’autres mises à jour du système d’exploitation. L’installation de cette mise à jour prend environ 4 heures. La mise à jour du microprogramme de disque est une mise à jour perturbatrice qui entraîne une interruption de service pour votre appareil. Nous vous recommandons d’appliquer cette mise à jour 4 pour maintenir votre appareil à jour. 
> * Pour les nouvelles versions, vous ne voyez pas immédiatement les mises à jour, car nous effectuons un déploiement échelonné des mises à jour. Revérifiez les mises à jour dans quelques jours, car elles seront bientôt disponibles.

## <a name="whats-new-in-update-4"></a>Nouveautés d’Update 4

Update 4 comporte les principaux correctifs de bogues et améliorations suivants.

* **Algorithmes automatiques de récupération d’espace plus intelligents** : dans Update 4, les algorithmes automatiques de récupération d’espace ont été améliorés pour ajuster les cycles de récupération d’espace en fonction de l’espace récupéré attendu disponible dans le cloud. 

* **Améliorations des performances des volumes épinglés localement** : Update 4 a amélioré les performances des volumes épinglés localement dans les scénarios de réception d’une grande quantité de données (comparable à la taille du volume).

* **Restauration basée sur une carte thermique** : dans les versions antérieures, suite à une récupération d’urgence, les données étaient restaurées à partir du cloud en fonction des modèles d’accès, ce qui ralentissait les performances. 

    Update 4 implémente une nouvelle fonctionnalité qui suit les données fréquemment consultées pour créer une carte thermique de l’appareil en cours d’utilisation avant la récupération d’urgence (la chaleur est élevée pour les segments de données les plus utilisés et basse pour les segments les moins utilisés). Après une récupération d’urgence, StorSimple utilise la carte thermique pour restaurer et rafraîchir automatiquement les données à partir du cloud. 

    Toutes les restaurations sont désormais basées sur une carte thermique. Pour plus d’informations sur la façon d’interroger et d’annuler les travaux de restauration et de rafraîchissement basés sur une carte thermique, consultez la [référence à l’applet de commande Windows PowerShell pour StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

* **Outil de diagnostic StorSimple** : l’outil de diagnostic StorSimple désormais disponible dans Update 4 permet de diagnostiquer et de résoudre facilement les problèmes liés à l’intégrité du système, du réseau, des performances et des composants matériels. Cet outil est exécuté via Windows PowerShell pour StorSimple. Pour plus d’informations, consultez [Dépannage à l’aide de l’outil Diagnostics de StorSimple](storsimple-8000-diagnostics.md).

* **Outil de migration StorSimple basé sur l’interface utilisateur** : avant cette version, la migration des données à partir des séries 5000-7000 exigeait que les utilisateurs exécutent une partie du flux de travail de migration à l’aide de l’interface Azure PowerShell. Cette version fournit un outil de migration StorSimple facile à utiliser basé sur l’interface utilisateur qui facilite ce même flux de travail de migration. Cet outil permet également de consolider les compartiments de récupération. 

* **Modifications relatives à FIPS** : à partir de cette version, FIPS est activée par défaut sur tous les appareils StorSimple série 8000 pour les comptes de cloud public Microsoft Azure Government et Azure.

* **Modifications de la mise à jour** : dans cette version, les bogues liés aux échecs de mise à jour ont été résolus.

* **Alerte de défaillance de disque** : une nouvelle alerte avertissant l’utilisateur des défaillances imminentes de disques a été ajoutée dans cette version. Si vous rencontrez cette alerte, demandez au support technique de Microsoft de vous livrer un disque de remplacement. Pour plus d’informations, consultez [Alertes matérielles sur votre appareil StorSimple](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Modifications relatives au remplacement du contrôleur** : une applet de commande permettant à l’utilisateur d’interroger l’état du processus de remplacement du contrôleur a été ajoutée dans cette version. Pour plus d’informations, consultez [l’applet de commande pour interroger l’état du remplacement du contrôleur](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Problèmes résolus dans Update 4

Le tableau suivant récapitule les problèmes qui ont été résolus dans Update 4.    

| Non  | Fonctionnalité | Problème | S’applique à un appareil physique | S’applique à un appareil virtuel |
| --- | --- | --- | --- | --- |
| 1 |Basculement |Dans la version précédente, après le basculement, un problème lié au nettoyage sur le site du client était observé. Ce problème a été résolu dans cette version. |OUI |OUI |
| 2 |Volumes épinglés localement  |Dans la version précédente, un problème de création de volume associé pour les volumes épinglés localement entraînait l’échec de création du volume. La cause racine de ce problème a été identifiée et il a été résolu dans cette version. |OUI |Non  |
| 3 |Package de prise en charge |Dans la version précédente, des problèmes liés au package de prise en charge qui provoquaient une exception System.OutOfMemory ou d’autres erreurs entraînaient l’échec de création du package de prise en charge. Ces bogues ont été résolus dans cette version. |OUI |OUI |
| 4 |Surveillance |Dans la version précédente, un problème lié aux graphiques de surveillance des volumes épinglés localement entraînait l’affichage de la consommation dans EB. Ce bogue a été résolu dans cette version. |OUI |OUI |
| 5. |Migration |Dans la version précédente, plusieurs problèmes étaient liés à la fiabilité de la migration des appareils de la série 5000-7000 vers la série 8000. Ces problèmes ont été résolus dans cette version. |OUI |OUI |
| 6. |Mettre à jour |Dans les versions précédentes, en cas d’échec d’une mise à jour, les contrôleurs entraient en mode de récupération ; par conséquent, l’utilisateur ne pouvait pas poursuivre la mise à jour et devait contacter le Support Microsoft. <br> Ce comportement a été modifié dans cette version. Si l’utilisateur rencontre un échec de mise à jour une fois que les deux contrôleurs exécutent la même version (Update 4), ces derniers n’entrent pas en mode de récupération. Si l’utilisateur rencontre cette erreur, nous recommandons d’attendre un peu et de recommencer la mise à jour. La nouvelle tentative peut réussir. Si cette nouvelle tentative échoue, contactez le Support Microsoft. |OUI |OUI |


## <a name="known-issues-in-update-4-from-previous-releases"></a>Problèmes connus dans Update 4 depuis les versions précédentes

Il n’existe aucun nouveau problème connu dans Update 4. Pour une liste des problèmes toujours présents dans Update 4 depuis les versions précédentes, consultez les [notes de publication d’Update 3](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Mises à jour du microprogramme et du contrôleur Serial-Attached SCSI (SAS) dans Update 4

Cette version contient des mises à jour du microprogramme et du pilote LSI et du contrôleur SAS. Pour plus d’informations sur la procédure d’installation de ces mises à jour, consultez [Installer Update 4](storsimple-install-update-4.md) sur votre appareil StorSimple.

## <a name="virtual-device-updates-in-update-4"></a>Mises à jour des appareils virtuels dans Update 4

Cette mise à jour ne peut pas s’appliquer à l’équipement cloud StorSimple (également connu sous le nom d’appareil virtuel). De nouveaux appareils virtuels devront être créés. 

## <a name="next-step"></a>Étape suivante

Découvrez comment [installer Update 4](storsimple-install-update-4.md) sur votre appareil StorSimple.

