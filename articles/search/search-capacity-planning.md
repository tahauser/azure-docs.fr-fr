---
title: "Planification des capacités de Recherche Azure | Microsoft Docs"
description: "Ajustez les ressources informatiques des partitions et des réplicas dans Recherche Azure, où chaque ressource est facturée en unités de recherche facturables."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 1dc16afe-56f9-439d-8874-1733ae1a2b74
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 47dcd5366ef8ba3d4598e6d418b11997c61bddea
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="scale-resource-levels-for-query-and-indexing-workloads-in-azure-search"></a>Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail dans Azure Search
Une fois que vous avez [choisi un niveau tarifaire](search-sku-tier.md) et [approvisionné un service de recherche](search-create-service-portal.md), l’étape suivante, facultative, consiste à augmenter le nombre de réplicas ou de partitions utilisés par votre service. Chaque niveau propose un nombre fixe d’unités de facturation. Cet article explique comment allouer ces unités pour obtenir une configuration optimale par rapport à vos exigences pour l’exécution des requêtes, l’indexation et le stockage.

La configuration des ressources est disponible lorsque vous configurez un service au [niveau basique](http://aka.ms/azuresearchbasic) ou à l’un des [niveaux standard](search-limits-quotas-capacity.md). Pour les services à ces niveaux, la capacité est achetée par incréments *d’unités de recherche* (SU) où chaque partition et chaque réplica est considéré comme une SU. 

La facture est proportionnelle au nombre de SU : moins elles sont nombreuses, plus la facture diminue. La facturation reste en vigueur tant que le service est configuré. Si vous n’utilisez pas temporairement un service, la seule manière d’éviter la facturation consiste à supprimer ce service, puis à le recréer lorsque vous en avez besoin.

> [!Note]
> La suppression d’un service a pour effet de supprimer toutes les données qui s’y trouvent. Il n’existe aucune fonctionnalité dans Azure Search permettant de sauvegarder et restaurer les données de recherche persistantes. Pour redéployer un index existant sur un nouveau service, vous devez exécuter le programme initialement utilisé pour le créer et le charger. 

## <a name="terminology-partitions-and-replicas"></a>Terminologie : partitions et réplicas
Les partitions et les réplicas sont les principales ressources qui sous-tendent un service de recherche.

| Ressource | Définition |
|----------|------------|
|*Partitions* | Fournissent un stockage des index et des E/S pour les opérations de lecture-écriture (par exemple, lors de la reconstruction ou de l’actualisation d’un index).|
|*Réplicas* | Instances du service de recherche, principalement utilisées pour équilibrer la charge des opérations de requête. Chaque réplica héberge toujours une seule copie d’un index. Si vous avez 12 réplicas, vous aurez 12 copies de chaque index chargées sur le service.|

> [!NOTE]
> Il n’existe aucun moyen de manipuler ou de gérer directement les index qui s’exécutent sur un réplica. Une copie de chaque index sur chaque réplica fait partie de l’architecture de service.
>

## <a name="how-to-allocate-partitions-and-replicas"></a>Comment allouer des partitions et des réplicas
Dans Azure Search, un service se voit initialement allouer un niveau minimal de ressources consistant en une partition et un réplica. Pour les niveaux qui le prennent en charge, vous pouvez ajuster progressivement les ressources de calcul en augmentant les partitions si vous avez besoin de plus de stockage et d’E/S ou de réplicas pour des volumes de requêtes plus importants ou des performances améliorées. Un seul service doit avoir suffisamment de ressources pour gérer toutes les charges de travail (indexation et requêtes). Vous ne pouvez pas subdiviser les charges de travail entre plusieurs services.

Pour augmenter ou modifier l’allocation des réplicas et des partitions, nous vous recommandons l’aide du portail Azure. Le portail applique des limites sur les combinaisons autorisées inférieures aux limites maximales :

1. Connectez-vous au [portail Azure](https://portal.azure.com/), puis sélectionnez le service de recherche.
2. Dans **Paramètres**, ouvrez le panneau **Mettre à l’échelle** et utilisez les curseurs pour augmenter ou diminuer le nombre de partitions et de réplicas.

Si vous avez besoin d’une approche codée ou scriptée de l’approvisionnement, [l’API REST de gestion](https://docs.microsoft.com/rest/api/searchmanagement/services) est une alternative au portail.

En règle générale, les applications de recherche ont besoin de plus de réplicas que de partitions, en particulier lorsque les opérations de service favorisent les charges de travail de requête. La section [Haute disponibilité](#HA) explique pourquoi.

> [!NOTE]
> Une fois qu’un service est configuré, il ne peut pas être mis à niveau vers une référence (SKU) supérieure. Vous devez créer un service de recherche au nouveau niveau et recharger vos index. Pour obtenir des instructions sur l’approvisionnement du service, voir [Créer un service Azure Search dans le portail](search-create-service-portal.md) .
>
>

<a id="HA"></a>

## <a name="high-availability"></a>Haute disponibilité
Nous vous recommandons généralement de démarrer avec une partition et un ou deux réplicas, puis de monter en puissance à mesure que les volumes de requête se créent. Les charges de travail de requêtes s’exécutent principalement sur des réplicas. Si vous nécessitez une haute disponibilité ou un débit plus important, vous aurez probablement besoin de réplicas supplémentaires.

Recommandations générales pour la haute disponibilité :

* Deux réplicas pour la haute disponibilité des charges de travail en lecture seule (requêtes)
* Trois réplicas minimum pour la haute disponibilité des charges de travail en lecture/écriture (les requêtes et l’indexation en tant que documents individuels sont ajoutées, mises à jour ou supprimées)

Les contrats de niveau de service (SLA) pour la Recherche Azure sont ciblés au moment des opérations de requête et des mises à jour d’index qui se composent d’ajout, de mise à jour ou de suppression de documents.

Le niveau De base est plafonné à une partition et trois réplicas. Si vous souhaitez pouvoir répondre immédiatement aux fluctuations de la demande sur le plan de l’indexation et du débit des requêtes, songez à passer à l’un des niveaux Standard.

### <a name="index-availability-during-a-rebuild"></a>Disponibilité des index lors d’une reconstruction

La haute disponibilité pour Azure Search se rapporte aux requêtes et aux mises à jour d’index qui n’impliquent pas la reconstruction d’un index. Si vous supprimez un champ, modifiez un type de données ou renommez un champ, vous devez reconstruire l’index. Pour reconstruire l’index, vous devez supprimer l’index, le recréer et recharger les données.

> [!NOTE]
> Vous pouvez ajouter de nouveaux champs à un index Recherche Azure sans reconstruire l’index. La valeur du nouveau champ sera Null pour tous les documents déjà présents dans l’index.

Pour maintenir la disponibilité de l’index pendant une reconstruction, vous devez disposer d’une copie de l’index avec un nom différent sur le même service ou d’une copie de l’index de même nom sur un autre service, et fournir la redirection ou la logique de basculement dans votre code.

## <a name="disaster-recovery"></a>Récupération d'urgence
Il n'existe actuellement aucun mécanisme intégré de récupération d'urgence. L’ajout de partitions ou de réplicas ne vous permettra pas d’atteindre les objectifs de récupération d'urgence qui ont été fixés. L’approche la plus courante consiste à intégrer la redondance au niveau du service en configurant un deuxième service de recherche dans une autre région. Comme avec la disponibilité pendant une reconstruction d’index, la redirection ou la logique de basculement doit provenir de votre code.

## <a name="increase-query-performance-with-replicas"></a>Augmenter les performances des requêtes avec des réplicas
La latence des requêtes vous permet de découvrir si des réplicas supplémentaires doivent être ajoutés. En règle générale, la première étape vers l’amélioration des performances des requêtes consiste à ajouter davantage de cette ressource. Lorsque vous ajoutez des réplicas, les copies supplémentaires de l’index sont mises en ligne pour prendre en charge les charges de travail supérieures de requête et équilibrer la charge des requêtes sur plusieurs réplicas.

Nous ne pouvons fournir aucune estimation sur les requêtes par seconde (RPS) : les performances des requêtes dépendent de la complexité de la requête et des charges de travail concurrentes. Bien que l’ajout de réplicas entraîne clairement une amélioration de l’évolutivité et des performances, le résultat final n’est pas strictement linéaire : si vous ajoutez trois réplicas, le débit n’est pas forcément multiplié par trois.

Pour obtenir de l’aide sur l’estimation des requêtes par seconde pour vos charges de travail, consultez [Considérations sur les performances et l’optimisation de Recherche Azure](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Améliorer les performances d’indexation avec des partitions
Les applications de recherche nécessitant une actualisation des données en temps réel ou presque ont proportionnellement besoin de plus de partitions que de réplicas. L’ajout de partitions répartit les opérations de lecture/écriture sur un plus grand nombre de ressources de calcul. Il vous offre également davantage d’espace disque pour stocker des documents et des index supplémentaires.

Plus les index sont grands, plus ils sont longs à interroger. Par conséquent, peut-être constaterez-vous que chaque augmentation incrémentielle des partitions nécessite une augmentation plus faible mais proportionnelle des réplicas. La complexité et le volume de vos requêtes auront une incidence sur la vitesse d’exécution des requêtes.

## <a name="basic-tier-partition-and-replica-combinations"></a>Niveau de base : combinaisons de partitions et de réplicas
Un service basique peut avoir exactement une partition et jusqu’à trois réplicas, pour une limite maximale de trois unités de recherche. Les seules ressources ajustables sont les réplicas. Vous devez disposer d’au moins 2 réplicas pour la haute disponibilité sur des requêtes.

<a id="chart"></a>

## <a name="standard-tiers-partition-and-replica-combinations"></a>Niveaux Standard : combinaisons de partitions et de réplicas
Ce tableau indique les unités de recherche requises pour prendre en charge les combinaisons de réplicas et de partitions soumises à la limite de 36 unités de recherche (SU), pour tous les niveaux standard.

|   | **1 partition** | **2 partitions** | **3 partitions** | **4 partitions** | **6 partitions** | **12 partitions** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 unité de recherche |2 unités de recherche |3 unités de recherche |4 unités de recherche |6 unités de recherche |12 unités de recherche |
| **2 réplicas** |2 unités de recherche |4 unités de recherche |6 unités de recherche |8 unités de recherche |12 unités de recherche |24 unités de recherche |
| **3 réplicas** |3 unités de recherche |6 unités de recherche |9 unités de recherche |12 unités de recherche |18 unités de recherche |36 unités de recherche |
| **4 réplicas** |4 unités de recherche |8 unités de recherche |12 unités de recherche |16 unités de recherche |24 unités de recherche |N/A |
| **5 réplicas** |5 unités de recherche |10 unités de recherche |15 unités de recherche |20 unités de recherche |30 unités de recherche |N/A |
| **6 réplicas** |6 unités de recherche |12 unités de recherche |18 unités de recherche |24 unités de recherche |36 unités de recherche |N/A |
| **12 réplicas** |12 unités de recherche |24 unités de recherche |36 unités de recherche |N/A |N/A |N/A |

Les unités de recherche, leur tarification et leur capacité sont détaillées sur le site web Azure. Pour plus d'informations, consultez la rubrique [Tarification](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Le nombre de réplicas et de partitions est divisible par 12 de manière égale (plus précisément, 1, 2, 3, 4, 6, 12). Azure Search divise au préalable chaque index en 12 partitions pour que celles-ci puissent être réparties équitablement sur plusieurs partitions. Par exemple, si votre service comporte trois partitions et que vous créez un index, chaque partition contiendra quatre partitions de l'index. Le partitionnement d’un index réalisé par la Recherche Azure est un détail d'implémentation susceptible d’être modifié dans des futures versions. Le nombre de partitions (12 à l’heure actuelle) peut être, à l’avenir, totalement différent.
>
>

## <a name="billing-formula-for-replica-and-partition-resources"></a>Formule de facturation des ressources de type réplica et partition
La formule de calcul du nombre de SU utilisées pour des combinaisons données est le produit des réplicas et des partitions, soit (R X P = SU). Par exemple, le produit de trois réplicas par trois partitions est facturé comme neuf SU.

Le coût par SU est déterminé par le niveau, avec un taux de facturation inférieur par unité pour le niveau de base par rapport au niveau standard. Consultez la [Tarification](https://azure.microsoft.com/pricing/details/search/)pour connaître les coûts pour chaque niveau.
