---
title: "Choisir une référence (SKU) ou un niveau tarifaire pour la Recherche Azure | Microsoft Docs"
description: "Azure Search peut être configuré sur ces niveaux de référence (SKU) : Gratuit, De base et Standard, où Standard est disponible dans différentes configurations de ressources et différents niveaux de capacité."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 8d4b7bca-02a5-43ee-b3f8-03551dfb32fd
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/24/2016
ms.author: heidist
ms.openlocfilehash: 781683f27c943e25d5629dd846da357f51c9d4f9
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2017
---
# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Choisir une référence (SKU) ou un niveau tarifaire pour Azure Search
Dans Azure Search, un [service est configuré](search-create-service-portal.md) sur un niveau tarifaire ou une SKU spécifique. Vous avez le choix entre les options suivantes : **Gratuit**, **De base** ou **Standard**, où **Standard** est disponible dans plusieurs configurations et capacités.

Cet article vise à vous aider à choisir une option. Si la capacité d’un niveau tarifaire est trop basse, vous devrez configurer un nouveau service au niveau supérieur, puis recharger vos index. Il n’existe aucune mise à niveau sur place du même service d’une référence (SKU) à un autre.

> [!NOTE]
> Une fois que vous avez choisi un niveau et [configuré un service de recherche](search-create-service-portal.md), vous pouvez augmenter le nombre de réplicas et de partitions au sein du service. Consultez [Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail](search-capacity-planning.md) pour en savoir plus.
>
>

## <a name="how-to-approach-a-pricing-tier-decision"></a>Comment choisir un niveau tarifaire
Dans Azure Search, le niveau détermine la capacité et non la disponibilité des fonctionnalités. De manière générale, les fonctionnalités sont disponibles à chaque niveau, notamment les fonctionnalités préliminaires. La seule exception est que les indexeurs dans S3 HD ne sont pas pris en charge.

> [!TIP]
> Nous vous recommandons de toujours configurer un service **Gratuit** (un par abonnement, sans date d’expiration) afin qu’il soit facilement disponible pour les petits projets. Utilisez le service **Gratuit** à des fins de test et d’évaluation. Créez un deuxième service facturable de niveau **De base** ou **Premium** pour des charges de travail de production ou de test plus importantes.
>
>

La capacité et les coûts d’exécution du service vont de pair. Les informations contenues dans cet article peuvent vous aider à décider quelle référence (SKU) offre le juste équilibre. Mais, pour que ces indications soient utiles, vous avez besoin d’estimations approximatives pour les éléments suivants :

* Nombre et taille des index que vous envisagez de créer.
* Nombre et taille des documents à télécharger.
* Volume de requêtes, en termes de requêtes par seconde (RPS). Pour obtenir de l’aide, consultez [Considérations sur les performances et l’optimisation de Recherche Azure](search-performance-optimization.md).

Le nombre et la taille sont des éléments importants, car les limites maximales sont atteintes par le biais d’une limite stricte sur le nombre d’index par service, ou sur les ressources (stockage ou réplicas) utilisées par le service. La limite réelle de votre service est déterminée par l’élément épuisé en premier (ressources ou objets).

Une fois les estimations connues, les étapes suivantes doivent simplifier le processus :

* **Étape 1** Consulter les descriptions de la référence (SKU) ci-dessous pour découvrir les options disponibles.
* **Étape 2** Répondre aux questions ci-dessous pour arriver à une décision préliminaire.
* **Étape 3** Valider votre décision en passant en revue des limites strictes en matière de stockage et de tarification.

## <a name="sku-descriptions"></a>Descriptions de la référence (SKU)
Le tableau suivant fournit des descriptions de chaque niveau.

