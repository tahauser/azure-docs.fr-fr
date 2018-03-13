---
title: "Azure Cosmos DB comme magasin de valeurs de clés – Synthèse des coûts | Microsoft Docs"
description: "Découvrez le faible coût qu’implique l’utilisation d’Azure Cosmos DB comme magasin de valeurs de clés."
keywords: "magasin de valeurs de clés"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: e6f87cd82ebe31965fcaac1f66d2df03fd58294f
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB comme magasin de valeurs de clés – Synthèse des coûts

Azure Cosmos DB est un service de base de données multimodèle, distribué dans le monde entier, qui permet de créer facilement des applications à grande échelle et à haute disponibilité. Par défaut, Azure Cosmos DB indexe automatiquement et efficacement toutes les données qu’il reçoit. Vous pouvez ainsi créer des requêtes [SQL](sql-api-sql-query.md) (et [JavaScript](programming.md)) rapides et cohérentes sur n’importe quel type de données. 

Cet article décrit le coût qu’implique l’utilisation d’Azure Cosmos DB pour de simples opérations de lecture et d’écriture lorsqu’il est utilisé comme magasin de valeurs de clés. Les opérations d’écriture incluent des insertions, des remplacements, des suppressions et des upserts de documents. En plus de la garantie d’un contrat SLA de disponibilité à 99,99 % pour tous les comptes à une et plusieurs régions avec cohérence souple, et de la disponibilité de lecture de 99,999 % pour tous les comptes de base de données couvrant plusieurs régions, Azure Cosmos DB garantit une latence inférieure à 10 ms pour les lectures et une latence inférieure à 15 ms pour les écritures (indexées), au 99e centile. 

## <a name="why-we-use-request-units-rus"></a>Pourquoi utiliser des unités de requête (RU) ?

Les performances d’Azure Cosmos DB sont basées sur la quantité d’[unités de requête](request-units.md) approvisionnées pour la partition. L’approvisionnement se fait avec une granularité d’une seconde et s’achète par unité de requête par seconde ([à ne pas confondre avec la tarification horaire](https://azure.microsoft.com/pricing/details/cosmos-db/)). Les unités de requête doivent être considérées comme une devise qui simplifie l’approvisionnement du débit dont a besoin l’application. Les utilisateurs n’ont donc plus à différencier les unités de capacité des opérations de lecture et d’écriture. Le modèle de devise unique des unités de requête permet de partager efficacement la capacité approvisionnée entre les opérations de lecture et d’écriture. Ce modèle de capacité approvisionnée permet au service de fournir un débit prévisible et constant, une faible latence garantie et une haute disponibilité. Enfin, les unités de requête sont utilisées pour modéliser le débit. Toutefois, chaque unité de requête provisionnée a également une quantité définie de ressources (mémoire, cœurs). Les unités de requête par seconde ne correspondent pas uniquement à des opérations d’E/S par seconde.

En tant que système de base de données distribué globalement, Azure Cosmos DB est le seul service Azure à fournir un contrat SLA sur la latence, le débit et la cohérence, en plus de la haute disponibilité. Le débit que vous approvisionnez est appliqué à chacune des régions associées à votre compte de base de données Azure Cosmos DB. Pour les opérations de lecture, Azure Cosmos DB propose plusieurs [niveaux de cohérence](consistency-levels.md) bien définis. 

Le tableau suivant indique le nombre d’unités de requête nécessaires pour lire et écrire des transactions en fonction de la taille des documents de 1 Ko et 100 Ko.

|Taille de l’élément|1 lecture|1 écriture|
|-------------|------|-------|
|1 Ko|1 unité de requête|5 unités de requête|
|100 Ko|10 unités de requête|50 unités de requête|

## <a name="cost-of-reads-and-writes"></a>Coût des lectures et des écritures

Si vous provisionnez 1 000 unités de requête par seconde, vous obtenez un total de 3,6 millions d’unités de requête par heure, à un coût de 0,08 dollar l’heure (aux États-Unis et en Europe). Pour un document d’une taille de 1 Ko, vous pouvez donc consommer 3,6 millions de lectures ou 0,72 million d’écritures (3,6 millions d’unités de requête/5) en utilisant le débit provisionné. Normalisé au million de lectures et d’écritures, le coût serait de 0,022 $/million de lectures (0,08 $/3,6) et de 0,111 $/million d’écritures (0,08 $/0,72). Le coût par million devient minime, comme le montre le tableau ci-dessous.

|Taille de l’élément|1 million de lectures|1 million d’écritures|
|-------------|-------|--------|
|1 Ko|0,022 $|0,111 $|
|100 Ko|0,222 $|1,111 $|


La plupart des magasins d’objets et d’objets Blob de base facturent 0,40 $ le million de transactions de lecture et 5 $ le million de transactions d’écriture. S’il est utilisé de manière optimale, Cosmos DB peut être jusqu’à 98 % moins cher que les autres solutions (pour les transactions de 1 Ko).

## <a name="next-steps"></a>Étapes suivantes

Consultez régulièrement cette rubrique pour obtenir de nouveaux articles sur l’optimisation de l’approvisionnement des ressources Azure Cosmos DB. En attendant, n’hésitez pas à utiliser notre [calculatrice d’unités de requête](https://www.documentdb.com/capacityplanner).

