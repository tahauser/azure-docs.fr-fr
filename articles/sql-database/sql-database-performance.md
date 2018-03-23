---
title: Surveiller et améliorer les performances - Azure SQL Database | Microsoft Docs
description: La base de données SQL Azure fournit des outils de performances pour vous aider à identifier les zones susceptibles d’améliorer les performances actuelles des requêtes.
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 07/19/2016
ms.author: sstein
ms.openlocfilehash: 1d327e33a84917688e70f234df311e7b7cf4b30b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="monitor-and-improve-performance"></a>Surveiller et améliorer les performances
Azure SQL Database identifie les problèmes potentiels de votre base de données et recommande les actions susceptibles d’améliorer les performances de votre charge de travail en fournissant des recommandations et des actions de réglage intelligent.

Pour vérifier les performances de votre base de données, utilisez la vignette **Performances** de la page Présentation ou accédez à la section « Support + dépannage » :

   ![Afficher les performances](./media/sql-database-performance/entries.png)

Dans la section « Support + dépannage », vous pouvez utiliser les pages suivantes :


1. [Vue d’ensemble des performances](#performance-overview) pour surveiller les performances de votre base de données ; 
2. [Recommandations sur les performances](#performance-recommendations) pour trouver des recommandations en termes de performances pouvant améliorer les performances de votre charge de travail ;
3. [Analyse des performances des requêtes](#query-performance-insight) pour rechercher les principales requêtes consommatrices de ressources ;
4. [Paramétrage automatique](#automatic-tuning) pour permettre à Azure SQL Database d’optimiser automatiquement votre base de données.

## <a name="performance-overview"></a>Vue d'ensemble des performances
Cette vue fournit un résumé des performances de votre base de données et vous aide à optimiser les performances et à résoudre les problèmes. 

![Performances](./media/sql-database-performance/performance.png)

* La vignette **Recommandations** fournit une répartition des recommandations de paramétrage de votre base de données (les trois premières recommandations sont affichées s’il en existe plus). La page **[Recommandations sur les performances](#performance-recommendations)** s’affiche si vous cliquez sur cette vignette. 
* La vignette **Activité de paramétrage** fournit un résumé des actions de paramétrage en cours et terminées pour votre base de données, ce qui vous donne un aperçu rapide de l’historique des activités de paramétrage. Cliquez sur cette vignette pour afficher l'historique de paramétrage complet de votre base de données.
* La vignette **Paramétrage automatique** affiche la [configuration de paramétrage automatique](sql-database-automatic-tuning-enable.md) de votre base de données (options de paramétrage qui sont automatiquement appliquées à votre base de données). Cliquez sur cette vignette pour ouvrir la boîte de dialogue de configuration de l'automatisation.
* La vignette **Requêtes de base de données** affiche le résumé des performances de requête de votre base de données (utilisation DTU globale et requêtes les plus consommatrices de ressources). La page **[Analyse des performances des requêtes](#query-performance-insight)** s’affiche si vous cliquez sur cette vignette.

## <a name="performance-recommendations"></a>Recommandations en matière de performances
Cette page fournit des [recommandations de réglage intelligent](sql-database-advisor.md) qui peuvent améliorer les performances de votre base de données. Les types de recommandations suivants sont affichés sur cette page :

* Recommandations sur les index à créer ou à supprimer.
* Recommandations en cas de problèmes de schéma dans la base de données.
* Recommandations quand les requêtes peuvent tirer parti des requêtes paramétrables.

![Performances](./media/sql-database-performance/recommendations.png)

Vous trouverez également un historique complet des actions de réglage qui ont été appliquées dans le passé.

Découvrez comment rechercher et appliquer les recommandations relatives aux performances dans l’article [Rechercher et appliquer les recommandations en matière de performances](sql-database-advisor-portal.md).

## <a name="automatic-tuning"></a>Réglage automatique
Azure SQL Database peut régler automatiquement les performances de base de données en appliquant les [recommandations relatives aux performances](sql-database-advisor.md). Pour en savoir plus, voir l’article [Automatic tuning (Réglage automatique)](sql-database-automatic-tuning.md). Pour l’activer, lire [How to enable automatic tuning (Activation du réglage automatique)](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](sql-database-query-performance.md) vous permet de passer moins de temps à résoudre les problèmes de performances des bases de données en fournissant :

* Une meilleure compréhension de la consommation des ressources des bases de données (DTU). 
* Les requêtes principales consommatrices d’UC peuvent être réglées pour améliorer les performances. 
* La possibilité d’examiner les détails d’une requête. 

  ![tableau de bord des performances](./media/sql-database-query-performance/performance.png)

Pour plus d’informations sur cette page, voir l’article **[How to use Query Performance Insight (Utilisation de Query Performance Insight)](sql-database-query-performance.md)**.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Guide des performances de base de données SQL Azure pour les bases de données uniques](sql-database-performance-guidance.md)
* [Quand utiliser un pool élastique ?](sql-database-elastic-pool-guidance.md)

