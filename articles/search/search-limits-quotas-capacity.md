---
title: Limites de service d’Azure Search | Microsoft Docs
description: Limites de service permettant de planifier la capacité et limites maximales des requêtes et réponses de la Recherche Azure.
services: search
documentationcenter: ''
author: HeidiSteen
manager: jhubbard
editor: ''
tags: azure-portal
ms.assetid: 857a8606-c1bf-48f1-8758-8032bbe220ad
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/26/2018
ms.author: heidist
ms.openlocfilehash: fb2234e79e8deb98a94068f31a40c8f0b415d7ba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="service-limits-in-azure-search"></a>Limites de service d’Azure Search
Les limites maximales de stockage, de charges de travail et de quantités d’index, de documents et d’autres objets dépendent du niveau tarifaire (**Gratuit**, **De base** ou **Standard**) de la [Recherche Azure](search-create-service-portal.md).

* **Gratuit** est un service partagé multi-locataire qui est fourni avec votre abonnement Azure. 
* Le niveau **De base** fournit des ressources informatiques dédiées aux charges de production à petite échelle.
* Le niveau **Standard** est exécuté sur des ordinateurs dédiés, avec une capacité de stockage et de traitement beaucoup plus grande, et ce, à chaque niveau. Le niveau Standard apparaît dans : S1, S2, S3 et S3 Haute densité (S3 HD).

> [!NOTE]
> Un service est approvisionné à un niveau spécifique. Si vous avez besoin de passer au niveau supérieur pour obtenir plus de capacité, vous devez provisionner un nouveau service (la mise à niveau sur place n’est pas disponible). Pour en savoir plus, consultez [Choisir une référence (SKU) ou un niveau tarifaire](search-sku-tier.md). Pour en savoir plus sur le réglage de capacité dans un service que vous avez déjà approvisionné, consultez [Mettre à l’échelle des niveaux de ressources pour interroger et indexer les charges de travail](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Limites d’abonnement
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="service-limits"></a>Limites du service
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

## <a name="index-limits"></a>Limites d’index

| Ressource | Gratuit | De base | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Nombre maximal de champs par index |1 000 |100 <sup>1</sup> |1 000 |1 000 |1 000 |1 000 |
| Nombre maximal de profils de score par index |100 |100 |100 |100 |100 |100 |
| Nombre maximal de fonctions par profil |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Le niveau de base est la seule référence soumise à une limite inférieure de 100 champs par index.

## <a name="indexer-limits"></a>Limites de l’indexeur

| Ressource | Gratuit | De base | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Charge d’indexation maximale par appel |10 000 documents |Limité uniquement par le nombre maximal de documents |Limité uniquement par le nombre maximal de documents |Limité uniquement par le nombre maximal de documents |Limité uniquement par le nombre maximal de documents |N/A <sup>1</sup> |
| Durée maximale d’exécution | 1-3 minutes <sup>2</sup> |24 heures |24 heures |24 heures |24 heures |N/A <sup>1</sup> |
| Indexeur d’objets blob : taille maximale des objets blob, en Mo |16 |16 |128 |256 |256 |N/A <sup>1</sup> |
| Indexeur d’objets blob : nombre maximal de caractères du contenu extrait d’un objet blob |32 000 |64 000 |4 millions |4 millions |4 millions |N/A <sup>1</sup> |

<sup>1</sup> S3 HD ne prend actuellement pas en charge les indexeurs. Contactez le support Azure si vous avez un besoin urgent de cette fonctionnalité.

<sup>2</sup> La durée d’exécution maximale de l’indexeur pour le niveau Gratuit est de 3 minutes pour les sources d’objets blob, et de 1 minute pour toutes les autres sources de données.


## <a name="document-size-limits"></a>Limites de taille des documents
| Ressource | Gratuit | De base | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Taille de chaque document par API d’index |< 16 Mo |< 16 Mo |< 16 Mo |< 16 Mo |< 16 Mo |< 16 Mo |

Indique la taille maximum du document lors de l’appel d’une API d’index. La taille du document est en fait une limite de taille du corps de requête de l’API d’index. Étant donné que vous pouvez transmettre en une seule fois un lot de plusieurs documents à l’API d’index, la limite de taille dépend en fait du nombre de documents dans le lot. Pour un lot comprenant un seul document, la taille maximale du document est de 16 Mo de JSON.

Pour réduire la taille du document, pensez à exclure de la requête les données non requêtables. Les images et autres données binaires ne sont pas directement requêtables et ne doivent pas être stockées dans l’index. Pour intégrer les données non requêtables dans les résultats de la recherche, définissez un champ sans possibilité de recherche qui stocke une référence URL à la ressource.

## <a name="queries-per-second-qps"></a>Requêtes par seconde

Les estimations du nombre de requêtes par seconde doivent être développées indépendamment par chaque client. La taille et la complexité des index et des requêtes ainsi que la quantité de trafic sont les principaux facteurs qui déterminent le nombre de requêtes par seconde. Si ces facteurs sont inconnus, il est impossible d’établir des estimations significatives.

Les estimations sont plus prévisibles si elles sont calculées sur des services qui s’exécutent sur des ressources dédiées (niveaux De base et Standard). Vous pouvez mieux estimer les requêtes par seconde, car vous contrôlez davantage de paramètres. Pour obtenir de l’aide sur la manière d’aborder les estimations, consultez [Performances et optimisation de Recherche Azure](search-performance-optimization.md).

## <a name="api-request-limits"></a>Limites de requête d’API
* 16 Mo maximum par requête <sup>1</sup>
* La longueur maximale d’une URL est de 8 Ko
* 1 000 documents maximum par lot de charges, de fusions ou de suppressions d’index
* 32 champs maximum dans la clause $orderby
* La taille maximale des termes de recherche du texte encodé en UTF-8 est de 32 766 octets (32 Ko moins 2 octets)

<sup>1</sup> Dans la Recherche Azure, le corps d’une requête est soumis à une limite supérieure de 16 Mo. Cela signifie qu’une limite pratique est imposée au contenu des champs individuels ou des collections qui ne font pas l’objet de limites théoriques (pour plus d’informations sur la composition et les restrictions des champs, consultez [Types de données pris en charge](https://msdn.microsoft.com/library/azure/dn798938.aspx)).

## <a name="api-response-limits"></a>Limites de réponse d’API
* 1 000 documents maximum retournés par page de résultats de recherche
* 100 suggestions maximum retournées par requête d’API de suggestion

## <a name="api-key-limits"></a>Limites de clés API
Les clés API sont utilisées pour l'authentification de service. Il existe deux types de clé API. Les clés d’administration sont spécifiées dans l’en-tête de la demande et accordent un accès complet en lecture et en écriture au service. Les clés de requête sont en lecture seule, spécifiées dans l’URL et généralement distribuées aux applications clientes.

* 2 clés administrateur maximum par service
* 50 clés de requête maximum par service
