---
title: "Niveaux de cohérence dans Azure Cosmos DB | Microsoft Docs"
description: "Azure Cosmos DB offre cinq niveaux de cohérence qui permettent de faire des compromis avisés entre cohérence éventuelle, disponibilité et latence."
keywords: "cohérence éventuelle, azure cosmos db, azure, Microsoft Azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: cgronlun
documentationcenter: 
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: mimig
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c3bd28316e3d2e7596021d6964594002d47d160a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Niveaux de cohérence des données paramétrables dans Azure Cosmos DB
Azure Cosmos DB a été conçu dès le départ pour être distribué à l’échelle mondiale. Il offre des garanties de latence faible prévisible et plusieurs modèles de cohérence souples bien définis. Pour le moment, Azure Cosmos DB prend en charge cinq niveaux de cohérence : Fort, Obsolescence limitée, Session, Préfixe cohérent et Éventuel. Les termes « obsolescence limitée », « session », « préfixe cohérent » et « éventuelle » font référence à des modèles de « cohérence souple », qui fournissent un niveau de cohérence inférieur à celui de la cohérence forte (qui constitue le niveau de cohérence le plus élevé). 

Outre les modèles de cohérence **forte** et **éventuelle** souvent proposés par les bases de données distribuées, Azure Cosmos DB propose trois modèles de cohérence supplémentaires soigneusement codifiés et mis en œuvre : **Obsolescence limitée**, **Session** et **Préfixe cohérent**. L’utilité de chacun de ces niveaux de cohérence a été vérifiée à l’aide de cas d’utilisation réels. Ensemble, ces cinq niveaux de cohérence vous permettent de trouver un bon compromis entre cohérence, disponibilité et latence. 

## <a name="distributed-databases-and-consistency"></a>Bases de données distribuées et cohérence
Les bases de données distribuées commerciales se répartissent en deux catégories : les bases de données qui n’offrent pas de choix de cohérence bien définis et démontrables, et celles qui offrent deux possibilités de programmabilité extrêmes (cohérence éventuelle et cohérence forte). 

