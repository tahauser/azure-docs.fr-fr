---
title: "Migrer des machines virtuelles Azure vers des disques gérés | Microsoft Docs"
description: "Migrez des machines virtuelles créées à l’aide de disques non gérés dans des comptes de stockage afin d’utiliser des disques gérés."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: 3471792bba92879e446837434cbdba667cf679ad
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2018
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrer des machines virtuelles Azure vers des disques gérés dans Azure

Les disques gérés du service Azure Managed Disks simplifient la gestion de votre stockage en éliminant la nécessité de gérer séparément les comptes de stockage.  Vous pouvez également migrer vos machines virtuelles Azure existantes vers des disques gérés afin de tirer parti de la fiabilité accrue des machines virtuelles dans un groupe à haute disponibilité. Cela permet de s’assurer que les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont suffisamment isolés les uns des autres pour éviter les points de défaillance uniques. Les disques des différentes machines virtuelles d’un groupe à haute disponibilité sont automatiquement placés dans des unités d’échelle (tampons) de stockage distinctes, ce qui limite l’impact des défaillances d’unités d’échelle de stockage uniques dues à des défaillances matérielles et logicielles.
Selon vos besoins, vous avez le choix entre deux types d’options de stockage :

- Les [disques managés Premium](premium-storage.md) sont des supports basés sur des disques SSD (Solide State Drive) qui assurent de hautes performances et une faible latence pour les machines virtuelles dont les charges de travail nécessitent de nombreuses E/S. Vous pouvez tirer parti de la vitesse et des performances des disques gérés Premium en migrant vers ces disques.

- Les [disques gérés Standard](standard-storage.md) utilisent un support de stockage basé sur un lecteur de disque dur (HDD) et sont mieux adaptés aux charges de travail de développement/test et d’accès peu fréquent, qui sont moins sensibles à la variabilité des performances.

Vous pouvez migrer vers des disques gérés dans les cas de figure suivants :

| Migrer...                                            | Lien vers la documentation                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Convertir des machines virtuelles autonomes et des machines virtuelles dans un groupe à haute disponibilité en disques gérés   | [Convertir des machines virtuelles pour utiliser des disques gérés](convert-unmanaged-to-managed-disks.md) |
| Une machine virtuelle unique du modèle Classic vers le modèle Resource Manager sur des disques gérés     | [Migrer une machine virtuelle unique](migrate-single-classic-to-resource-manager.md)  | 
| Toutes les machines virtuelles d’un réseau virtuel du modèle Classic vers le modèle Resource Manager sur des disques gérés     | [Migration de ressources IaaS d’un environnement Classic vers Resource Manager](migration-classic-resource-manager-ps.md), puis [Convertir une machine virtuelle à partir de disques non gérés vers des disques gérés](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Planification de la conversion en disques gérés

Cette section vous aide à prendre la meilleure décision concernant les types de machines virtuelles et de disques.


## <a name="location"></a>Lieu

Choisissez un emplacement où Azure Managed Disks est disponible. Si vous effectuez une migration vers des disques gérés Premium, assurez-vous également que le stockage Premium est disponible dans la région où vous prévoyez la migration. Pour obtenir des informations à jour sur les emplacements disponibles, consultez [Services Azure par région](https://azure.microsoft.com/regions/#services) .

## <a name="vm-sizes"></a>Tailles de machine virtuelle

Si vous effectuez une migration vers des disques gérés Premium, vous devez mettre à jour la taille de la machine virtuelle par rapport à la capacité de stockage Premium disponible dans la région où se trouve la machine virtuelle. Passez en revue les tailles de machine virtuelle compatibles avec le stockage Premium. Les spécifications des tailles des machines virtuelles Azure sont répertoriées dans la section [Tailles des machines virtuelles](sizes.md).
Passez en revue les caractéristiques de performances des machines virtuelles fonctionnant avec Premium Storage et choisissez la taille de machine virtuelle la mieux adaptée à votre charge de travail. Assurez-vous que la bande passante disponible est suffisante sur votre machine virtuelle pour gérer le trafic du disque.

## <a name="disk-sizes"></a>Tailles du disque

**Disques gérés Premium**

Il existe sept types de disques gérés Premium qui peuvent être utilisés avec votre machine virtuelle, chacun d’eux présentant des limites d’E/S par seconde et de débit spécifiques. Prenez en compte ces limites lors de la sélection du type de disque Premium pour votre machine virtuelle en fonction des besoins en capacité, en performances, en extensibilité et en charges maximales de votre application.

| Type de disque Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Taille du disque           | 128 Go| 512 Go| 128 Go| 256 Go|512 Go | 1024 Go (1 To)    | 2 048 Go (2 To)    | 4 095 Go (4 To)    | 
| IOPS par disque       | 120   | 240   | 500   | 1100  |2 300              | 5 000              | 7500              | 7500              | 
| Débit par disque | 25 Mo par seconde  | 50 Mo par seconde  | 100 Mo par seconde | 125 Mo par seconde |150 Mo par seconde | 200 Mo par seconde | 250 Mo par seconde | 250 Mo par seconde |

**Disques gérés Standard**

Il existe sept types de disques gérés Standard qui peuvent être utilisés avec votre machine virtuelle. Chacun d’eux dispose d’une capacité différente, mais ils partagent les mêmes limites d’E/S par seconde et de débit. Choisissez le type de disque géré Standard selon les besoins en capacité de votre application.

| Type de disque Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Taille du disque           | 30 Go            | 64 Go            | 128 Go           | 256 Go           |512 Go           | 1024 Go (1 To)   | 2 048 Go (2 To)    | 4 095 Go (4 To)   | 
| IOPS par disque       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Débit par disque | 60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde |60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde | 60 Mo par seconde | 

## <a name="disk-caching-policy"></a>Stratégie de mise en cache du disque

**Disques gérés Premium**

Par défaut, la stratégie de mise en cache est *Lecture seule* pour tous les disques de données Premium et *Lecture-écriture* pour le disque du système d’exploitation Premium attaché à la machine virtuelle. Ce paramètre de configuration est recommandé pour optimiser les performances des E/S de votre application. Pour les disques de données en écriture seule ou avec d'importantes opérations d'écriture (par ex., les fichiers journaux de SQL Server), désactivez la mise en cache du disque pour de meilleures performances de l'application.

## <a name="pricing"></a>Tarifs

Consultez la [tarification des disques gérés](https://azure.microsoft.com/pricing/details/managed-disks/). La tarification des disques gérés Premium est identique à celle des disques non gérés Premium. En revanche, la tarification des disques gérés Standard est différente de celle des disques non gérés Standard.



## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [disques gérés](managed-disks-overview.md)
