---
title: "Calculs d’évaluations dans Azure Migrate | Microsoft Docs"
description: "Offre une vue d’ensemble des calculs d’évaluations dans le service Azure Migrate."
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 39a63769-31eb-49f9-9089-4d3e4e88a412
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: f00825ff9a5018e67672ce452f01130f84919e52
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2017
---
# <a name="assessment-calculations"></a>Calculs d’évaluation

[Azure Migrate](migrate-overview.md) évalue les charges de travail locales du point de vue de la migration vers Azure. Cet article donne des informations sur le calcul des évaluations.



## <a name="overview"></a>Vue d'ensemble

Une évaluation Azure Migrate comporte trois étapes. Elle commence par une analyse de l’adéquation, suivie d’estimations de dimensionnement en fonction des performances, et enfin, d’une estimation de coût mensuel. Une machine ne passe au stade suivant que si elle réussit le précédent. Par exemple, si elle rate la vérification d’adéquation Azure, elle est marquée comme inadaptée à Azure, et le dimensionnement et l’évaluation des coûts ne seront pas calculés. 


## <a name="azure-suitability-analysis"></a>Analyse d’adéquation Azure

Les machines à migrer vers Azure doivent respecter les limitations et les exigences d’Azure. Par exemple, si un disque de machine virtuelle locale a une capacité supérieure à 4 To, il ne peut pas être hébergé sur Azure. Les vérifications de conformité Azure sont résumées dans le tableau suivant. 

**Vérification** | **Détails**
--- | ---
**Type de démarrage** | Le type de démarrage du disque du système d’exploitation invité doit être BIOS et non UEFI.
**Cœurs** | Le nombre de cœurs des machines doit être (inférieur ou) égal au nombre maximal de cœurs (32) pris en charge pour une machine virtuelle Azure.<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération les cœurs utilisés pour la comparaison. Si un facteur de confort est spécifié dans les paramètres de l’évaluation, le nombre de cœurs utilisés est multiplié par le facteur de confort.<br/><br/> En l’absence d’historique des performances, Azure Migrate utilise les cœurs alloués, sans appliquer le facteur de confort.
**Mémoire** | La taille de la mémoire de la machine doit être (inférieure ou) égale à la mémoire maximale (448 Go) autorisée pour une machine virtuelle Azure. <br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération la mémoire utilisée pour la comparaison. Si un facteur de confort est spécifié, la mémoire utilisée est multipliée par le facteur de confort.<br/><br/> En l’absence d’historique, la mémoire allouée est utilisée, sans appliquer le facteur de confort.<br/><br/> 
**Windows Server 2003-2008 R2** | 32 bits et 64 bits pris en charge.<br/><br/> Azure assure seulement un support au mieux.
**Windows Server 2008 R2 avec tous les Service Packs** | 64 bits pris en charge.<br/><br/> Azure assure un support complet.
**Windows Server 2012 avec tous les Service Packs** | 64 bits pris en charge.<br/><br/> Azure assure un support complet.
**Windows Server 2012 R2 avec tous les Service Packs** | 64 bits pris en charge.<br/><br/> Azure assure un support complet.
**Windows Server 2016 avec tous les Service Packs** | 64 bits pris en charge.<br/><br/> Azure assure un support complet.
**Clients Windows 7 et versions ultérieures** | 64 bits pris en charge.<br/><br/> Azure assure un support avec abonnement Visual Studio uniquement.
**Linux** | 64 bits pris en charge.<br/><br/> Azure assure un support complet de ces [systèmes d’exploitation](../virtual-machines/linux/endorsed-distros.md).
**Disque de stockage** | La taille allouée d’un disque doit être inférieure ou égale à 4 To (4 096 Go).<br/><br/> Le nombre de disques attachés à la machine doit être inférieur ou égal à 65, disque du système d’exploitation compris. 
**Mise en réseau** | Au maximum 32 cartes réseau doivent être attachées à une machine.


## <a name="performance-based-sizing"></a>Dimensionnement en fonction des performances

