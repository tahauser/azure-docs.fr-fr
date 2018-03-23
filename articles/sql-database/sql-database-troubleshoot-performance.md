---
title: Surveillance et optimisation des performances - Azure SQL Database | Microsoft Docs
description: Conseils pour le réglage des performances dans Azure SQL Database par le biais de l’évaluation et de l’amélioration.
services: sql-database
author: v-shysun
manager: craigg
editor: ''
keywords: réglage des performances de sql, réglage des performances de base de données, conseils pour le réglage des performances de sql, réglage des performances de sql database
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 11/03/2017
ms.author: v-shysun
ms.openlocfilehash: 273a80134975fb9aa4d40b132c1403cc27246371
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="monitoring-and-performance-tuning"></a>Surveillance et optimisation des performances

Azure SQL Database est un service de données flexible et géré automatiquement dans lequel vous pouvez aisément surveiller l’utilisation, ajouter ou supprimer des ressources (processeur, mémoire, E/S), obtenir des recommandations susceptibles d’améliorer les performances de votre base de données ou laisser la base de données s’adapter à votre charge de travail et optimiser automatiquement les performances.

Cet article fournit une vue d’ensemble des options de surveillance et d’optimisation des performances, disponibles dans Azure SQL Database.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="monitoring-and-troubleshooting-database-performance"></a>Surveillance et résolution des problèmes de performances des bases de données

Azure SQL Database vous permet de surveiller facilement votre utilisation des bases de données et d’identifier les requêtes susceptibles d’entraîner des problèmes de performances. Vous pouvez surveiller les performances des bases de données à l’aide du portail Azure ou des vues système. Vous disposez des options suivantes pour la surveillance et la résolution des problèmes de performances de bases de données :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez la base de données, puis utilisez le graphique de surveillance pour rechercher des ressources proches de leur maximum. Consommation de DTU est affichée par défaut. Cliquez sur **Modifier** pour modifier l’intervalle de temps et les valeurs affichées.
2. Utilisez [Query Performance Insight](sql-database-query-performance.md) pour identifier les requêtes utilisant le plus de ressources.
3. Vous pouvez utiliser des vues de gestion dynamique (DMV), des événements étendus (`XEvents`) et le magasin des requêtes dans SSMS pour obtenir des paramètres de performances en temps réel.

Consultez la [rubrique dédiée aux conseils sur les performances](sql-database-performance-guidance.md) pour rechercher les techniques que vous pouvez utiliser afin d’améliorer les performances d’Azure SQL Database si en cas de problème avec ces rapports ou ces vues.

> [!IMPORTANT] 
> Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
>

## <a name="optimize-database-to-improve-performance"></a>Optimiser une base de données pour en améliorer les performances

Azure SQL Database vous permet de déceler les opportunités d’amélioration et d’optimisation des performances des requêtes sans modifier les ressources, et ce, en passant en revue les [recommandations de paramétrage des performances](sql-database-advisor.md). Des index manquants et des requêtes optimisées de manière incorrecte sont souvent à l’origine de performances de base de données limitées. Vous pouvez appliquer ces recommandations de paramétrage pour améliorer les performances de votre charge de travail.
Vous pouvez également laisser Azure SQL Database [optimiser automatiquement les performances de vos requêtes](sql-database-automatic-tuning.md) en appliquant toutes les recommandations identifiées et en vérifiant qu’elles améliorent les performances de la base de données. Vous pouvez utiliser les options suivantes pour améliorer les performances de votre base de données :

1. Utilisez [SQL Database Advisor](sql-database-advisor-portal.md) pour obtenir des recommandations concernant la création et la suppression d’index, le paramétrage des requêtes et la résolution des problèmes de schéma.
2. [Activez le réglage automatique](sql-database-automatic-tuning-enable.md) et laissez Azure SQL Database corriger automatiquement les problèmes de performances identifiés.

## <a name="improving-database-performance-with-more-resources"></a>Amélioration des performances de base de données avec davantage de ressources

Enfin, si aucun élément ne peut être modifié pour améliorer les performances de votre base de données, vous pouvez modifier la quantité de ressources disponibles dans Azure SQL Database. Vous pouvez assigner davantage de ressources en modifiant le [niveau de service](sql-database-service-tiers.md) d’une base de données autonome ou augmenter les unités de débit de base de données élastiques d’un pool élastique à tout moment.
1. Pour les bases de données autonomes, vous pouvez [Modifier les niveaux de service](sql-database-service-tiers.md) à la demande pour améliorer les performances de la base de données.
2. S’il existe plusieurs bases de données, envisagez d’utiliser des [pools élastiques](sql-database-elastic-pool-guidance.md) pour une mise à l’échelle automatique des ressources.

## <a name="tune-and-refactor-application-or-database-code"></a>Régler et refactoriser le code d’une application ou d’une base de données

Vous pouvez modifier le code d’une application pour optimiser l’utilisation de la base de données, modifier des index, forcer des plans ou utiliser des conseils afin d’adapter manuellement la base de données à votre charge de travail. Pour obtenir des directives et des conseils sur le réglage manuel et la réécriture du code, consultez la [rubrique de conseils de performances](sql-database-performance-guidance.md).


## <a name="next-steps"></a>Étapes suivantes

- Pour activer le réglage automatique dans Azure SQL Database et permettre à cette fonction de gérer entièrement votre charge de travail, consultez [Activer le réglage automatique](sql-database-automatic-tuning-enable.md).
- Pour utiliser le réglage manuel, vous pouvez consulter les [recommandations de réglage dans le portail Azure](sql-database-advisor-portal.md) et appliquer manuellement les recommandations qui amélioreront les performances de vos requêtes.
- Modifier les ressources disponibles dans votre base de données en modifiant les [niveaux de service Azure SQL Database](sql-database-performance-guidance.md)
