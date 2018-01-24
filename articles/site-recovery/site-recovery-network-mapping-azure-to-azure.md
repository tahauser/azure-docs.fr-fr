---
title: "Mappage réseau entre deux régions Azure dans Azure Site Recovery | Microsoft Docs"
description: "Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques. Informez-vous sur le basculement dans Microsoft Azure ou un centre de données secondaire."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: bf3d557c77e3cb6ade6f1bb3773c807f9c8b43f6
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>Mappage réseau entre deux régions Azure


Cet article explique comment mapper des réseaux virtuels Azure de deux régions Azure. Le mappage réseau garantit que, quand une machine virtuelle répliquée est créée dans la région Azure cible, elle est créée sur le réseau virtuel qui est mappé au réseau virtuel de la machine virtuelle source.  

## <a name="prerequisites"></a>configuration requise
Avant de mapper des réseaux, vérifiez que vous avez créé des [réseaux virtuels Azure](../virtual-network/virtual-networks-overview.md) dans les régions Azure source et cible.

## <a name="map-networks"></a>Mapper des réseaux

Pour mapper un réseau virtuel Azure dans une région Azure à un autre réseau virtuel dans une autre région, accédez à Infrastructure Site Recovery -> Mappage réseau (pour les machines virtuelles Azure) et créez un mappage réseau.

![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


Dans l’exemple ci-dessous, ma machine virtuelle s’exécute dans la région Asie de l’Est et est répliquée vers la région Asie du Sud-Est.

Sélectionnez les réseaux source et cible, puis cliquez sur OK pour créer un mappage réseau d’Asie de l’Est à Asie du Sud-Est.

![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Suivez la même procédure pour créer un mappage réseau d’Asie du Sud-Est à Asie de l’Est.

![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>Mappage réseau lors de l’activation de la réplication

Si le mappage réseau n’a pas été effectué lors de la première réplication d’une machine virtuelle d’une région Azure vers une autre, vous pourrez choisir le réseau cible dans le même processus. Site Recovery crée des mappages réseau de la région source à la région cible et de la région cible à la région source, en fonction de cette sélection.   

![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Par défaut, Site Recovery crée dans la région cible un réseau qui est identique au réseau source, en ajoutant « -asr » comme suffixe au nom du réseau source. Vous pouvez choisir un réseau déjà créé en cliquant sur Personnaliser.

![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


Si le mappage réseau est déjà fait, vous ne pouvez pas changer le réseau virtuel cible lors de l’activation de la réplication. Pour le changer, modifiez le mappage réseau existant.  

![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Si vous modifiez un mappage réseau de la région 1 à la région 2, n’oubliez pas de modifier aussi le mappage réseau de la région 2 à la région 1.
>
>


## <a name="subnet-selection"></a>Sélection de sous-réseau
Le sous-réseau de la machine virtuelle cible est sélectionné en fonction du nom du sous-réseau de la machine virtuelle source. S’il existe sur le réseau cible un sous-réseau portant le même nom que la machine virtuelle source, il sera choisi pour la machine virtuelle cible. S’il n’y a aucun sous-réseau du même nom sur le réseau cible, le premier sous-réseau dans l’ordre alphabétique sera choisi comme sous-réseau cible. Vous pouvez modifier ce sous-réseau en accédant aux paramètres Calcul et réseau de la machine virtuelle.

![Modifier le sous-réseau](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Adresse IP

L’adresse IP de chacune des interfaces réseau de la machine virtuelle cible est choisie de la façon suivante :

### <a name="dhcp"></a>DHCP
Si l’interface réseau de la machine virtuelle source utilise le protocole DHCP, l’interface réseau de la machine virtuelle cible est également définie avec DHCP.

### <a name="static-ip"></a>Adresse IP statique
Si l’interface réseau de la machine virtuelle source utilise une adresse IP statique, l’interface réseau de la machine virtuelle cible est également configurée pour utiliser une adresse IP statique. L’adresse IP statique est choisie comme suit :

#### <a name="same-address-space"></a>Même espace d’adressage

Si les sous-réseaux source et cible ont le même espace d’adressage, l’adresse IP de l’interface réseau de la machine virtuelle source est définie comme adresse IP cible. S’il n’existe pas d’adresse IP identique, l’adresse IP suivante est définie comme adresse IP cible.

#### <a name="different-address-space"></a>Espace d’adressage différent

Si les sous-réseaux source et cible ont un espace d’adressage différent, l’adresse IP suivante disponible sur le sous-réseau cible est définie comme adresse IP cible.

Vous pouvez modifier l’adresse IP cible sur chacune des interfaces réseau en accédant aux paramètres Calcul et Réseau de la machine virtuelle.

## <a name="next-steps"></a>étapes suivantes

En savoir plus sur la [mise en réseau pour la réplication des machines virtuelles Azure](site-recovery-azure-to-azure-networking-guidance.md).
