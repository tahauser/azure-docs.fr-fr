---
title: "Examen de l’architecture de réplication des machines virtuelles Azure entre les régions Azure | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des composants et de l’architecture utilisés lors de la réplication de machines virtuelles Azure entre régions Azure avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: a449969e627c67660973ccf17123958678dd8844
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2017
---
# <a name="azure-to-azure-replication-architecture"></a>Architecture de la réplication Azure vers Azure


Cet article décrit l’architecture et les processus utilisés quand vous répliquez, basculez et récupérez des machines virtuelles Azure entre des régions Azure, à l’aide du service [Azure Site Recovery](../site-recovery-overview.md).

>[!NOTE]
>La réplication de machine virtuelle Azure avec le service Site Recovery est actuellement en préversion.



## <a name="architectural-components"></a>Composants architecturaux

Le graphique suivant fournit une vue d’ensemble d’un environnement de machine virtuelle Azure dans une région spécifique (en l’occurrence, Est des États-Unis). Dans un environnement de machine virtuelle Azure :
- Les applications peuvent s’exécuter sur des machines équipées de disques réparties sur plusieurs comptes de stockage.
- Les machines virtuelles peuvent être incluses dans un ou plusieurs sous-réseaux d’un réseau virtuel.


**Réplication Azure vers Azure**

![environnement client](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Processus de réplication

### <a name="step-1"></a>Étape 1

Quand vous activez la réplication de machines virtuelles Azure, les ressources indiquées ci-après sont automatiquement créées dans la région cible en fonction des paramètres de la région source. Vous pouvez personnaliser les paramètres des ressources cibles en fonction des besoins. 

![Activer le processus de réplication, étape 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Ressource** | **Détails**
--- | ---
**Groupe de ressources cible** | Groupe de ressources auquel appartiennent les machines virtuelles répliquées après le basculement.
**Réseau virtuel cible** | Réseau virtuel dans lequel les machines virtuelles répliquées sont situées après le basculement. Un mappage réseau est créé entre les réseaux virtuels source et cible, et inversement.
**Comptes Stockage de cache** | Avant que les modifications apportées aux machines virtuelles sources soient répliquées vers un compte de stockage cible, elles sont suivies et envoyées au compte de stockage de cache dans l’emplacement cible. Cela garantit un impact minimal sur les applications de production s’exécutant sur la machine virtuelle.
**Comptes de stockage cibles**  | Comptes de stockage dans l’emplacement cible vers lesquels les données sont répliquées.
**Groupes à haute disponibilité cibles**  | Groupes à haute disponibilité dans lesquels se trouvent les machines virtuelles répliquées après basculement.

### <a name="step-2"></a>Étape 2

À mesure que la réplication est activée, le service Mobilité de l’extension de Site Recovery est automatiquement installé sur la machine virtuelle :

1. La machine virtuelle est inscrite auprès de Site Recovery.

2. Une réplication continue est configurée pour la machine virtuelle. Les écritures de données sur les disques de machine virtuelle sont transférées en continu vers le compte de stockage de cache dans l’emplacement source.

   ![Activer le processus de réplication, étape 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

  
 Site Recovery n’a jamais besoin de connectivité entrante à la machine virtuelle. Seule est requise une connectivité sortante aux URL/adresses IP du service Site Recovery, aux URL/adresses IP d’authentification d’Office 365, et aux adresses IP de compte de stockage de cache.

### <a name="step-3"></a>Étape 3 :

Une fois que la réplication continue est en cours, les écritures sur disque sont immédiatement transférées vers le compte de stockage de cache. Site Recovery traite les données, puis les envoie au compte de stockage cible. Une fois les données traitées, des points de récupération sont générés dans le compte de stockage cible à intervalles de quelques minutes.

## <a name="failover-process"></a>Processus de basculement

Quand vous démarrez un basculement, les machines virtuelles sont créées dans le groupe de ressources cible, le réseau virtuel cible, le sous-réseau cible et dans le groupe à haute disponibilité cible. Lors d’un basculement, vous pouvez utiliser n’importe quel point de récupération.

![Processus de basculement](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Étapes suivantes

Suivez le [démarrage rapide](azure-to-azure-quickstart.md) pour activer la réplication d’une machine virtuelle Azure vers une région secondaire.

