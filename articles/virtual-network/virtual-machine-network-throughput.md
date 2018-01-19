---
title: "Débit réseau des machines virtuelles Azure | Microsoft Docs"
description: "Découvrez plus d’informations sur le débit réseau des machines virtuelles Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: f22b6f361f0c5bea547721309bb0f75b62f18d92
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/12/2018
---
# <a name="virtual-machine-network-bandwidth"></a>Bande passante réseau des machines virtuelles

Azure offre une variété de tailles et de types de machines virtuelles, qui présentent une combinaison différente de capacités de performances. L’une de ces capacités est la bande passante réseau (ou débit réseau), mesurée en mégabits par seconde (Mbits/s). Étant donné que les machines virtuelles sont hébergées sur du matériel partagé, la capacité du réseau doit être répartie équitablement entre les machines virtuelles partageant le même matériel. Les machines virtuelles plus volumineuses bénéficient d’une plus grande bande passante que les machines plus petites.
 
La bande passante réseau allouée à chaque machine virtuelle est mesurée en fonction du trafic sortant de la machine virtuelle. L’ensemble du trafic réseau quittant la machine virtuelle est mesuré en fonction de la limite d’allocation, quelle que soit la destination. Par exemple, si une machine virtuelle se voit allouer une limite de 1 000 Mbits/s, cette limite s’applique de toute façon, que le trafic sortant soit destiné à une machine virtuelle du réseau virtuel ou à une machine en dehors d’Azure.
 
Le trafic en entrée n’est pas compté ou limité directement. Toutefois, il existe d’autres facteurs, tels que les limites en termes de processeur et de stockage, susceptibles d’affecter la capacité d’une machine virtuelle à traiter les données entrantes.

La mise en réseau accélérée est une fonctionnalité conçue pour améliorer les performances du réseau, notamment la latence, le débit et le taux d’utilisation des processeurs. La mise en réseau accélérée peut améliorer le débit de la machine virtuelle, mais en deçà de la limite de bande passante allouée à la machine virtuelle. Pour en savoir plus sur la mise en réseau accélérée, consultez « Mise en réseau accélérée pour les machines virtuelles [Windows](create-vm-accelerated-networking-powershell.md) ou [Linux](create-vm-accelerated-networking-cli.md) ».
 
Les machines virtuelles Azure doivent être associées à une interface réseau minimum. La bande passante allouée à une machine virtuelle représente l’intégralité du trafic sortant sur l’ensemble des interfaces réseau associées à une machine virtuelle. En d’autres termes, la bande passante est allouée machine virtuelle par machine virtuelle, quel que soit le nombre d’interfaces réseau associées à cette machine. Pour savoir combien d’interfaces réseau les machines virtuelles Azure de différentes tailles peuvent prendre en charge, consultez les informations sur les tailles de machines virtuelles [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="expected-network-throughput"></a>Débit réseau attendu

Le débit sortant attendu et le nombre d’interfaces réseau prises en charge par chaque taille de machine virtuelle sont détaillés dans les spécifications des tailles de machine virtuelle Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Sélectionnez un type, comme le type d’usage général, puis choisissez une série de tailles dans la page qui en résulte, par exemple « Dv2-series ». Chaque série comporte une table, dont la dernière colonne, intitulée **Nombre max de cartes réseau/Performance réseau attendue (Mbits/s)**, détaille les spécifications de mise en réseau. 

Cette limite de débit s’applique à la machine virtuelle. Le débit n’est pas affecté par les facteurs suivants :
- **Nombre d’interfaces réseau** : la limite de bande passante inclut l’ensemble du trafic sortant en provenance de la machine virtuelle.
- **Mise en réseau accélérée** : bien que cette fonctionnalité puisse être utile pour atteindre la limite publiée, elle ne modifie pas cette limite.
- **Destination du trafic** : toutes les destinations s’inscrivent dans la limite de sortie.
- **Protocole** : l’intégralité du trafic sortant, sur l’ensemble des protocoles, s’inscrit dans la limite.

## <a name="next-steps"></a>Étapes suivantes

- [Optimiser le débit réseau d’un système d’exploitation de machine virtuelle](virtual-network-optimize-network-bandwidth.md)
- [Tester le débit réseau](virtual-network-bandwidth-testing.md) d’une machine virtuelle
