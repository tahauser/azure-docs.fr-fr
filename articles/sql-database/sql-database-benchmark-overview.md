---
title: Vue d’ensemble du test d’évaluation de la base de données SQL Azure
description: Cette rubrique décrit le test d’évaluation de la base de données SQL Azure utilisé pour mesurer les performances de la base de données SQL Azure.
services: sql-database
author: jan-eng
manager: jhubbard
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 06/21/2016
ms.author: janeng
ms.openlocfilehash: 25685f663d976674ad877bcfa1409ef016dd02ae
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-sql-database-benchmark-overview"></a>Vue d’ensemble du test d’évaluation de la base de données SQL Azure
## <a name="overview"></a>Vue d'ensemble
La base de données SQL Microsoft Azure propose trois [niveaux de service](sql-database-service-tiers.md) associés à plusieurs niveaux de performance. Chaque niveau de performance permet de gagner en ressources, c’est-à-dire en puissance, pour générer à chaque fois un meilleur débit.

Il est important de pouvoir évaluer dans quelle mesure le gain de puissance de chaque niveau de performance se répercute sur les performances de la base de données. C’est pourquoi Microsoft a développé le test d’évaluation de la base de données Azure (ASDB, Azure SQL Database Benchmark). Le test d’évaluation combine plusieurs opérations de base disponibles dans toutes les charges de travail OLTP. Nous évaluons le débit atteint avec les bases de données exécutées à chaque niveau de performance.

Les ressources et la puissance de chaque niveau de service et de performances sont exprimées en termes [d’unités de transaction de base de données (DTU)](sql-database-what-is-a-dtu.md). Ces unités permettent de décrire la capacité relative d’un niveau de performance à partir du processeur, de la mémoire et des taux de lecture et d’écriture associés à chaque niveau de performance. Le fait de doubler la notation en DTU d’une base de données revient à multiplier par deux la puissance de la base de données. Le test d’évaluation nous permet de déterminer dans quelle mesure la puissance croissante offerte par chaque niveau de performance affecte les performances de la base de données. Il consiste à mesurer les opérations réelles de la base de données, en adaptant sa taille, le nombre d’utilisateurs et les taux de transaction compte tenu des ressources disponibles.

En exprimant le débit de la couche de service De base en termes de transactions par heure, le niveau de service Standard en transactions par minute et la couche de service Premium en transactions par seconde, ce test permet de corréler rapidement les performances potentielles de chaque niveau de service aux besoins d’une application.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Mise en corrélation des résultats du test d’évaluation avec les performances réelles de la base de données
Il est important de comprendre que le test ASDB, comme tous les tests d’évaluation, fournit uniquement des résultats représentatifs et indicatifs. Les taux de transaction obtenus avec l’application d’évaluation ne seront pas les mêmes que ceux qui peuvent être atteints avec d’autres applications. Le test d’évaluation comprend un ensemble de différents types de transactions qui s’exécutent sur un schéma contenant une plage de tables et de types de données. Si le test d’évaluation exécute les mêmes opérations de base communes à toutes les charges de travail OLTP, il n’a pas vocation à représenter une classe de base de données ou d’application spécifique. L’objectif de ce test est de fournir des indications quant aux performances relatives d’une base de données que l’on peut escompter lors d’un changement de niveau de performance. En réalité, les bases de données varient en termes de taille et de complexité. Elles supportent diverses charges de travail et réagissent de différentes façons. Par exemple, une application gourmande en E/S peut atteindre rapidement les seuils d’E/S, de la même manière qu’une application gourmande en ressources processeur peut atteindre rapidement les limites processeur. Rien ne peut garantir qu’une base de données particulière évoluera de la même façon que le test d’évaluation sous une charge plus importante.

Le test d’évaluation et sa méthodologie sont décrits plus en détail ci-dessous.

## <a name="benchmark-summary"></a>Résumé du test d’évaluation
Le test ASDB mesure les performances d’une série d’opérations de base de données basiques que l’on rencontre le plus fréquemment dans les charges de travail de traitement transactionnel en ligne (OLTP). Bien qu’il ait été conçu pour les environnements cloud, le schéma de base de données, le remplissage des données et les transactions ont été formulés pour représenter les éléments de base couramment utilisés dans les charges de travail OLTP.

