---
title: Gérer les ressources de calcul dans Azure SQL Data Warehouse | Microsoft Docs
description: Découvrez les capacités de montée en puissance des performances dans Azure SQL Data Warehouse. Procédez à une montée en puissance en ajustant la valeur DWU ou allégez les coûts en suspendant l’entrepôt de données.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: ''
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/20/2018
ms.author: elbutter
ms.openlocfilehash: c34e37f0c6393c65d4b60705012769608bb7395b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Gérer les ressources de calcul dans Azure SQL Data Warehouse
Découvrez comment gérer les ressources de calcul dans Azure SQL Data Warehouse. Vous pouvez alléger les coûts en suspendant l’entrepôt de données, ou mettre à l’échelle ce dernier afin de répondre aux exigences en matière de niveau de performance. 

## <a name="what-is-compute-management"></a>En quoi consiste la gestion des ressources de calcul ?
L’architecture de SQL Data Warehouse sépare le stockage du calcul, ce qui permet de les mettre à l’échelle indépendamment l’un de l’autre. En conséquence, vous pouvez procéder à la mise à l’échelle du calcul afin de répondre aux exigences en matière de niveau de performance, sans modifier le stockage des données. Vous avez également la possibilité de suspendre ou reprendre des ressources de calcul. Dans le cadre de cette architecture, la [facturation](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) du calcul est donc effectuée séparément de celle du stockage. Si vous n’avez pas besoin d’utiliser votre entrepôt de données pendant un certain temps, vous pouvez alléger les coûts de calcul en suspendant les ressources de calcul. 

