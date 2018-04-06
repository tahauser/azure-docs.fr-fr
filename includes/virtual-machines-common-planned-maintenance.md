---
title: Fichier Include
description: Fichier Include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: e484dac645ff2e5867d2e652c389a9950e8bac12
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
Azure exécute régulièrement des mises à jour afin d’améliorer la fiabilité, les performances et la sécurité de l’infrastructure hôte des machines virtuelles. Ces mises à jour vont de la mise à jour corrective de composants logiciels dans l’environnement d’hébergement (système d’exploitation, hyperviseur et différents agents déployés sur l’hôte) en passant par la mise à niveau des composants réseau, jusqu’à la désaffectation de matériel. La majorité de ces mises à jour ont lieu sans affecter les machines virtuelles hébergées. Cependant, il existe des cas où les mises à jour ont un impact :

- Si une mise à jour sans redémarrage est possible, Azure utilise la maintenance avec préservation de la mémoire pour mettre en pause la machine virtuelle tandis que l’hôte est mis à jour ou que la machine virtuelle est déplacée vers un hôte déjà mis à jour.

- Si la maintenance nécessite un redémarrage, une notification vous dira pour quand est prévue la maintenance. Dans ces cas, vous disposez aussi d’une période pour commencer la maintenance vous-même, au moment qui vous convient.

Cette page décrit comment Microsoft Azure exécute les deux types de maintenance. Pour en savoir plus sur les événements non planifiés (pannes), consultez Gestion de la disponibilité des machines virtuelles pour [Windows] (../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).

Les applications qui s’exécutent sur une machine virtuelle peuvent recueillir des informations sur les mises à jour à venir à l’aide du Service de métadonnées Azure pour [Windows](../articles/virtual-machines/windows/instance-metadata-service.md) ou [Linux] (../articles/virtual-machines/linux/instance-metadata-service.md).