## <a name="schema"></a>Schéma
Le schéma a été conçu de façon suffisamment variée et complexe pour prendre en charge un large éventail d’opérations. Le test d’évaluation s’exécute sur une base de données composée de six tables. Les tables se répartissent en trois catégories : taille fixe, extensibles et évolutives. Il existe deux tables de taille fixe, trois tables extensibles et une table évolutive. Les tables de taille fixe comportent un nombre constant de lignes. Les tables extensibles ont une cardinalité proportionnelle aux performances de la base de données, mais qui ne varie pas pendant le test d’évaluation. La table évolutive est dimensionnée à la manière d’une table extensible sur la charge initiale, mais la cardinalité change pendant l’exécution du test d’évaluation à mesure que des lignes sont insérées et supprimées.

Le schéma inclut divers types de données, notamment des entiers, des valeurs numériques, des caractères et des valeurs date/heure. Le schéma inclut des clés primaires et secondaires, mais aucune clé étrangère ; autrement dit, il n’y a pas de contraintes d’intégrité référentielle entre les tables.

Un programme de génération de données génère les données pour la base de données initiale. Les entiers et les données numériques sont générés à l’aide de différentes stratégies. Dans certains cas, les valeurs sont distribuées de façon aléatoire sur une plage. Dans d’autres cas, un ensemble de valeurs est permuté de façon aléatoire pour garantir une distribution spécifique. Les champs de texte sont générés à partir d’une liste pondérée de mots permettant de produire des données réalistes.

La base de données est dimensionnée selon un « facteur d’échelle ». Le facteur d’échelle (« SF ») détermine la cardinalité des tables extensibles et évolutives. Comme décrit dans la section « Utilisateurs et rythme » ci-dessous, la taille de la base de données, le nombre d’utilisateurs et les performances maximales s’adaptent tous au prorata des uns des autres.

## <a name="transactions"></a>Transactions
La charge de travail se compose de neuf types de transactions, comme indiqué dans le tableau ci-dessous. Chaque transaction est conçue pour mettre en évidence un ensemble particulier de caractéristiques système dans le moteur de base de données et le matériel système, en les distinguant clairement des autres transactions. Cette approche permet d’évaluer plus facilement l’impact des différents composants sur les performances globales. Par exemple, la transaction « Read Heavy » génère un nombre important d’opérations de lecture à partir du disque.

| Type de transaction | Description |
| --- | --- |
| Read Lite |SÉLECTION ; dans la mémoire ; lecture seule |
| Read Medium |SÉLECTION ; principalement dans la mémoire ; lecture seule |
| Read Heavy |SÉLECTION ; principalement hors de la mémoire ; lecture seule |
| Update Lite |MISE À JOUR ; dans la mémoire ; lecture-écriture |
| Update Heavy |MISE À JOUR ; principalement hors de la mémoire ; lecture-écriture |
| Insert Lite |INSERTION ; dans la mémoire ; lecture-écriture |
| Insert Heavy |INSERTION ; principalement hors de la mémoire ; lecture-écriture |
| Supprimer |SUPPRESSION ; combinaison de ressources dans la mémoire et hors de la mémoire ; lecture-écriture |
| CPU Heavy |SÉLECTION ; dans la mémoire ; charge UC relativement importante ; lecture seule |

## <a name="workload-mix"></a>Combinaison de charges de travail
Les transactions sont sélectionnées de manière aléatoire à partir d’une distribution pondérée avec la combinaison générale suivante. La combinaison générale présente un ratio lecture/écriture d’environ 2:1.

| Type de transaction | % de la combinaison |
| --- | --- |
| Read Lite |35 |
| Read Medium |20 |
| Read Heavy |5. |
| Update Lite |20 |
| Update Heavy |3 |
| Insert Lite |3 |
| Insert Heavy |2 |
| Supprimer |2 |
| CPU Heavy |10 |