Une fois que la machine est marquée comme adaptée à Azure, Azure Migrate le mappe à une taille de machine virtuelle dans Azure, selon les critères suivants :

- **Vérification de stockage** : Azure Migrate s’efforce de mapper chaque disque attaché à la machine avec un disque dans Azure : - Azure Migrate multiplie les E/S par seconde (IOPS) par le facteur de confort. Il multiplie également le débit (en Mbit/s) de chaque disque par le facteur de confort. Il obtient ainsi les IOPS et le débit effectifs du disque. Sur cette base, Azure Migrate mappe le disque avec un disque Standard ou Premium dans Azure.
    - Si le service ne parvient pas à trouver un disque avec les IOPS et le débit requis, il marque la machine comme inadaptée à Azure.
    - S’il trouve un ensemble de disques adaptés, Azure Migrate sélectionne ceux qui prennent en charge la méthode de redondance du stockage et l’emplacement spécifié dans les paramètres d’évaluation.
    - Si plusieurs disques sont éligibles, il sélectionne celui dont le coût est le plus bas.
- **Débit du disque de stockage** : cliquez [ici](../azure-subscription-service-limits.md#storage-limits) pour en savoir plus sur les limites d’Azure par disque et par machine virtuelle.
- **Type de disque** : Azure Migrate prend uniquement en charge les disques managés.
- **Vérification du réseau** : Azure Migrate s’efforce de trouver une machine virtuelle Azure capable de prendre en charge le nombre de cartes réseau présentes sur la machine locale.
    - Pour cela, il aggrège les données transmises par seconde (Mbit/s) en sortie de la machine (sortie de réseau), toutes cartes réseau confondues, et applique le facteur de confort au nombre global. Ce nombre est utilisé pour trouver une machine virtuelle Azure qui prend en charge les performances réseau nécessaires.
    - Azure Migrate prend les paramètres réseau de la machine virtuelle et suppose qu’il s’agit d’un réseau à l’extérieur du centre de données.
    - Si aucune donnée de performances réseau n’est disponible, seul le nombre de cartes réseau est pris en considération pour le dimensionnement de la machine virtuelle.
- **Vérification du calcul** : une fois les exigences calculées pour le réseau et le stockage, Azure Migrate prend en considération les exigences de calcul :
    - Si des données de performances sont disponibles pour la machine virtuelle, il examine les cœurs et la mémoire utilisés et applique le facteur de confort. En fonction de ce nombre, il s’efforce de trouver une taille de machine virtuelle adaptée dans Azure.
    - Si aucune taille adaptée n’est trouvée, la machine est marquée comme inadaptée à Azure.
    - Si une taille adaptée est trouvée, Azure Migrate applique les calculs de mise en réseau et de stockage. Il applique ensuite les paramètres d’emplacement et de niveau tarifaire de façon à obtenir la recommandation de taille de machine virtuelle finale.


## <a name="monthly-cost-estimation"></a>Estimation des coûts mensuels

Une fois les recommandations de dimensionnement terminées, Azure Migrate calcule les coûts de calcul et de stockage après la migration.

- **Calcul du coût** : à partir de la taille de machine virtuelle Azure recommandée, Azure Migrate utilise l’API de facturation pour calculer le coût mensuel de la machine virtuelle. Le calcul prend en compte les paramètres de système d’exploitation, de Software Assurance, d’emplacement et de devise. Il agrège le coût de toutes les machines pour calculer le coût de calcul mensuel total.
- **Coût de stockage** : le coût de stockage mensuel d’une machine est calculé en additionnant le coût mensuel de tous les disques qui lui sont attachés. Azure Migrate calcule les coûts totaux de stockage mensuel en additionnant le coût de stockage de toutes les machines. Actuellement, le calcul ne prend pas en compte les offres spécifiées dans les paramètres d’évaluation.

Les coûts sont affichés dans la devise spécifiée dans les paramètres d’évaluation. 


## <a name="next-steps"></a>Étapes suivantes

[Configurer une évaluation pour des machines virtuelles VMware locales](tutorial-assessment-vmware.md)
