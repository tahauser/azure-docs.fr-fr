---
title: "Calculs d’évaluations dans Azure Migrate | Microsoft Docs"
description: "Offre une vue d’ensemble des calculs d’évaluation dans le service Azure Migrate."
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: b264e2ceac4e76faa37d21972b94cfe323aa3ce5
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="assessment-calculations"></a>Calculs d’évaluation

[Azure Migrate](migrate-overview.md) évalue les charges de travail sur site pour la migration vers Azure. Cet article donne des informations sur le calcul des évaluations.


## <a name="overview"></a>Vue d'ensemble

Une évaluation Azure Migrate comporte trois étapes. Elle commence par une analyse de l’adéquation, suivie du dimensionnement et pour finir, d’une estimation du coût mensuel. Une machine ne passe au stade suivant que si elle réussit le précédent. Par exemple, si elle rate la vérification d’adéquation Azure, elle est marquée comme inadaptée à Azure, et le dimensionnement et l’évaluation des coûts ne seront pas effectués. 


## <a name="azure-suitability-analysis"></a>Analyse d’adéquation Azure

Toutes les machines ne conviennent pas à une exécution dans le cloud, car celui-ci a ses propres limitations et configuration requise. Azure Migrate évalue l’adéquation de chaque machine locale pour la migration vers Azure, et classe les machines dans l’une des catégories suivantes :
- **Disponible pour Azure** : la machine peut être migrée telle quelle vers Azure sans aucune modification. Elle démarrera dans Azure avec une prise en charge complète d’Azure.
- **Préparé pour Azure sous condition** : la machine peut démarrer dans Azure, mais est susceptible de ne pas offrir une prise en charge complète d’Azure. Par exemple, une machine avec une version antérieure du système d’exploitation Windows Server n’est pas prise en charge dans Azure. Vous devez faire attention lors de la migration de ces machines vers Azure, et suivre les instructions de correction suggérées dans l’évaluation afin de résoudre les problèmes de disponibilité avant la migration.
- **Non disponible pour Azure** : la machine ne démarrera pas dans Azure. Par exemple, si une machine locale a un disque dont la taille est supérieure à 4 To, elle ne peut pas être hébergée sur Azure. Vous devez suivre les instructions de correction suggérées dans l’évaluation afin de résoudre les problèmes de disponibilité avant la migration vers Azure. Le dimensionnement adéquat et l’estimation du coût ne sont pas effectués pour les machines marquées comme n’étant pas disponibles pour Azure.
- **État de la préparation inconnu** : Azure Migrate n’a pas pu déterminer l’état de préparation de la machine car les données disponibles dans vCenter Server sont insuffisantes. 

Azure Migrate passe en revue les propriétés de la machine et le système d’exploitation invité pour identifier l’état de préparation Azure de la machine locale.

### <a name="machine-properties"></a>Propriétés de machine virtuelle
Azure Migrate passe en revue les propriétés suivantes de la machine virtuelle locale pour déterminer si une machine virtuelle peut s’exécuter sur Azure.
 
**Propriété** | **Détails** | **État de préparation pour Azure**
--- | --- | ---
**Type de démarrage** | Azure prend en charge les machines virtuelles avec le type de démarrage BIOS, et non UEFI. | Préparé pour Azure sous condition si le type de démarrage est UEFI. 
**Cœurs** | Le nombre de cœurs des machines doit être inférieur ou égal au nombre maximal de cœurs (32) pris en charge pour une machine virtuelle Azure.<br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération les cœurs utilisés pour la comparaison. Si un facteur de confort est spécifié dans les paramètres de l’évaluation, le nombre de cœurs utilisés est multiplié par le facteur de confort.<br/><br/> En l’absence d’historique des performances, Azure Migrate utilise les cœurs alloués, sans appliquer le facteur de confort. | Non disponible si le nombre de cœurs est supérieur à 32. 
**Mémoire** | La taille de la mémoire de la machine doit être inférieure ou égale à la mémoire maximale (448 Go) autorisée pour une machine virtuelle Azure. <br/><br/> Si l’historique des performances est disponible, Azure Migrate prend en considération la mémoire utilisée pour la comparaison. Si un facteur de confort est spécifié, la mémoire utilisée est multipliée par le facteur de confort.<br/><br/> En l’absence d’historique, la mémoire allouée est utilisée, sans appliquer le facteur de confort.<br/><br/> | Non disponible si la taille de la mémoire est supérieure à 448 Go.
**Disque de stockage** | La taille allouée d’un disque doit être inférieure ou égale à 4 To (4 096 Go).<br/><br/> Le nombre de disques attachés à la machine doit être inférieur ou égal à 65, disque du système d’exploitation compris. | Non disponible si un disque a une taille supérieure à 4 To ou si plus de 65 disques sont attachés à la machine. 
**Mise en réseau** | Au maximum 32 cartes réseau doivent être attachées à une machine. | Non disponible si la machine a plus de 32 cartes réseau.

