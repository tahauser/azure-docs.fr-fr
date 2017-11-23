---
title: "Mappage réseau entre deux régions Azure dans Azure Site Recovery | Microsoft Docs"
description: "Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques. Informez-vous sur le basculement dans Microsoft Azure ou un centre de données secondaire."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: pratshar
ms.openlocfilehash: d73acd17d69564134026672b81c5dc32e15d8cd7
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>Mappage réseau entre deux régions Azure


Cet article explique comment mapper des réseaux virtuels Azure de deux régions Azure. Le mappage réseau garantit que quand une machine virtuelle répliquée est créée dans la région Azure cible, elle est créée sur le réseau virtuel qui est mappé au réseau virtuel de la machine virtuelle source.  

## <a name="prerequisites"></a>Prérequis
Avant de mapper des réseaux, vérifiez que vous avez créé des [réseaux virtuels Azure](../virtual-network/virtual-networks-overview.md) dans les régions Azure source et cible.

## <a name="map-networks"></a>Mapper des réseaux

Pour mapper un réseau virtuel Azure dans une région Azure à un autre réseau virtuel dans une autre région, accédez à Infrastructure Site Recovery -> Mappage réseau (pour les machines virtuelles Azure) et créez un mappage réseau.

![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


Dans l’exemple ci-dessous, ma machine virtuelle s’exécute dans la région Asie de l’Est et est répliquée vers Asie du Sud-Est.

Sélectionnez les réseaux source et cible, puis cliquez sur OK pour créer un mappage réseau Asie de l’Est à Asie du Sud-Est.

![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Faites la même chose pour créer un mappage réseau Asie du Sud-Est à Asie de l’Est.  
![Mappage réseau](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>Mappage réseau lors de l’activation de la réplication

Si le mappage réseau n’est pas effectué quand vous répliquez une machine virtuelle pour la première fois d’une région Azure vers une autre, vous pouvez choisir le réseau cible dans le cadre du même processus. Site Recovery crée des mappages réseau de la région source à la région cible et de la région cible à la région source, en fonction de cette sélection.   

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
Le sous-réseau de la machine virtuelle cible est sélectionné en fonction du nom du sous-réseau de la machine virtuelle source. Si un sous-réseau portant le même nom que celui de la machine virtuelle source est disponible sur le réseau cible, il est choisi pour la machine virtuelle cible. S’il n’y a aucun sous-réseau du même nom sur le réseau cible, le premier sous-réseau dans l’ordre alphabétique est choisi comme sous-réseau cible. Vous pouvez modifier ce sous-réseau en accédant aux paramètres Calcul et réseau de la machine virtuelle.

![Modifier le sous-réseau](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>Adresse IP

L’adresse IP pour chaque interface réseau de la machine virtuelle cible est choisie comme suit :

### <a name="dhcp"></a>DHCP
Si l’interface réseau de la machine virtuelle source utilise le protocole DHCP, l’interface réseau de la machine virtuelle cible est également définie avec DHCP.

### <a name="static-ip"></a>Adresse IP statique
Si l’interface réseau de la machine virtuelle source utilise une adresse IP statique, l’interface réseau de la machine virtuelle cible est également configurée pour utiliser une adresse IP statique. L’adresse IP statique est choisie comme suit :

#### <a name="same-address-space"></a>Même espace d’adressage

Si les sous-réseaux source et cible ont le même espace d’adressage, l’adresse IP cible est configurée comme identique à l’adresse IP de l’interface réseau de la machine virtuelle source. Si la même adresse IP n’est pas disponible, une autre adresse IP disponible est définie comme adresse IP cible.

#### <a name="different-address-space"></a>Espace d’adressage différent

Si les sous-réseaux source et cible ont un espace d’adressage différent, l’adresse IP cible est configurée avec n’importe quelle adresse IP disponible sur le sous-réseau cible.

Vous pouvez modifier l’adresse IP cible sur chaque interface réseau en accédant aux paramètres Calcul et réseau de la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [mise en réseau pour la réplication des machines virtuelles Azure](site-recovery-azure-to-azure-networking-guidance.md).
