---
title: Gérer les interfaces réseau dans Azure Site Recovery pour la réplication d’un emplacement local sur Azure | Microsoft Docs
description: Décrit comment gérer les interfaces réseau pour la réplication d’un emplacement local sur Azure avec Azure Site Recovery.
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: manayar
ms.openlocfilehash: a0d42608dc689e5f084f4ec91858531feeac8033
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="manage-virtual-machine-network-interfaces-for-on-premises-to-azure-replication"></a>Gérer les interfaces réseau des machines virtuelles pour la réplication d’un emplacement local sur Azure

Une machine virtuelle (VM) dans Azure doit être associée à au moins une interface réseau. Elle peut avoir autant d’interfaces réseau qu’en prend en charge la taille de machine virtuelle.

Par défaut, la première interface réseau associée à une machine virtuelle Azure est définie comme interface réseau principale. Toutes les autres interfaces réseau de la machine virtuelle sont des interfaces réseau secondaires. Par défaut, tout le trafic sortant de la machine virtuelle est envoyé à partir de l’adresse IP affectée à la configuration IP principale de l’interface réseau principale.

Dans un environnement local, les machines virtuelles ou serveurs peuvent avoir plusieurs interfaces réseau pour différents réseaux au sein de l’environnement. Des réseaux distincts sont généralement utilisés pour effectuer des opérations spécifiques, telles que les mises à niveau, la maintenance et l’accès à Internet. Lors de la migration ou du basculement vers Azure à partir d’un environnement local, gardez à l’esprit que les interfaces réseau de la même machine virtuelle doivent toutes être connectées au même réseau virtuel.

Par défaut, Azure Site Recovery crée autant d’interfaces réseau sur une machine virtuelle Azure qu’il y a d’interfaces connectées au serveur local. Vous pouvez éviter la création d’interfaces réseau redondantes pendant la migration ou le basculement en modifiant les paramètres d’interface réseau sous les paramètres de la machine virtuelle répliquée.

## <a name="select-the-target-network"></a>Sélectionner le réseau cible

Pour les machines VMware et physiques, ainsi que pour les machines virtuelles Hyper-V (sans System Center Virtual Machine Manager), vous pouvez spécifier le réseau virtuel cible des machines virtuelles individuelles. Pour les machines virtuelles Hyper-V managées par Virtual Machine Manager, utilisez le [mappage réseau](site-recovery-network-mapping.md) pour mapper les réseaux de machines virtuelles sur un serveur Virtual Machine Manager source et des réseaux Azure cible.

1. Sous **Éléments répliqués** dans un coffre Recovery Services, sélectionnez n’importe quel élément répliqué pour accéder à ses paramètres.

2. Sélectionnez l’onglet **Calcul et réseau** pour accéder aux paramètres réseau de l’élément répliqué.

3. Sous **Propriétés réseau**, choisissez un réseau virtuel dans la liste des interfaces réseau disponibles.

    ![Paramètres réseau](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

La modification du réseau cible a une incidence sur toutes les interfaces réseau de cette machine virtuelle spécifique.

Pour les clouds Virtual Machine Manager, la modification du mappage réseau influe sur toutes les machines virtuelles et leurs interfaces réseau.

## <a name="select-the-target-interface-type"></a>Sélectionner le type d’interface cible

Sous la section **Interfaces réseau** du volet **Calcul et réseau**, vous pouvez afficher et modifier les paramètres d’interface réseau. Vous pouvez également spécifier le type d’interface réseau cible.

- Une interface réseau **principale** est requise pour le basculement.
- Toutes les autres interfaces réseau sélectionnées, le cas échéant, sont des interfaces réseau **secondaires**.
- Sélectionnez **Ne pas utiliser** pour exclure une interface réseau du processus de création lors du basculement.

Par défaut, lorsque vous activez la réplication, Site Recovery sélectionne toutes les interfaces réseau détectées sur le serveur local. Il en marque une comme **principale** et toutes les autres comme **secondaires**. Toutes les interfaces ajoutées ultérieurement sur le serveur local sont indiquées avec la mention **Ne pas utiliser** par défaut. Lorsque vous ajoutez d’autres interfaces réseau, assurez-vous que la taille de cible de machine virtuelle Azure correcte est sélectionnée pour prendre en charge toutes les interfaces réseau requises.

## <a name="modify-network-interface-settings"></a>Modifier les paramètres d’interface réseau

Vous pouvez modifier le sous-réseau et l’adresse IP des interfaces de réseau d’un élément répliqué. Si une adresse IP n’est pas spécifiée, Site Recovery affecte l’adresse IP disponible suivante du sous-réseau à l’interface réseau lors du basculement.

1. Sélectionnez n’importe quelle interface réseau disponible pour ouvrir les paramètres d’interface réseau.

2. Choisissez un sous-réseau dans la liste des sous-réseaux disponibles.

3. Entrez l’adresse IP de votre choix (comme requis).

    ![Paramètres d’interface réseau](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Sélectionnez **OK** pour terminer la modification et revenir au volet **Calcul et réseau**.

5. Répétez les étapes 1 à 4 pour les autres interfaces réseau.

6. Sélectionnez **Enregistrer** pour enregistrer toutes les modifications.

## <a name="next-steps"></a>Étapes suivantes
  [En savoir plus](../virtual-network/virtual-network-network-interface-vm.md) sur les interfaces réseau des machines virtuelles Azure.