## <a name="scaling-compute"></a>Mise à l’échelle du calcul
Vous pouvez procéder à la montée ou descente en puissance du calcul en ajustant le paramétrage relatif aux [unités Data Warehouse Units (DWU)](what-is-a-data-warehouse-unit-dwu-cdwu.md) de votre entrepôt de données. Les performances de chargement et de requête peuvent s’accroître de manière linéaire à mesure que vous augmentez la valeur DWU. SQL Data Warehouse offre des [niveaux de service](performance-tiers.md#service-levels) pour les DWU qui garantissent une évolution notable des performances lorsque vous procédez à une montée ou descente en puissance. 

Pour plus d’informations sur la procédure de montée en puissance, consultez les articles de démarrage rapide pour le [portail Azure](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md) ou [T-SQL](quickstart-scale-compute-tsql.md). Vous pouvez également effectuer des opérations de montée en puissance à l’aide d’une [API REST](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Pour procéder à une mise à l’échelle, le service SQL Data Warehouse commence par tuer toutes les requêtes entrantes, puis il restaure les transactions afin de garantir un état cohérent. La mise à l’échelle ne se produit qu’une fois la restauration des transactions effectuée. Dans le cadre d’une opération de mise à l’échelle, le système détache la couche de stockage des nœuds de calcul, ajoute des nœuds de calcul, puis rattache la couche de stockage à la couche de calcul. Chaque entrepôt de données présente 60 distributions, qui sont réparties uniformément entre les nœuds de calcul. L’ajout de nœuds de calcul augmente la puissance de calcul. À mesure que le nombre de nœuds de calcul augmente, le nombre de distributions par nœud de calcul diminue, offrant ainsi davantage de puissance de calcul pour vos requêtes. De même, la diminution de la valeur DWU abaisse le nombre de nœuds de calcul, ce qui réduit les ressources de calcul pour les requêtes.

Le tableau ci-après illustre l’évolution du nombre de distributions par nœud de calcul à mesure que la valeur DWU change.  La valeur DWU6000 fournit 60 nœuds de calcul et offre un niveau de performance de requête bien supérieur à celui de la valeur DWU100. 

| Data Warehouse Units  | \# de nœuds de calcul | \# de distributions par nœud |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5.                  | 12                           |
| 600  | 6.                  | 10                           |
| 1 000 | 10                 | 6.                            |
| 1 200 | 12                 | 5.                            |
| 1 500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Détermination du nombre d’unités DWU optimal

Pour découvrir les avantages d’une montée en puissance en termes de niveau de performance, en particulier dans le cas des valeurs DWU les plus élevées, vous devez utiliser un jeu de données d’au moins 1 To. Pour déterminer le nombre d’unités DWU idéal pour votre entrepôt de données, essayez d’effectuer une montée et une descente en puissance. Exécutez quelques requêtes avec différentes valeurs DWU après avoir chargé vos données. La mise à l’échelle étant rapide, vous pouvez essayer plusieurs niveaux de performances en une heure ou moins. 

Recommandations en matière de recherche de la valeur DWU optimale :

- Si vous disposez d’un entrepôt de données en développement, commencez par sélectionner un nombre réduit d’unités DWU.  DW400 ou DW200 est un bon point de départ.
- Analysez les performances de votre application, en observant notamment le nombre d’unités DWU sélectionné.
- Déterminez la mesure dans laquelle vous devez augmenter ou diminuer le nombre d’unités DWU à l’aide d’une mise à l’échelle linéaire. 
- Continuez à effectuer des ajustements jusqu’à ce que vous atteigniez le niveau de performances requis par vos activités.

## <a name="when-to-scale-out"></a>Quand procéder à une montée en puissance
Une montée en puissance par l’augmentation de la valeur DWU a une incidence sur les aspects des performances ci-après :

- amélioration linéaire des performances du système pour les analyses, les agrégations et les instructions CTAS (Create Table As Select) ;
- augmentation du nombre de processus de lecture et d’écriture pour le chargement de données ;
- nombre maximal de requêtes simultanées et d’emplacements de concurrence.

Recommandations concernant les cas dans lesquels augmenter la valeur DWU :

- Avant d’exécuter une opération de chargement ou de transformation de données importante, effectuez une montée en puissance afin que vos données soient disponibles plus rapidement.
- Pendant les heures de pointe, procédez à une montée en puissance pour prendre en charge un nombre plus important de requêtes simultanées. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Que faire si la montée en puissance n’améliore pas les performances ?

L’ajout d’unités DWU augmente le parallélisme. Si le travail est réparti uniformément entre les nœuds de calcul, ce surcroît de parallélisme accroît le niveau de performance des requêtes. Si la montée en puissance n’améliore pas votre niveau de performance, certaines raisons peuvent l’expliquer. Il peut exister une asymétrie des données entre les distributions, ou il est possible que des requêtes introduisent un déplacement important des données. Pour examiner les problèmes liés aux performances des requêtes, consultez l’article relatif à la [résolution des problèmes de performances](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Suspension et reprise du calcul
La suspension du calcul consiste à détacher la couche de stockage des nœuds de calcul. Les ressources de calcul sont libérées de votre compte. Pendant la suspension du calcul, vous n’êtes pas facturé pour ce dernier. La reprise du calcul rattache le stockage aux nœuds de calcul et réenclenche la facturation du calcul. Lorsque vous suspendez un entrepôt de données :

* Les ressources de calcul et de mémoire sont renvoyées dans le pool des ressources disponibles du centre de données.
* Les coûts de DWU sont nuls pendant toute la durée de la suspension.
* Le stockage de données n’est pas affecté et vos données restent intactes. 
* SQL Data Warehouse annule toutes les opérations en cours d’exécution ou en file d’attente.

Lorsque vous reprenez un entrepôt de données :

* SQL Data Warehouse acquiert les ressources de calcul et de mémoire correspondant à votre paramétrage DWU.
* Les frais de calcul concernant vos unités DWU vous sont de nouveau facturés.
* Vos données deviennent disponibles.
* Une fois que l’entrepôt de données est en ligne, vous devez redémarrer vos requêtes de charge de travail.

Si vous souhaitez que votre entrepôt de données soit toujours accessible, envisagez de le réduire à sa taille minimale au lieu de le suspendre. 

Pour plus d’informations sur les procédures de suspension et de reprise, consultez les articles de démarrage rapide pour le [portail Azure](pause-and-resume-compute-portal.md) ou pour [PowerShell](pause-and-resume-compute-powershell.md). Vous pouvez également utiliser [l’API REST de suspension](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) ou [l’API REST de reprise](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Vider les transactions avant la suspension ou la mise à l’échelle
Nous vous recommandons d’autoriser l’achèvement des transactions existantes avant d’initialiser une opération de suspension ou de mise à l’échelle.

Lorsque vous suspendez ou mettez à l’échelle de votre SQL Data Warehouse, en arrière-plan, vos requêtes sont annulées lorsque vous lancez la requête de suspension de mise à l’échelle.  L’annulation d’une simple requête SELECT est une opération rapide et n’a quasiment aucun impact sur le temps nécessaire à la suspension ou à la mise à l’échelle de votre instance.  Toutefois, les requêtes transactionnelles, qui modifient vos données ou la structure des données, ne pourront peut-être pas s’arrêter rapidement.  **Par définition, les requêtes transactionnelles doivent être terminées dans leur intégralité ou annuler leurs modifications.**  L’annulation du travail effectué par une requête transactionnelle peut être aussi longue, voire plus, que la modification originale appliquée par la requête.  Par exemple, si vous annulez une requête qui supprimait des lignes et était en cours d’exécution depuis une heure, le système mettra peut-être une heure à insérer à nouveau les lignes supprimées.  Si vous exécutez une suspension ou une mise à l’échelle pendant que les transactions sont en cours, votre suspension ou mise à l’échelle peut sembler très longue, car la suspension et la mise à l’échelle doivent attendre la fin de la restauration avant de se lancer.

Consultez également les articles fournissant une [présentation des transactions](sql-data-warehouse-develop-transactions.md) et décrivant la [procédure d’optimisation des transactions](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatisation de la gestion du calcul
Pour plus d’informations sur l’automatisation des opérations de gestion du calcul, consultez l’article décrivant comment [gérer le calcul avec Azure Functions](manage-compute-with-azure-functions.md).

L’exécution de chacune des opérations de montée en puissance, de suspension et de reprise peut nécessiter plusieurs minutes. Si vous procédez à une mise à l’échelle, une suspension ou une reprise automatiques, nous vous recommandons d’implémenter une logique pour vous assurer que certaines opérations ont été effectuées avant d’exécuter une autre action. La vérification de l’état de l’entrepôt de données au niveau de différents points de terminaison vous permet d’automatiser correctement de telles opérations. 

Pour vérifier l’état de l’entrepôt de données, voir les articles de démarrage rapide pour [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) ou [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state). Vous pouvez également vérifier l’état de l’entrepôt de données à l’aide d’une [API REST](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Autorisations

La mise à l’échelle de l’entrepôt de données requiert les autorisations décrites dans [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse.md).  La suspension et la reprise requièrent l’autorisation [Collaborateur de base de données SQL](../active-directory/role-based-access-built-in-roles.md#sql-db-contributor), notamment Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Étapes suivantes
Un autre aspect de la gestion des ressources de calcul concerne l’allocation de ressources de calcul différentes pour des requêtes spécifiques. Pour plus d’informations, consultez l’article [Classes de ressources pour la gestion des charges de travail](resource-classes-for-workload-management.md).
