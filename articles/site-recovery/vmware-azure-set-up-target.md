---
title: Préparation de l’environnement cible en vue de la réplication VMware sur Azure | Microsoft Docs
description: Cet article décrit comment préparer votre environnement cible Azure en vue d’une réplication de machines virtuelles VMware sur Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: bsiva
ms.openlocfilehash: 5f14bbed7ae59737f62fb736591775cb7ba495c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>Préparation de l’environnement cible en vue de la réplication VMware sur Azure

Cet article décrit comment préparer votre environnement cible Azure de manière à lancer la réplication de machines virtuelles VMware sur Azure.

## <a name="prerequisites"></a>Prérequis


L’article suppose que :
- Vous avez créé un coffre Recovery Services pour protéger vos machines virtuelles VMware. Vous pouvez créer un coffre Recovery Services dans le [portail Azure](http://portal.azure.com "portail Azure").
- Vous avez [configuré votre environnement local](vmware-azure-set-up-source.md) pour répliquer les machines virtuelles VMware vers Azure.

## <a name="prepare-target"></a>Préparer la cible

Après avoir réalisé **l’Étape 1 : Sélection de l’objectif de la protection** et **l’Étape 2 : Préparation de la source**, vous passez à **l’Étape 3 : Cible**.

![Préparer la cible](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Abonnement :** dans le menu déroulant, sélectionnez l’abonnement vers lequel vous souhaitez répliquer vos machines virtuelles.
2. **Modèle de déploiement :** sélectionnez le modèle de déploiement (Classique ou Gestionnaire de ressources).

Selon le modèle de déploiement choisi, une validation est exécutée pour vérifier que vous disposez au moins d’un compte de stockage et d’un réseau virtuel compatibles dans l’abonnement cible pour la réplication et le basculement de votre machine virtuelle.

Une fois les validations terminées avec succès, cliquez sur OK pour passer à l’étape suivante.

Si vous n’avez pas de compte de stockage Resource Manager ou de réseau virtuel compatible, vous pouvez en créer un en cliquant sur le bouton **+ Compte de stockage** ou sur le bouton **+ Réseau** en haut de la page.

## <a name="next-steps"></a>Étapes suivantes
[Configurer les paramètres de réplication](vmware-azure-set-up-replication.md)
