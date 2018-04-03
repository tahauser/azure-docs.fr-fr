---
title: Azure Site Recovery deployment planner pour le déploiement de VMware vers Azure| Microsoft Docs
description: Il s’agit du guide de l’utilisateur d’Azure Site Recovery deployment planner.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: garavd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/09/2018
ms.author: nisoneji
ms.openlocfilehash: 337217e66fe4d3780af197911a0e72c6f936e411
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="cost-estimation-report-of-azure-site-recovery-deployment-planner"></a>Rapport d’estimation de coût du planificateur de déploiement Azure Site Recovery  

Le rapport du planificateur de déploiement fournit le résumé de l’estimation des coûts dans les feuilles [Recommandations](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) et l’analyse des coûts détaillée dans la feuille Estimation du coût. Une analyse des coûts détaillée par machine virtuelle est proposée. 

### <a name="cost-estimation-summary"></a>Résumé de l’estimation des coûts 
Le graphique affiche la vue Résumé du coût estimé de la récupération d’urgence totale (DR) pour Azure de votre région cible choisie et avec la devise que vous avez spécifiée pour la génération du rapport.
Résumé de l’estimation des coûts

![Résumé de l’estimation des coûts](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Le résumé vous aide à comprendre le coût que vous devez payer pour le stockage, le calcul, le réseau et la licence lorsque vous protégez toutes vos machines virtuelles compatibles avec Azure à l’aide de Azure Site Recovery. Le coût est calculé à partir de machines virtuelles compatibles et non sur toutes les machines virtuelles profilées.  
 
Vous pouvez afficher le coût mensuel ou annuel. En savoir plus sur les [régions cibles prises en charge](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) et les [devises prises en charge](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Coût par composant** Le coût total de la récupération d’urgence est divisé en quatre composants : coût de la licence d’Azure Site Recovery, du stockage, du réseau et du calcul. Le coût est calculé en fonction de la consommation facturée pendant la réplication et au moment de la récupération d’urgence pour le calcul, le stockage (premium et standard), le ExpressRoute/VPN configuré entre le site local et Azure, et la licence de Azure Site Recovery.

**Coût par état** Le coût total de la récupération d’urgence (DR) est catégorisé selon deux états différents, la réplication et l’extraction de la récupération d’urgence. 

**Coût de la réplication** : le coût qui sera engendré par la réplication. Il couvre le coût du stockage, du réseau et de la licence d’Azure Site Recovery. 

**Coût d’extraction de la récupération d’urgence** : le coût engendré par les basculements de test. Azure Site Recovery prépare des machines virtuelles pendant le basculement de test. Le coût d’extraction de la récupération d’urgence couvre les coûts de calcul et de stockage des machines virtuelles en cours d’exécution. 

**Coût de stockage Azure par mois/année** Il montre le coût de stockage total qui sera engagé pour le stockage standard et premium pour la réplication et l’extraction de récupération d’urgence.

## <a name="detailed-cost-analysis"></a>Analyse du coût détaillée
Les prix Azure de calcul, de stockage, de réseau, etc., varient entre les régions Azure. Vous pouvez générer un rapport d’estimation des coûts avec les prix Azure les plus récents selon votre abonnement, l’offre associée à votre abonnement et pour la région cible Azure dans la devise indiquée. Par défaut, l’outil utilise la région Azure Ouest des États-Unis 2 et la devise dollar américain (USD). Si vous avez utilisé une autre région et devise, la prochaine fois que vous générez un rapport sans ID d’abonnement, ID d’offre, région cible et devise, il utilisera les prix de la dernière région cible utilisée et la dernière devise utilisée pour l’estimation des coûts.
Cette section présente l’ID d’abonnement et l’ID d’offre que vous avez utilisés pour la génération de rapports.  Si aucun n’a été utilisé, il est vide.

Dans l’ensemble du rapport, les cellules marquées en gris sont en lecture seule. Les cellules en blanc peuvent être modifiées selon vos besoins.

![Estimation des coûts details1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Coût global de récupération d’urgence par composants
La première section indique le coût global de récupération d’urgence par composants et le coût de récupération d’urgence par états. 

**Calcul** : coût de machines virtuelles IaaS exécutées sur Azure pour les besoins de récupération d’urgence. Il inclut les machines virtuelles créées par Azure Site Recovery pendant les extractions de la récupération d’urgence (basculements de test) et les machines virtuelles exécutées sur Azure telles que SQL Server avec groupes de disponibilité AlwaysOn et contrôleurs de domaine/serveurs de noms de domaine.

**Stockage** : coût de consommation de stockage Azure pour les besoins de récupération d’urgence. Il inclut la consommation du stockage pour la réplication et au cours des exercices de récupération d’urgence.
Réseau : ExpressRoute et coûts VPN de Site à Site pour les besoins de récupération d’urgence. 

**Licence ASR** : coût de la licence d’Azure Site Recovery pour toutes les machines virtuelles compatibles. Si vous avez saisi manuellement une machine virtuelle dans la table d’analyse des coûts détaillée, le coût de la licence Azure Site Recovery est également inclus pour cette machine virtuelle.

### <a name="overall-dr-cost-by-states"></a>Coût global de récupération d’urgence par états
Le coût total de la récupération d’urgence est catégorisé selon deux états différents, la réplication et l’extraction de la récupération d’urgence.

**Coût de réplication** : coût engagé au moment de la réplication. Il couvre le coût du stockage, du réseau et de la licence d’Azure Site Recovery. 

**Coût d’extraction de la récupération d’urgence** : coût engagé au moment des extractions de la récupération d’urgence. Azure Site Recovery prépare des machines virtuelles pendant les extractions de la récupération d’urgence. Le coût d’extraction de la récupération d’urgence couvre les coûts de calcul et de stockage des machines virtuelles en cours d’exécution.
Extraction de la récupération d’urgence totale par an = Nombre d’extractions de la récupération d’urgence x Durée de chaque extraction de la récupération d’urgence (jours) Coût moyen d’extraction de la récupération d’urgence - Coût total de l’extraction de la récupération d’urgence / 12

### <a name="storage-cost-table"></a>Table des coûts de stockage :
Ce tableau montre les coûts de stockage standard et premium liés à la réplication et aux extractions de la récupération d’urgence avec et sans remise.

### <a name="site-to-azure-network"></a>Du site vers le réseau Azure
Sélectionnez le paramètre approprié en fonction de vos besoins. 

**ExpressRoute** : par défaut, l’outil sélectionne le plan ExpressRoute le plus proche qui correspond à la bande passante réseau requise pour la réplication delta. Vous pouvez modifier le plan en fonction de vos besoins.

**Passerelle VPN** : sélectionnez la passerelle VPN si vous en avez une dans votre environnement. Par défaut, N/A.

**Région cible** : région Azure spécifiée pour la récupération d’urgence. Le prix utilisé dans le rapport pour le calcul, le stockage, le réseau et la licence est basé sur la tarification Azure pour cette région. 

### <a name="vm-running-on-azure"></a>Machine virtuelle s’exécutant sur Azure
Si vous disposez d’un contrôleur de domaine ou d’une machine virtuelle DNS ou SQL Server avec groupes de disponibilité AlwaysOn s’exécutant sur Azure pour la récupération d’urgence, vous pouvez indiquer le nombre de machines virtuelles et la taille à prendre en compte de leur coût de calcul dans le coût total de la récupération d’urgence. 

### <a name="apply-overall-discount-if-applicable"></a>Appliquer la remise globale le cas échéant
Si vous êtes client ou partenaire Azure et si vous bénéficiez d’une remise sur la tarification Azure globale, vous pouvez utiliser ce champ. L’outil applique la remise (en %) sur tous les composants.

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Nombre de types de machines virtuelles et coût du calcul (par an)
Ce tableau montre le nombre de machines virtuelles Windows et non Windows et le coût du calcul de l’extraction de la récupération d’urgence associé.

### <a name="settings"></a>Paramètres 
**Utilisation de disques managés** : indique si le disque managé est utilisé au moment de l’extraction de la récupération d’urgence. La valeur par défaut de ce paramètre est Oui. Si vous avez défini -UseManagedDisks sur Non, le prix du disque non managé est utilisé pour le calcul du coût.

**Devise** : devise dans laquelle le rapport est généré. Durée de coût : vous pouvez afficher tous les coûts pour le mois ou l’ensemble de l’année. 

## <a name="detailed-cost-analysis-table"></a>Tableau d’analyse du coût détaillée
![Analyse du coût détaillée](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) Le tableau répertorie la répartition des coûts pour chaque machine virtuelle compatible. Vous pouvez également utiliser cette table pour obtenir le coût estimé de récupération d’urgence Azure des machines virtuelles non profilées en ajoutant manuellement des machines virtuelles. Elle est utile lorsque vous devez estimer les coûts Azure d’un nouveau déploiement de récupération d’urgence sans profilage détaillé.
Pour ajouter manuellement des machines virtuelles : 
1.  Cliquez sur le bouton « Ligne d’insertion » pour insérer une nouvelle ligne entre les lignes de début et de fin.

2.  Remplissez les colonnes suivantes en fonction de la taille approximative de la machine virtuelle et le nombre de machines virtuelles correspondant à cette configuration : 

* Nombre de machines virtuelles, taille IaaS (votre sélection)
* Type de stockage (Standard/Premium)
* Taille de stockage totale de machine virtuelle (en Go)
* Nombre d’extractions de la récupération d’urgence par an 
* Durée de chaque extraction de la récupération d’urgence (en jours) 
* Type de système d’exploitation
* Redondance des données 
* Azure Hybrid Benefit

3.  Vous pouvez appliquer la même valeur à toutes les machines virtuelles du tableau en cliquant sur le bouton « Appliquer à tous » pour le nombre d’extraction de la récupération d’urgence par an, la durée de chaque extraction de la récupération d’urgence (en jours), la redondance des données et d’Azure Hybrid Use Benefit.

4.  Cliquez sur « Recalculer le coût » pour mettre à jour le coût.

**VMName** : nom de la machine virtuelle.

**Nombre de machines virtuelles**: le nombre de machines virtuelles qui correspondent à la configuration. Vous pouvez mettre à jour le nombre de machines virtuelles existantes si des machines virtuelles de configuration similaire ne sont pas profilées mais sont protégées.

**Taille de IaaS (recommandation)** : il s’agit du rôle de machine virtuelle de la machine virtuelle compatible recommandée par l’outil. 

**Taille IaaS (votre sélection)** : par défaut, elle est identique à la taille de rôle de machine virtuelle recommandée. Vous pouvez changer le rôle selon vos besoins. Le coût du calcul est basé sur la taille du rôle de machine virtuelle sélectionnée.

**Type de stockage** : type de stockage utilisé par la machine virtuelle. Il s’agit du stockage Standard ou Premium.

**Taille de stockage totale de machine virtuelle (en Go)** : stockage total de la machine virtuelle.

**Nombre d’extractions de la récupération d’urgence par an** : nombre de fois que vous effectuez des extractions de la récupération d’urgence en une année. Par défaut, il est de 4 fois par an. Vous pouvez modifier la période pour des machines virtuelles spécifiques ou appliquer la nouvelle valeur à toutes les machines virtuelles en saisissant la nouvelle valeur sur la ligne du haut et en cliquant sur le bouton « Appliquer à tous ». Le coût total de l’extraction de la récupération d’urgence est calculé en fonction du nombre d’extractions de la récupération d’urgence et la période de cette dernière.  

**Durée de chaque extraction de la récupération d’urgence (en jours)** : durée de chaque extraction de la récupération d’urgence. Par défaut, elle est de 7 jours tous les 90 jours selon l’[avantage Récupération d’urgence de la Software Assurance](https://azure.microsoft.com/en-in/pricing/details/site-recovery). Vous pouvez modifier la période pour des machines virtuelles spécifiques ou appliquer la nouvelle valeur à toutes les machines virtuelles en saisissant la nouvelle valeur sur la ligne du haut et en cliquant sur le bouton « Appliquer à tous ». Le coût total de l’extraction de la récupération d’urgence est calculé en fonction du nombre d’extractions de la récupération d’urgence et la période de cette dernière.
  
**Type de système d’exploitation** : type de système d’exploitation de la machine virtuelle. Il peut s’agir de Windows ou de Linux. Si le type de système d’exploitation est Windows, Azure Hybrid Use Benefit peut être appliqué à cette machine virtuelle. 

**Redondance des données** : une des options suivantes est possible : stockage localement redondant (LRS), stockage géo-redondant (GRS) ou stockage géo-redondant avec accès en lecture (RA-GRS). La valeur par défaut est LRS. Vous pouvez modifier le type en fonction de votre compte de stockage pour les machines virtuelles spécifiques, ou appliquer le nouveau type à toutes les machines virtuelles en modifiant le type de la ligne du haut et en cliquant sur « Appliquer à tous ».  Le coût du stockage pour la réplication est calculé en fonction du prix de la redondance des données sélectionné. 

**Azure Hybrid Benefit** : vous pouvez appliquer Azure Hybrid Benefit aux machines virtuelles Windows, le cas échéant.  La valeur par défaut est Oui. Vous pouvez modifier le paramètre pour les machines virtuelles spécifiques, ou mettre à jour toutes les machines virtuelles en cliquant sur le bouton « Appliquer à tous ».

**Consommation Azure totale** : elle inclut les coûts de calcul, de stockage et de licence Azure Site Recovery pour la récupération d’urgence. Selon votre sélection, cela indique le coût mensuel ou annuel.

**Coût de réplication d’état stable** : il inclut le coût de stockage pour la réplication.

**Coût d’extraction de la récupération d’urgence total (moyenne)** : il inclut le coût de calcul et de stockage pour l’extraction de la récupération d’urgence.

**Coût de licence ASR** : coût de la licence d’Azure Site Recovery.

## <a name="supported-target-regions"></a>Régions cibles prises en charge
Le planificateur de déploiement Azure Site Recovery fournit une estimation de coût pour les régions Azure suivantes. Si votre région n’est pas répertoriée ci-dessous, vous pouvez utiliser une des régions suivantes dont la tarification se rapproche le plus de votre région.

eastus, eastus2, westus, centralus, northcentralus, southcentralus, northeurope, westeurope, eastasia, southeastasia, japaneast, japanwest, australiaeast, australiasoutheast, brazilsouth, southindia, centralindia, westindia, canadacentral, canadaeast, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth 

## <a name="supported-currencies"></a>Devises prises en charge
Le Planificateur de déploiement Azure Site Recovery peut générer le rapport de coût avec une des devises suivantes.

|Devise|NOM||Devise|NOM||Devise|NOM|
|---|---|---|---|---|---|---|---|
|ARS|Peso argentin ($)||AUD|Dollar australien ($)||BRL|Real brésilien (R$)|
|CAD|Dollar canadien ($)||CHF|Franc suisse (chf)||DKK|Couronne danoise (kr)|
|EUR|Euro (€)||GBP|Livre britannique (£)||HKD|Dollar de Hong Kong (HK$)|
|IDR|Roupie indonésienne (Rp)||INR|Roupie indienne (₹)||JPY|Yen japonais (¥)|
|KRW|Won coréen (₩)||MXN|Peso mexicain (MXN$)||MYR|Ringgit malais (RM$)|
|NOK|Couronne norvégienne (kr)||NZD|Dollar néo-zélandais ($)||RUB|Rouble russe (руб)|
|SAR|Riyal saoudien (SR)||SEK|Couronne suédoise (kr)||TWD|Dollar taiwanais (NT$)|
|TRY|Lire turque (TL)||USD| Dollar américain ($)||ZAR|Rand sud-africain (R)|

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la protection des [machines virtuelles de VMware vers Azure à l’aide d’Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-vmware-to-azure).