| Niveau | Principaux scénarios |
| --- | --- |
| **Gratuit** |Un service partagé, sans frais, utilisé pour l’évaluation, l’investigation ou de petites charges de travail. Dans la mesure où il est partagé avec d’autres abonnés, le débit des requêtes et l’indexation varient en fonction des autres utilisateurs ayant recours au service. Faible capacité (50 Mo ou 3 index contenant jusqu’à 10 000 documents chacun). |
| **De base** |Charges de travail de production de petite taille sur du matériel dédié. Hautement disponible. Capacité maximale de 3 réplicas et 1 partition (2 Go). |
| **S1** |Standard 1 prend en charge les combinaisons flexibles de partitions (12) et de réplicas (12), utilisées pour des charges de travail de taille moyenne sur du matériel dédié. Vous pouvez allouer des partitions et des réplicas dans des combinaisons prises en charge par un nombre maximal de 36 unités de recherche facturables. À ce niveau, les partitions font 25 Go chacune. |
| **S2** |Standard 2 exécute les grandes charges de travail de production à l’aide des mêmes 36 unités de recherche que S1, mais avec des partitions et des réplicas plus importants. À ce niveau, les partitions font 100 Go chacune. |
| **S3** |Standard 3 exécute les charges de production proportionnellement plus importantes sur les systèmes haut de gamme, dans des configurations allant jusqu’à 12 partitions ou 12 réplicas sous 36 unités de recherche. À ce niveau, les partitions font 200 Go chacune. |
| **S3 HD** |Standard 3 haute densité est conçu pour un grand nombre d’index plus petits. Vous pouvez avoir jusqu'à 3 partitions de 200 Go chacune.|

> [!NOTE]
> Les valeurs maximales de réplica et de partition sont facturées en tant qu’unités de recherche (36 unités maximum par service), ce qui impose une limite effective inférieure à ce que la valeur maximale indique au premier abord. Par exemple, pour utiliser le nombre maximal de 12 réplicas, vous pouvez avoir au maximum 3 partitions (3 * 12 = 36 unités). De même, pour utiliser le nombre maximal de partitions, vous devez réduire les réplicas à 3. Pour accéder à un graphique sur les combinaisons autorisées, voir [Mettre à l’échelle les niveaux de ressources pour interroger et indexer les charges de travail dans Azure Search](search-capacity-planning.md) .
>
>

## <a name="review-limits-per-tier"></a>Passer en revue les limites par couche
Le tableau suivant est un sous-ensemble des [Limites de service dans Azure Search](search-limits-quotas-capacity.md). Il répertorie les facteurs susceptibles d’avoir un impact sur le choix de la référence (SKU). Vous pouvez vous référer à ce graphique pour répondre aux questions ci-dessous.