## <a name="users-and-pacing"></a>Utilisateurs et rythme
La charge de travail d’évaluation est pilotée par un outil qui envoie des transactions sur un ensemble de connexions afin de simuler le comportement d’un nombre d’utilisateurs simultanés. Bien que l’ensemble des connexions et transactions soient générées de façon automatique, nous appellerons ici ces connexions « utilisateurs » par souci de commodité. Bien que chaque utilisateur fonctionne indépendamment de tous les autres, tous exécutent le même cycle d’étapes, comme indiqué ci-dessous :

1. Établir une connexion à la base de données.
2. Répéter jusqu’à obtention du signal de sortie :
   * Sélection d’une transaction de façon aléatoire (à partir d’une distribution pondérée)
   * Exécution de la transaction sélectionnée et évaluation du temps de réponse
   * Délai d’attente
3. Fermer la connexion à la base de données.
4. Quitter.

Le délai (à l’étape 2c) est sélectionné au hasard, mais avec une distribution de 1 seconde en moyenne. Chaque utilisateur peut donc, en moyenne, générer au maximum une transaction par seconde.

## <a name="scaling-rules"></a>Règles de mise à l’échelle
Le nombre d’utilisateurs est déterminé par la taille de la base de données (en unités de facteur d’échelle). On compte un seul utilisateur pour cinq unités de facteur d’échelle. En raison du délai, un même utilisateur peut générer, en moyenne, au maximum une transaction par seconde.

Par exemple, une base de données utilisant un facteur d’échelle de 500 (SF = 500) comportera 100 utilisateurs et pourra atteindre un taux maximum de 100 TPS. Pour augmenter le taux de TPS, la base de données doit être plus volumineuse et comporter un plus grand nombre d’utilisateurs.

Le tableau ci-dessous indique le nombre d’utilisateurs effectivement pris en charge pour chaque niveau de service et de performance.

| Niveau de service (niveau de performance) | Utilisateurs | Taille de la base de données |
| --- | --- | --- |
| De base |5. |720 Mo |
| Standard (S0) |10 |1 Go |
| Standard (S1) |20 |2,1 Go |
| Standard (S2) |50 |7,1 Go |
| Premium (P1) |100 |14 Go |
| Premium (P2) |200 |28 Go |
| Premium (P6) |800 |114 Go |

## <a name="measurement-duration"></a>Durée de la mesure
Pour être reconnu valide, un test d’évaluation doit s’effectuer sur une durée de mesure constante d’au moins une heure.

## <a name="metrics"></a>Mesures
Le débit et le temps de réponse constituent les principaux indicateurs du test d’évaluation.

* Le débit est la mesure de performance la plus importante dans ce test d’évaluation. Il est exprimé en transactions par unité de temps et tient compte de tous les types de transactions.
* Le temps de réponse permet de mesurer la prévisibilité des performances. La limite de temps de réponse varie en fonction de la classe de service, les classes de service plus élevées devant satisfaire à des exigences de temps de réponse plus serrées, comme indiqué ci-dessous.

| Classe de service | Mesure du débit | Temps de réponse requis |
| --- | --- | --- |
| Premium |Transactions par seconde |95e centile à 0,5 seconde |
| standard |Transactions par minute |90e centile à 1 seconde |
| De base |Transactions par heure |80e centile à 2 secondes |

## <a name="conclusion"></a>Conclusion
Le test d’évaluation de la base de données SQL Azure mesure les performances relatives de la base de données SQL Azure lorsqu’elle est exécutée sur l’un des différents niveaux de service et de performance disponibles. Ce test simule une série d’opérations de base de données basiques que l’on rencontre le plus fréquemment dans les charges de travail de traitement transactionnel en ligne (OLTP). En mesurant les performances réelles, il évalue l’impact d’un changement de niveau de performance sur le débit de manière plus significative qu’un simple listage des ressources fournies par chaque niveau, telles que la vitesse du processeur, la taille de la mémoire ou le taux d’E/S par seconde. Nous projetons à terme de continuer à faire évoluer le test d’évaluation afin d’en élargir la portée et d’étendre les données obtenues.

## <a name="resources"></a>Ressources
[Présentation de la base de données SQL](sql-database-technical-overview.md)

[Niveaux de service et niveaux de performances](sql-database-service-tiers.md)

[Guide des performances pour les bases de données uniques](sql-database-performance-guidance.md)