La première option embrouille les développeurs d’applications en raison des menus détails de leurs protocoles de réplication et les oblige à faire des compromis difficiles entre cohérence, disponibilité, latence et débit. La deuxième option demande de choisir l’un des deux extrêmes. En dépit de l’abondance des études et des propositions concernant plus de 50 modèles de cohérence, la communauté des bases de données distribuées n’a pas été en mesure de commercialiser des niveaux de cohérence allant au-delà de la cohérence forte ou éventuelle. Cosmos DB vous permet aux développeurs de choisir entre cinq modèles de cohérence bien définis couvrant tout l’éventail de cohérences : fort, obsolescence limitée, [session](http://dl.acm.org/citation.cfm?id=383631), préfixe cohérent et éventuel. 

![Azure Cosmos DB offre le choix entre plusieurs modèles de cohérence bien définis (souples)](./media/consistency-levels/five-consistency-levels.png)

Le tableau suivant illustre les garanties spécifiques que chaque niveau de cohérence fournit.
 
**Niveaux de cohérence et garanties**

| Niveau de cohérence | Garanties |
| --- | --- |
| Remarque | Linéarisabilité. Garantit que les lectures retournent la version la plus récente d’un élément.|
| Obsolescence limitée | Préfixe cohérent. Retard des lectures par rapport aux écritures par k préfixes ou un intervalle t |
| session   | Préfixe cohérent. Lectures unitones, écritures unitones, lecture de vos écritures, l’écriture suit les lectures |
| Préfixe cohérent | Les mises à jour retournées sont un préfixe de toutes les mises à jour, sans interruption |
| Eventual (Éventuel)  | Lectures en désordre |

Vous pouvez configurer le niveau de cohérence par défaut de votre compte Cosmos DB (et remplacer ultérieurement la cohérence sur une demande de lecture spécifique). En interne, le niveau de cohérence par défaut s’applique aux données au sein des groupes de partitions qui peuvent s’étendre sur plusieurs régions. Environ 73 % des locataires Azure Cosmos DB utilisent la cohérence de session et 20 % lui préfèrent l’obsolescence limitée. Environ 3 % des clients Azure Cosmos DB essaient différents niveaux de cohérence avant de choisir celui qui convient le mieux à leur application. Seuls 2 % des locataires Azure Cosmos DB modifient les niveaux de cohérence sur demande. 

Dans DB Cosmos, les lectures au niveaux de cohérence session, préfixe cohérent et cohérence éventuelle sont deux fois meilleur marché que les lectures aux niveaux de cohérence fort ou obsolescence limitée. Cosmos DB propose des contrats SLA complets de pointe, incluant des garanties de cohérence en plus de la disponibilité, du débit et de la latence. Azure Cosmos DB utilise un [vérificateur de linéarisabilité](http://dl.acm.org/citation.cfm?id=1806634) qui opère en permanence sur les résultats de télémétrie de service et vous signale ouvertement toutes les violations de cohérence. Pour le niveau de cohérence Obsolescence limitée, Azure Cosmos DB surveille et signale toute violation des limites k et t. Pour les cinq niveaux de cohérence souple, Azure Cosmos DB vous signale directement la [métrique probabiliste d’obsolescence limitée](http://dl.acm.org/citation.cfm?id=2212359).  

## <a name="service-level-agreements"></a>Contrats de niveau de service

Azure Cosmos DB propose des [contrats SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) complets à 99,99 % qui garantissent le débit, la cohérence, la disponibilité et la latence pour les comptes de base de données Azure Cosmos DB limités à une seule région Azure et configurés avec l’un des cinq niveaux de cohérence, ainsi que pour les comptes de base de données couvrant plusieurs régions Azure et qui sont configurés avec l’un des quatre niveaux de cohérence souple. En outre, Azure Cosmos DB propose un contrat SLA à 99,999 % pour la disponibilité de lecture aux comptes de base de données couvrant plusieurs régions Azure, et ce, quel que soit le niveau de cohérence choisi.

## <a name="scope-of-consistency"></a>Portée de la cohérence
La granularité de la cohérence est limitée à la demande d’un utilisateur unique. Une demande d’écriture peut correspondre à une transaction d’insertion, de remplacement, d’upsert ou de suppression. Comme dans le cas des écritures, une transaction de lecture/requête est également limitée à une demande d’utilisateur unique. L’utilisateur peut être amené à paginer un grand jeu de résultats, s’étendant sur plusieurs partitions, mais chaque transaction de lecture se limite à une seule page et est traitée à partir d’une seule partition.

## <a name="consistency-levels"></a>Niveaux de cohérence
Vous pouvez configurer un niveau de cohérence par défaut sur votre compte de base de données, qui s’applique à toutes les collections (et bases de données) sous votre compte Cosmos DB. Par défaut, toutes les lectures et requêtes émises vers les ressources définies par l’utilisateur utilisent le niveau de cohérence par défaut spécifié sur le compte de base de données. Vous pouvez assouplir le niveau de cohérence d’une demande spécifique de lecture/requête donnée dans chacune des API prises en charge. Cinq types de niveaux de cohérence sont pris en charge par le protocole de réplication de d’Azure Cosmos DB. Ils offrent un compromis clair entre les garanties de cohérence spécifiques et les performances, comme décrit dans cette section.

**Remarque**: 

* Une cohérence forte offre une garantie de [linéarisabilité](https://aphyr.com/posts/313-strong-consistency-models) qui permet de s’assurer que les lectures renvoient la version la plus récente d’un élément. 
* la cohérence forte garantit qu'une écriture est visible uniquement après sa validation durable par le quorum majoritaire de réplicas. Une écriture est soit validée durablement de manière synchrone par les quorums principal et secondaire, soit abandonnée. Une lecture est toujours reconnue par le quorum de lecture majoritaire : un client ne voit jamais une écriture partielle ou non validée. Il est assuré de lire la toute dernière écriture reconnue. 
* Les comptes Azure Cosmos DB configurés pour utiliser une cohérence forte ne peuvent pas associer plus d’une région Azure à leur compte.  
* Le coût d’une opération de lecture (en termes [d’unités de requête](request-units.md) consommées) avec une cohérence forte est supérieur à celui des niveaux Session et Éventuel, mais équivalent à celui du niveau Obsolescence limitée.

**Obsolescence limitée**: 

* Le niveau de cohérence obsolescence limitée garantit que les lectures sont retardées derrière les écritures par, au plus, des versions ou préfixes *K* d’un élément ou un intervalle de temps *t*. 
* Par conséquent, lors du choix du niveau de cohérence obsolescence limitée, l’« obsolescence » peut être configurée de deux façons : par le nombre de versions *K* de l’élément par lequel les lectures sont retardées derrière les écritures, et l’intervalle de temps *t* 
* La cohérence de type obsolescence limitée fournit l’ordre global total, en dehors de la « fenêtre d’obsolescence ». Notez que des garanties de lecture unitone existent dans une région à l’intérieur et en dehors de la « fenêtre d’obsolescence ». 
* La cohérence Obsolescence limitée fournit une meilleure garantie de cohérence que les niveaux Session, Préfixe cohérent ou Éventuelle. Pour les applications distribuées à l’échelle mondiale, nous recommandons d’utiliser la cohérence Obsolescence limitée pour les scénarios dans lesquels vous voulez obtenir une cohérence forte en plus d’une disponibilité à 99,99 % et d’une latence faible.   
* Les comptes Azure Cosmos DB configurés avec une cohérence de type obsolescence limitée peuvent associer n’importe quel nombre de régions Azure avec leur compte. 
* Le coût d’une opération de lecture (en termes d’unités de requête consommées) en fonction de l’obsolescence limitée est supérieur à celui des niveaux de cohérence Session et Éventuel, mais identique au niveau de cohérence forte.

**Session**: 

* contrairement aux modèles de cohérence globaux offerts par les niveaux de cohérence Fort et Obsolescence limitée, le niveau Session s’étend à une session client spécifique. 
* La cohérence Session est idéale pour tous les scénarios dans lesquels une session utilisateur ou d’appareil est impliquée, car elle garantit des lectures unitones, des écritures unitones et des garanties de lecture de vos propres écritures. 
* Ce niveau fournit une cohérence prévisible pour une session, et un débit de lecture maximal en offrant la latence d’écriture et de lecture la plus basse. 
* Les comptes Azure Cosmos DB configurés avec une cohérence de type session peuvent associer n’importe quel nombre de régions Azure avec leur compte. 
* Le coût d’une opération de lecture (en unités de requête consommées) avec un niveau de cohérence Session est inférieur à celui des niveaux Forte et Obsolescence limitée, mais supérieur à celui du niveau Éventuelle.

<a id="consistent-prefix"></a>
**Préfixe cohérent** : 

* Le niveau de cohérence préfixe cohérent garantit qu’en l’absence d’autres écritures, les réplicas du groupe finissent par converger. 
* Le niveau de cohérence préfixe cohérent garantit que les lectures ne voient jamais d’écritures dans le désordre. Si les écritures ont été effectuées dans l’ordre `A, B, C`, un client voit `A`, `A,B` ou `A,B,C`, mais jamais dans le désordre comme `A,C` ou `B,A,C`.
* Les comptes Azure Cosmos DB configurés avec une cohérence de type préfixe cohérent peuvent associer n’importe quel nombre de régions Azure avec leur compte. 

**Eventual (Éventuel)**: 

* Le niveau de cohérence Éventuel garantit qu’en l’absence d’autres écritures, les réplicas du groupe finissent par converger. 
* Il s’agit de la forme de cohérence la plus faible qui permet à un client d’obtenir des valeurs plus anciennes que celles qu’il a pu voir précédemment.
* Le niveau Éventuel fournit la cohérence la plus faible en matière de lecture, tout en offrant la latence la moins élevée pour les lectures et les écritures.
* Les comptes Azure Cosmos DB configurés avec une cohérence éventuelle peuvent associer n’importe quel nombre de régions Azure avec leur compte. 
* Le coût d’une opération de lecture (en termes d’unités de requête consommées) avec un niveau de cohérence éventuelle est le plus bas de tous les niveaux de cohérence d’Azure Cosmos DB.

## <a name="configuring-the-default-consistency-level"></a>Configuration du niveau de cohérence par défaut
1. Dans la barre de lancement du [portail Azure](https://portal.azure.com/), cliquez sur **Azure Cosmos DB**.
2. Dans la page **Azure Cosmos DB**, sélectionnez le compte de base de données à modifier.
3. Dans la page du compte, cliquez sur **Cohérence par défaut**.
4. Dans la page **Cohérence par défaut**, sélectionnez le nouveau niveau de cohérence, puis cliquez sur **Enregistrer**.
   
    ![Capture d’écran montrant l’icône Paramètres et l’entrée Cohérence par défaut](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Niveaux de cohérence des requêtes
Par défaut, pour les ressources définies par l’utilisateur, le niveau de cohérence des requêtes est identique à celui des lectures. Par défaut, l’index est mis à jour de manière synchrone lors de chaque insertion, remplacement ou suppression d’un élément dans le conteneur Cosmos DB. Cela permet aux requêtes d’offrir le même niveau de cohérence que celui des lectures de point. Si Azure Cosmos DB est optimisé pour les écritures et prend en charge des volumes soutenus d’écritures, la maintenance d’index synchrone et les requêtes cohérentes, vous pouvez configurer certaines collections de manière à ce que la mise à jour de l’index soit effectuée en différé. Ce processus permet d'optimiser encore plus les performances des écritures. Il est idéal pour les scénarios d'ingestion en bloc lorsqu'une charge de travail implique principalement des lectures.  

| Mode d'indexation | Lectures | Requêtes |
| --- | --- | --- |
| Cohérence (par défaut) |Choisir parmi Fort, Obsolescence limitée, Session, Préfixe cohérent et Éventuel |Choisir parmi Fort, Obsolescence limitée, Session et Éventuel |
| Différé |Choisir parmi Fort, Obsolescence limitée, Session, Préfixe cohérent et Éventuel |Eventual (Éventuel) |
| Aucun |Choisir parmi Fort, Obsolescence limitée, Session, Préfixe cohérent et Éventuel |Non applicable |

Comme pour les demandes de lecture, vous pouvez réduire le niveau de cohérence d’une demande de requête spécifique dans chaque API.

## <a name="consistency-levels-for-the-mongodb-api"></a>Niveaux de cohérence pour l’API MongoDB

Azure Cosmos DB implémente actuellement MongoDB version 3.4, qui a deux paramètres de cohérence, forte et éventuelle. Azure Cosmos DB utilisant plusieurs API, les paramètres de cohérence sont applicables au niveau du compte, et l’application de la cohérence est contrôlée par chaque API.  Jusqu’à MongoDB 3.6, il n’y avait aucun concept de cohérence de session. Ainsi, si vous configurez un compte d’API MongoDB pour utiliser la cohérence de session, la cohérence est rétrogradée à éventuelle lorsque vous utilisez des API MongoDB. Si vous avez besoin d’une garantie de lecture de votre propre écriture pour un compte d’API MongoDB, le niveau de cohérence par défaut pour le compte doit être défini sur obsolescence forte ou limitée.

## <a name="next-steps"></a>Étapes suivantes
Si vous souhaitez en lire plus sur les niveaux de cohérence et les différents compromis, nous vous recommandons les ressources suivantes :

* Doug Terry. La cohérence des données répliquées expliquée par le baseball (vidéo).   
  [https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
* Doug Terry. La cohérence des données répliquées basée sur l’exemple du baseball.   
  [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* Doug Terry. Le niveau Par session garantit des données répliquées peu cohérentes.   
  [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
* Daniel Abadi. Cohérence des compromis en termes de conception de systèmes de base de données distribuée moderne : CAP n’est qu’une partie de l’histoire.   
  [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilités en fonction de l'obsolescence (PBS) pour les quorums partiels pratiques   
  [http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* Werner Vogels. Niveau de cohérence Éventuel repensé.    
  [http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* Moni Naor , Avishai Wool, The Load, Capacity, and Availability of Quorum Systems, SIAM Journal on Computing, v.27 n.2, p.423-447, avril 1998.
  [http://epubs.siam.org/doi/abs/10.1137/S0097539795281232](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* Sebastian Burckhardt, Chris Dern, Macanal Musuvathi, Roy Tan, Line-up : a complete and automatic linearizability checker, Proceedings of the 2010 ACM SIGPLAN conference on Programming language design and implementation, 05-10 juin 2010, Toronto, Ontario, Canada  [doi>10.1145/1806596.1806634] [http://dl.acm.org/citation.cfm?id=1806634](http://dl.acm.org/citation.cfm?id=1806634)
* Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica, Probabilistically bounded staleness for practical partial quorums, Proceedings of the VLDB Endowment, v.5 n.8, p.776-787, avril 2012 [http://dl.acm.org/citation.cfm?id=2212359](http://dl.acm.org/citation.cfm?id=2212359)