Pour obtenir des guides pratiques sur la gestion de la maintenance planifiée, consultez « Gestion des notifications de maintenance planifiée » pour [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="memory-preserving-maintenance"></a>Maintenance avec préservation de la mémoire

Lorsque les mises à jour ne nécessitent pas un redémarrage complet, des mécanismes de maintenance avec préservation de la mémoire sont utilisés pour limiter l’impact sur la machine virtuelle. La machine virtuelle est mise en pause pendant 30 secondes maximum, préservant la mémoire RAM, tandis que l’environnement d’hébergement applique les correctifs et mises à jour nécessaires ou déplace la machine virtuelle vers un hôte déjà mis à jour. La machine virtuelle est redémarrée et l’horloge de la machine virtuelle est automatiquement synchronisée. 

Pour les machines virtuelles dans des groupes à haute disponibilité, les domaines de mise à jour sont mis à jour un par un. Toutes les machines virtuelles d’un domaine de mise à jour (UD) sont mises en pause, mises à jour et redémarrées avant que la maintenance planifiée ne passe au domaine de mise à jour suivant.

Certaines applications peuvent être affectées par ces types de mises à jour. Par exemple, les applications qui effectuent des scénarios de traitement d’événements en temps réel, comme le streaming multimédia, le transcodage multimédia ou la mise en réseau à débit élevé, ne peuvent pas être conçues pour tolérer une pause de trente secondes. <!-- sooooo, what should they do? --> Si la machine virtuelle est déplacée vers un hôte différent, certaines charges de travail sensibles peuvent subir une légère dégradation des performances dans les quelques minutes qui précèdent la mise en pause de la machine virtuelle. 


## <a name="maintenance-requiring-a-reboot"></a>Maintenance nécessitant un redémarrage

Lorsque des machines virtuelles doivent être redémarrées en vue d’une maintenance planifiée, vous en êtes averti. Une maintenance planifiée comprend deux phases : la fenêtre de libre-service et une fenêtre de maintenance planifiée.

La **fenêtre de libre-service** vous permet de déclencher la maintenance sur vos machines virtuelles. Pendant ce temps, vous pouvez interroger chaque machine virtuelle pour afficher leur état et vérifier le résultat de votre dernière requête de maintenance.

Lorsque vous démarrez une maintenance en libre-service, votre machine virtuelle est déplacée vers un nœud qui a déjà été mis à jour pour le remettre en service. À cause du redémarrage de la machine virtuelle, le disque temporaire est perdu et les adresses IP dynamiques associées à l’interface réseau virtuelle sont mises à jour.

Si vous démarrez une maintenance en libre-service et rencontrez une erreur au cours du processus, l’opération s’interrompt, la machine virtuelle n’est pas mise à jour et est retirée de l’itération de la maintenance planifiée. Un nouveau planning vous sera fourni ultérieurement et une nouvelle occasion de procéder à une maintenance en libre-service. 

C’est après la fenêtre de libre-service qu’apparaît la **fenêtre de maintenance planifiée**. Pendant cet intervalle de temps, vous pouvez toujours interroger la fenêtre de maintenance, mais vous ne pouvez plus démarrer la maintenance.

Pour obtenir des informations sur la gestion de la maintenance nécessitant un redémarrage, consultez « Gestion des notifications de maintenance planifiée » pour [Linux](../articles/virtual-machines/linux/maintenance-notifications.md) ou [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

## <a name="availability-considerations-during-planned-maintenance"></a>Considérations relatives à la disponibilité lors de la maintenance planifiée 

Si vous décidez d’attendre jusqu’à la fenêtre de maintenance planifiée, plusieurs éléments sont à prendre en compte si vous voulez maintenir une haute disponibilité de vos machines virtuelles. 

### <a name="paired-regions"></a>Régions jumelées

Chaque région Azure est associée à une autre région au sein de la même région géographique; elles constituent ainsi des paires régionales. Pendant les maintenances planifiées, Azure ne met à jour que les machines virtuelles d’une seule région d’une paire régionale. Par exemple, lors de la mise à jour des ordinateurs virtuels de l’Amérique du Nord central, Azure ne met à jour dans le même temps aucune machine virtuelle de l’Amérique du Nord méridionale. En revanche, les autres régions, l’Europe du Nord par exemple, peuvent faire l’objet d’une maintenance en même temps que la région Est des États-Unis. Comprendre le fonctionnement des paires régionales peut vous aider à mieux répartir vos machines virtuelles entre les régions. Pour en savoir plus, consultez [Paires régionales Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

### <a name="availability-sets-and-scale-sets"></a>Groupes à haute disponibilité et groupes identiques

Lorsque vous déployez une charge de travail sur des machines virtuelles Azure, vous pouvez créer des machines virtuelles dans un groupe à haute disponibilité afin de fournir une haute disponibilité pour votre application. Ceci vous garantit qu’au moins une des machines virtuelles sera disponible pendant une panne ou un événement de maintenance.

Au sein d’un groupe à haute disponibilité, les machines virtuelles individuelles sont réparties sur un maximum de 20 domaines de mise à jour (UD). Lors de la maintenance planifiée, un seul domaine de mise à jour est affecté à un moment donné. Avertissement : L’ordre des domaines de mise à jour impactés n’est pas forcément séquentiel. 

Les groupes de machines virtuelles identiques sont des ressources Azure Compute que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques en tant que ressource unique. Le groupe identique est automatiquement déployé dans les domaines de mise à jour, comme les machines virtuelles dans un groupe à haute disponibilité. Comme pour les groupes à haute disponibilité, un seul domaine de mise à jour n’est impacté à la fois dans des groupes identiques.

Pour plus d’informations sur la configuration des machines virtuelles pour une haute disponibilité, consultez Gérer la disponibilité de vos machines virtuelles pour [Windows](../articles/virtual-machines/windows/manage-availability.md) ou [Linux](../articles/virtual-machines/linux/manage-availability.md).
