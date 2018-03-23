---
title: Surveiller le stockage en mémoire XTP | Microsoft Docs
description: Estimer et surveiller la capacité et l’utilisation du stockage en mémoire XTP ; résoudre l’erreur de capacité 41823
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 01/16/2018
ms.author: jodebrui
ms.openlocfilehash: c1adc6e98f7d101a6e5f3227f44b0035d9b9d157
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="monitor-in-memory-oltp-storage"></a>Surveiller le stockage OLTP In-Memory
Lorsque vous utilisez [OLTP en mémoire](sql-database-in-memory.md), les données des tables à mémoire optimisée et les variables de table résident dans un stockage OLTP en mémoire. Chaque niveau de service Premium est doté d’une taille de stockage OLTP en mémoire maximale, qui est décrite dans [Limites de ressources d’une base de données unique](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) et [Limites de ressources d’un pool élastique](sql-database-resource-limits.md#elastic-pool-change-storage-size). Une fois que cette limite est dépassée, des opérations d’insertion et de mise à jour peuvent commencer à échouer en générant l’erreur 41823 pour les bases de données autonomes et l’erreur 41840 pour les pools élastiques. À ce stade, vous devez soit supprimer des données pour libérer de la mémoire, soit mettre à niveau le niveau de performances de votre base de données.

## <a name="determine-whether-data-fits-within-the-in-memory-oltp-storage-cap"></a>Déterminer si la taille des données est adaptée à la capacité de stockage en mémoire OLTP
Déterminer les plafonds de stockage des différents niveaux de service Premium. Consultez [Limites de ressources d’une base de données unique](sql-database-resource-limits.md#single-database-storage-sizes-and-performance-levels) et [Limites de ressources d’un pool élastique](sql-database-resource-limits.md#elastic-pool-change-storage-size).

L’estimation de la mémoire requise pour une table à mémoire optimisée s’effectue de la même façon pour SQL Server que dans Base de données SQL Azure. Prenez quelques minutes pour consulter cet article sur [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

La table et les lignes de variable de table, ainsi que les index, sont pris en compte pour le calcul de la taille maximale des données utilisateur. En outre, l’instruction ALTER TABLE a besoin de suffisamment d’espace pour créer une version de la table entière et de ses index.

## <a name="monitoring-and-alerting"></a>Surveillance et alerte
Vous pouvez surveiller l’utilisation du stockage en mémoire sous forme de pourcentage de la limite maximale de stockage de votre niveau de performances dans le [portail Azure](https://portal.azure.com/) : 

1. Sur le panneau Base de données, recherchez la zone Utilisation de ressources, puis cliquez sur Modifier.
2. Sélectionnez la métrique `In-Memory OLTP Storage percentage`.
3. Pour ajouter une alerte, cliquez sur la zone Taux d’utilisation de la ressource pour ouvrir le panneau Métriques, puis cliquez sur Ajouter une alerte.

Vous pouvez également utiliser la requête suivante pour afficher l’utilisation du stockage en mémoire :

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-in-memory-oltp-storage-situations---errors-41823-and-41840"></a>Corrigez les situations de stockage OLTP en mémoire insuffisant : les erreurs 41823 et 41840
Atteindre le plafond de stockage OLTP en mémoire cause l’échec des opérations de base de données INSERT, UPDATE, ALTER et CREATE avec le message d’erreur 41823 (pour les bases de données autonomes) ou 41840 (pour les pools élastiques). Les deux erreurs provoquent l’abandon de la transaction active.

Les messages d’erreur 41823 et 41840 indiquent que les tables optimisées en mémoire et les variables de table dans la base de données ou le pool ont atteint la taille de stockage OLTP en mémoire maximale.

Pour résoudre cette erreur, deux possibilités s’offrent à vous :

* supprimer des données des tables à mémoire optimisée, en déchargeant potentiellement les données vers des tables traditionnelles sur disque ;
* adapter le niveau de service afin de disposer d’un stockage en mémoire suffisant pour les données que vous devez conserver dans des tables à mémoire optimisée.

> [!NOTE] 
> Dans de rares cas, les erreurs 41823 et 41840 peuvent être temporaires, ce qui signifie qu’il y a suffisamment de stockage OLTP en mémoire disponible, et que l’opération réussit quand elle est relancée. Par conséquent, nous vous recommandons de surveiller le stockage OLTP en mémoire total disponible et de commencer par recommencer l’opération lorsque vous rencontrez des erreurs 41823 ou 41840. Pour plus d’informations sur la logique de nouvelle tentative, consultez [Détection de conflit et logique de nouvelle tentative avec l’OLTP en mémoire](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/transactions-with-memory-optimized-tables#conflict-detection-and-retry-logic).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’instructions sur la surveillance, consultez [Analyse de base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md).