| Ressource | Gratuit | De base | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Contrat de Niveau de Service (SLA) |Nonn <sup>1</sup> |Oui |Oui |Oui |Oui |Oui |
| Limites d’index |3 |5 |50 |200 |200 |1000 <sup>2</sup> |
| Limites du document |10 000 au total |1 million par service |15 millions par partition |60 millions par partition |120 millions par partition |1 million par index |
| Partitions maximales |N/A |1 |12 |12 |12 |3 <sup>2</sup> |
| Taille de partition |50 Mo au total |2 Go par service |25 Go par partition |100 Go par partition (jusqu’à 1,2 To par service) |200 Go par partition (jusqu’à 2,4 To par service) |200 Go (jusqu'à un maximum de 600 Go par service) |
| Réplicas maximales |N/A |3 |12 |12 |12 |12 |

<sup>1</sup> Les fonctionnalités de niveau Gratuit et d’évaluation ne sont pas fournies avec des Contrats de niveau de service (SLA). Pour tous les niveaux facturables, les SLA entrent en vigueur lorsque vous approvisionnez une redondance suffisante pour votre service. Un SLA de requête (lecture) requiert au moins deux réplicas. Un SLA de requête et d’indexation (lecture-écriture) nécessite au moins trois réplicas. Le nombre de partitions n’est pas pris en compte dans les SLA. 

<sup>2</sup> Les niveaux S3 et S3 HD sont soutenus par une infrastructure haute capacité identique, mais chacun d’eux atteint sa limite maximale de différentes façons. S3 permet de cibler un plus petit nombre d’index très volumineux. Par conséquent, sa limite maximale est liée à la ressource (2,4 To pour chaque service). S3 HD cible un grand nombre de très petits index. Avec 1 000 index, S3 HD atteint ses limites sous la forme de contraintes d’index. Si vous êtes un client S3 HD qui nécessite plus de 1 000 index, contactez le Support Microsoft pour plus d’informations sur la marche à suivre.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Éliminer les références qui ne répondent pas aux exigences
Les questions suivantes peuvent vous aider à mieux choisir la référence (SKU) adaptée à votre charge de travail.

1. Avez-vous des exigences en matière de **contrat de niveau de service (SLA)** ? Vous pouvez utiliser n’importe quel niveau facturable (De base et plus), mais vous devez configurer votre service pour assurer la redondance. Un SLA de requête (lecture) requiert au moins deux réplicas. Un SLA de requête et d’indexation (lecture-écriture) nécessite au moins trois réplicas. Le nombre de partitions n’est pas pris en compte dans les SLA.
2. **De combien d’index** avez-vous besoin ? L’un des principaux éléments à prendre en compte lorsque vous choisissez une référence (SKU) est le nombre d’index pris en charge par chaque référence. La prise en charge de l’index est sensiblement différente dans les niveaux de tarification inférieurs. Les exigences en matière de nombre d’index sont déterminantes dans le choix d’une référence (SKU).
3. **Combien de documents** seront chargés dans chaque index ? Le nombre et la taille des documents déterminent la taille finale de l’index. En supposant que vous puissiez estimer la taille prévue de l’index, vous pouvez comparer ce nombre avec la taille de partition par référence (SKU), étendue par le nombre de partitions nécessaires pour stocker un index de cette taille.
4. **Quelle est la charge de requête attendue**? Une fois que les besoins de stockage sont compris, penchez-vous sur les charges de travail de requête. Les références (SKU) S2 et S3 offrent un débit presque équivalent, mais les exigences du contrat SLA écartent toute référence (SKU) de version préliminaire.
5. Si vous envisagez d’utiliser le niveau S2 ou S3, déterminez si vous avez besoin [d’indexeurs](search-indexer-overview.md). Les indexeurs ne sont pas encore disponibles pour le niveau S3 HD. Une autre approche consiste à utiliser un modèle Push pour les mises à jour de l’index, dans lequel vous écrivez le code d’application pour envoyer des notifications Push de jeu de données à un index.

La plupart des clients peuvent choisir ou écarter une référence spécifique en fonction de leurs réponses aux questions ci-dessus. Si encore ne savez pas quelle référence adopter, vous pouvez poser des questions sur les forums MSDN ou StackOverflow ou contacter le Support Azure pour obtenir une assistance.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Décision de validation : la référence (SKU) offre-t-elle suffisamment de stockage et de RPS ?
La dernière étape consiste à revoir la [page de tarification](https://azure.microsoft.com/pricing/details/search/) et les [sections par service et par index dans les limites du service](search-limits-quotas-capacity.md) pour comparer vos estimations aux limites d’abonnement et de service.

Si le prix ou les exigences de stockage sont trop élevés, vous souhaiterez peut-être refactoriser les charges de travail entre plusieurs services plus petits (par exemple). À un niveau plus granulaire, vous pourriez recréer des index plus petits, ou utiliser des filtres pour effectuer des requêtes plus efficaces.

> [!NOTE]
> Les besoins de stockage peuvent être augmentés de façon excessive si les documents contiennent des données superflues. Dans l’idéal, les documents sont constitués uniquement de métadonnées ou de données interrogeables. Les données binaires ne sont pas interrogeables et doivent être stockées séparément (dans une table ou un objet blob de stockage Azure) avec un champ dans l’index pour conserver une référence URL aux données externes. La taille maximale d’un document individuel est de 16 Mo (ou moins si vous chargez en bloc plusieurs documents en une seule demande). Pour plus d’informations, voir [Limites de service d’Azure Search](search-limits-quotas-capacity.md) .
>
>

## <a name="next-step"></a>Étape suivante
Une fois que vous avez déterminé la référence (SKU) qui convient le mieux, poursuivez avec ces étapes :

* [Créer un service de recherche dans le portail](search-create-service-portal.md)
* [Modifier l’allocation des partitions et des réplicas à l’échelle de votre service](search-capacity-planning.md)