### <a name="guest-operating-system"></a>Système d’exploitation invité 
Outre les propriétés de la machine virtuelle, Azure Migrate examine également le système d’exploitation invité de la machine virtuelle locale afin de déterminer si celle-ci peut s’exécuter sur Azure.

> [!NOTE]
> Azure Migrate considère le système d’exploitation spécifié dans vCenter Server pour effectuer l’analyse suivante. La détection effectuée par Azure Migrate étant basée sur l’appareil, il n’a aucun moyen de vérifier si le système d’exploitation en cours d’exécution sur la machine virtuelle est identique à celui spécifié dans vCenter Server.

La logique suivante est utilisée par Azure Migrate pour déterminer l’état de préparation Azure de la machine virtuelle en fonction du système d’exploitation.

**Système d’exploitation** | **Détails** | **État de préparation pour Azure**
--- | --- | ---
Windows Server 2016 avec tous les Service Packs | Azure assure un support complet. | Disponible pour Azure
Windows Server 2012 R2 avec tous les Service Packs | Azure assure un support complet. | Disponible pour Azure
Windows Server 2012 avec tous les Service Packs | Azure assure un support complet. | Disponible pour Azure
Windows Server 2008 R2 avec tous les Service Packs | Azure assure un support complet.| Disponible pour Azure
Windows Server 2003-2008 R2 | La date limite de prise en charge de ces systèmes d’exploitation est passée, et leur prise en charge dans Azure nécessite un [Custom Support Agreement (CSA)](https://aka.ms/WSosstatement). | Préparé pour Azure sous condition. Envisagez une mise à niveau du système d’exploitation avant de migrer vers Azure.
Windows 2000, 98, 95, NT, 3.1, MS-DOS | La date limite de prise en charge de ces systèmes d’exploitation est passée. La machine peut démarrer dans Azure, mais aucune prise en charge du système d’exploitation n’est fournie par Azure. | Préparé pour Azure sous condition. Nous vous recommandons d’effectuer une mise à niveau du système d’exploitation avant de migrer vers Azure.
Clients Windows 7, 8 et 10 | Azure assure un support avec abonnement Visual Studio uniquement. | Préparé pour Azure sous condition
Windows Vista, XP Professionnel | La date limite de prise en charge de ces systèmes d’exploitation est passée. La machine peut démarrer dans Azure, mais aucune prise en charge du système d’exploitation n’est fournie par Azure. | Préparé pour Azure sous condition. Nous vous recommandons d’effectuer une mise à niveau du système d’exploitation avant de migrer vers Azure.
Linux | Azure approuve ces [systèmes d’exploitation Linux](../virtual-machines/linux/endorsed-distros.md). D’autres systèmes d’exploitation Linux peuvent démarrer dans Azure, mais nous vous recommandons d’effectuer une mise à niveau du système d’exploitation vers une version approuvée avant de migrer vers Azure. | Disponible pour Azure si la version est approuvée.<br/><br/>Préparé pour Azure sous condition si la version n’est pas approuvée.
Autres systèmes d’exploitation<br/><br/> (par exemple, Oracle Solaris, Apple Mac OS, FreeBSD, etc.) | Azure n’approuve pas ces systèmes d’exploitation. La machine peut démarrer dans Azure, mais aucune prise en charge du système d’exploitation n’est fournie par Azure. | Préparé pour Azure sous condition. Nous vous recommandons d’installer un système d’exploitation pris en charge avant de migrer vers Azure.  
Système d’exploitation spécifié comme *Autre* dans vCenter Server | Azure Migrate ne peut pas identifier le système d’exploitation dans ce cas. | État de la préparation inconnu. Vérifiez que le système d’exploitation en cours d’exécution sur la machine virtuelle est pris en charge dans Azure. 
Systèmes d’exploitation 32 bits | La machine peut démarrer dans Azure, mais il est possible qu’Azure ne fournisse pas une prise en charge complète. | Préparé pour Azure sous condition. Envisagez de mettre à niveau le système d’exploitation 32 bits de la machine vers un système d’exploitation 64 bits avant de migrer vers Azure.

## <a name="sizing"></a>Dimensionnement

Une fois qu’une machine a été marquée comme disponible pour Azure, Azure Migrate dimensionne la machine virtuelle et ses disques pour Azure. Si le critère de dimensionnement spécifié dans les propriétés d’évaluation est le dimensionnement basé sur les performances, Azure Migrate prend en compte l’historique des performances de la machine pour identifier une taille de machine virtuelle dans Azure. Cette méthode est utile dans les scénarios où vous avez suralloué la machine virtuelle locale, mais où l’utilisation est faible et vous souhaitez dimensionner correctement les machines virtuelles dans Azure afin de réduire les coûts.

> [!NOTE]
> Azure Migrate collecte l’historique des performances des machines virtuelles locales à partir de vCenter Server. Pour garantir un dimensionnement correct, vérifiez que le paramètre de statistiques dans vCenter Server est défini au niveau 3, et attendez au moins une journée avant de lancer la détection des machines virtuelles locales. Si le paramètre de statistiques dans vCenter Server est inférieur au niveau 3, les données de performances pour le disque et le réseau ne sont pas collectées. 

Si vous ne souhaitez pas prendre en compte l’historique des performances et que vous voulez migrer la machine virtuelle telle quelle sur Azure, vous pouvez spécifier le critère de dimensionnement *Localement*. Dans ce cas, Azure Migrate dimensionne les machines virtuelles d’après la configuration locale sans prendre en compte les données d’utilisation.

### <a name="performance-based-sizing"></a>Dimensionnement en fonction des performances

Pour le dimensionnement basé sur les performances, Azure Migrate commence par les disques attachés à la machine virtuelle, suivi des cartes réseau, puis il mappe une machine virtuelle Azure en fonction des exigences de calcul de la machine virtuelle locale. 
 
- **Disques** : Azure Migrate tente de mapper chaque disque attaché à la machine à un disque dans Azure. 
    
    > [!NOTE]
    > Azure Migrate prend uniquement en charge les disques gérés pour l’évaluation.
    
    - Pour obtenir les E/S disque par seconde (IOPS) et le débit (MBps) effectifs, Azure Migrate multiplie les E/S disque par seconde et le débit par le facteur de confort. En fonction des valeurs effectives d’IOPS et de débit, Azure Migrate détermine si le disque doit être mappé à un disque standard ou premium dans Azure.
    - Si Azure Migrate ne parvient pas à trouver un disque avec les IOPS et le débit requis, il marque la machine comme inadaptée à Azure. [En savoir plus](../azure-subscription-service-limits.md#storage-limits) sur les limites par disque et par machine virtuelle dans Azure.
    - S’il trouve un ensemble de disques adaptés, Azure Migrate sélectionne ceux qui prennent en charge la méthode de redondance du stockage et l’emplacement spécifié dans les paramètres d’évaluation.
    - Si plusieurs disques sont éligibles, il sélectionne celui dont le coût est le plus bas.
    - Si les données de performances des disques ne sont pas disponibles, tous les disques sont mappés à des disques standard dans Azure.

- **Cartes réseau** : Azure Migrate essaie de trouver une machine virtuelle Azure qui peut prendre en charge le nombre de cartes réseau raccordées à la machine locale et les performances requises par ces cartes réseau.
    - Pour obtenir les performances réseau effectives de la machine virtuelle locale, Azure Migrate regroupe les données transmises par seconde (Mbits/s) à partir de l’ordinateur (données sortantes), parmi toutes les cartes réseau, et applique le facteur de confort. Ce nombre est utilisé pour trouver une machine virtuelle Azure qui prend en charge les performances réseau nécessaires.
    - Outre les performances réseau, il évalue également si la machine virtuelle Azure peut prendre en charge le nombre de cartes réseau requises.
    - Si aucune donnée de performances réseau n’est disponible, seul le nombre de cartes réseau est pris en considération pour le dimensionnement de la machine virtuelle.

- **Taille de machine virtuelle** : une fois les exigences de réseau et de stockage calculées, Azure Migrate prend en compte les besoins en UC et en mémoire afin de trouver une taille de machine virtuelle appropriée dans Azure.
    - Azure Migrate examine les cœurs et la mémoire utilisés, et applique le facteur de confort pour obtenir les cœurs et la mémoire effectifs. En fonction de ce nombre, il s’efforce de trouver une taille de machine virtuelle adaptée dans Azure.
    - Si aucune taille adaptée n’est trouvée, la machine est marquée comme inadaptée à Azure.
    - Si une taille adaptée est trouvée, Azure Migrate applique les calculs de mise en réseau et de stockage. Il applique ensuite les paramètres d’emplacement et de niveau tarifaire de façon à obtenir la recommandation de taille de machine virtuelle finale.
    - S’il existe plusieurs tailles de machine virtuelle Azure éligibles, celle dont le coût est le plus faible est recommandée.

### <a name="as-on-premises-sizing"></a>Dimensionnement « Localement »
Si le critère de dimensionnement est *Localement*, Azure Migrate ne tient pas compte de l’historique des performances des machines virtuelles, et alloue les machines virtuelles et les disques en fonction de la taille allouée localement.
- **Stockage** : pour chaque disque, un disque standard dans Azure est recommandé avec la même taille que le disque local.
- **Réseau** : pour chaque carte réseau, une carte réseau dans Azure est recommandée.
- **Calcul** : Azure Migrate examine le nombre de cœurs et la taille de la mémoire de la machine virtuelle locale, et recommande une machine virtuelle Azure avec la même configuration. S’il existe plusieurs tailles de machine virtuelle Azure éligibles, celle dont le coût est le plus faible est recommandée.
 
### <a name="confidence-rating"></a>Niveau de confiance

Chaque évaluation dans Azure Migrate est associée à un niveau de confiance allant de 1 étoile à 5 étoiles (1 étoile constituant la valeur la plus faible, et 5 étoiles la valeur la plus élevée). Le niveau de confiance est assigné à une évaluation en fonction de la disponibilité des points de données nécessaires pour calculer l’évaluation. Le niveau de confiance d’une évaluation permet d’évaluer la fiabilité des recommandations de taille fournies par Azure Migrate. 

Le niveau de confiance est utile quand vous effectuez un *dimensionnement basé sur les performances*, car Azure Migrate peut ne pas disposer de suffisamment de points de données pour effectuer un dimensionnement basé sur l’utilisation. Pour le *dimensionnement local*, le niveau de confiance s’élève toujours à 5 étoiles, car Azure Migrate dispose de tous les points de données nécessaires au dimensionnement de la machine virtuelle. 

Pour le dimensionnement basé sur les performances de la machine virtuelle, Azure Migrate a besoin des données d’utilisation relatives à l’UC et à la mémoire. Par ailleurs, pour chaque disque attaché à la machine virtuelle, les E/S par seconde en lecture/écriture et le débit sont des données nécessaires. De même, pour chaque carte réseau jointe à la machine virtuelle, Azure Migrate doit permettre au réseau entrant/sortant de respecter un dimensionnement basé sur les performances. Si aucun des chiffres d’utilisation ci-dessus n’est disponible dans vCenter Server, la recommandation de dimensionnement effectuée par Azure Migrate peut ne pas être fiable. Selon le pourcentage de points de données disponibles, le niveau de confiance pour l’évaluation est fourni :

   **Disponibilité des points de données** | **Niveau de confiance**
   --- | ---
   0 %-20 % | 1 étoile
   21 %-40 % | 2 étoiles
   41 %-60 % | 3 étoiles
   61 %-80 % | 4 étoiles
   81 %-100 % | 5 étoiles

Tous les points de données d’une évaluation peuvent ne pas être disponibles pour l’une des raisons suivantes :
- Le paramètre des statistiques dans vCenter Server n’est pas défini sur le niveau 3 et l’évaluation présente un dimensionnement basé sur les performances en tant que critère de dimensionnement. Si le paramètre des statistiques dans vCenter Server est inférieur au niveau 3, les données de performances pour le disque et le réseau ne sont pas collectées à partir de vCenter Server. Dans ce cas, la recommandation fournie par Azure Migrate pour le disque et le réseau n’est pas basée sur l’utilisation. Pour le stockage, Azure Migrate recommande des disques standard sans tenir compte des E/S par seconde/du débit du disque. Azure Migrate ne peut pas déterminer si le disque a besoin d’un disque premium dans Azure.
- Le paramètre des statistiques dans vCenter Server a été défini sur le niveau 3 pour une durée plus courte, avant le lancement de la découverte. Par exemple, intéressons-nous au scénario dans lequel vous faites passer le paramètre de statistiques au niveau 3 aujourd’hui et lancez la découverte en utilisant l’appliance de collecteur demain (après 24 heures). Si vous créez une évaluation pour une journée, vous disposez de tous les points de données, et le niveau de confiance de l’évaluation est de 5 étoiles. Mais si vous modifiez la durée des performances dans les propriétés d’évaluation pour la définir à un mois, le niveau de confiance diminue, car les données de performances du disque et du réseau pour mois dernier ne sont pas disponibles. Si vous souhaitez prendre en compte les données de performances du dernier mois, il est recommandé de conserver le paramètre des statistiques vCenter Server au niveau 3 pendant un mois avant d’exécuter la découverte. 
- Plusieurs machines virtuelles ont été arrêtées pendant la période de calcul de l’évaluation. Si des machines virtuelles ont été mises hors tension pendant un certain temps, vCenter Server ne disposera pas des données de performances pour cette période. 
- Quelques machines virtuelles ont été créées pendant la période de calcul de l’évaluation. Par exemple, si vous créez une évaluation de l’historique des performances du mois dernier, mais si la création de quelques machines virtuelles dans l’environnement ne remonte qu’à une semaine. Dans ce cas, l’historique des performances des nouvelles machines virtuelles ne sera pas disponible pour toute la durée définie.

> [!NOTE]
> Si le niveau de confiance d’une évaluation est inférieur à 4 étoiles, nous vous recommandons de faire passer les paramètres de statistiques vCenter Server au niveau 3, de patienter pendant toute la période que vous souhaitez prendre en compte pour l’évaluation (1 jour/1 semaine/1 mois), puis de procéder à la découverte et à l’évaluation. Si l’exemple précédent ne peut pas être effectué, le dimensionnement basé sur les performances est susceptible de manquer de fiabilité et il est recommandé de basculer vers le *dimensionnement Localement* en changeant les propriétés de l’évaluation.

## <a name="monthly-cost-estimation"></a>Estimation des coûts mensuels

Une fois les recommandations de dimensionnement terminées, Azure Migrate calcule les coûts de calcul et de stockage après la migration.

- **Calcul du coût** : à partir de la taille de machine virtuelle Azure recommandée, Azure Migrate utilise l’API de facturation pour calculer le coût mensuel de la machine virtuelle. Le calcul prend en compte les paramètres de système d’exploitation, de Software Assurance, d’emplacement et de devise. Il agrège le coût de toutes les machines pour calculer le coût de calcul mensuel total.
- **Coût de stockage** : le coût de stockage mensuel d’une machine est calculé en additionnant le coût mensuel de tous les disques qui lui sont attachés. Azure Migrate calcule les coûts totaux de stockage mensuel en additionnant le coût de stockage de toutes les machines. Actuellement, le calcul ne prend pas en compte les offres spécifiées dans les paramètres d’évaluation.

Les coûts sont affichés dans la devise spécifiée dans les paramètres d’évaluation. 


## <a name="next-steps"></a>Étapes suivantes

[Créer une évaluation pour des machines virtuelles VMware locales](tutorial-assessment-vmware.md)
