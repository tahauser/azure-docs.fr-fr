---
title: Journaux de serveur pour Azure Database pour MySQL
description: "Décrit les journaux disponibles dans Azure Database pour MySQL et les paramètres disponibles pour l’activation de différents niveaux de journalisation."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ce6b6208b74063ea5d6e9868ca414f833b1a2045
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="server-logs-in-azure-database-for-mysql"></a>Journaux de serveur dans Azure Database pour MySQL
Dans Azure Database pour MySQL, le journal des requêtes lentes est disponible pour les utilisateurs. L’accès aux journaux des transactions n’est pas pris en charge. Le journal des requêtes lentes peut être utilisé pour identifier les goulots d’étranglement en matière de performances, afin de les faire disparaître. 

Pour plus d’informations sur le journal des requêtes lentes MySQL, consultez la [section sur le journal des requêtes lentes](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) du manuel de référence MySQL.

## <a name="access-server-logs"></a>Accéder aux journaux du serveur
Vous pouvez répertorier et télécharger les journaux des serveurs Azure Database pour MySQL à l’aide du portail Azure et de l’interface de ligne de commande Azure.

Dans le portail Azure, sélectionnez votre serveur Azure Database pour MySQL. Sous l’en-tête **Surveillance**, sélectionnez la page **Journaux des serveurs**.

Pour plus d’informations sur l’interface de ligne de commande Azure, consultez [Configuration et accès aux journaux du serveur à l’aide de la ligne de commande Azure](howto-configure-server-logs-in-cli.md).

## <a name="log-retention"></a>Rétention des journaux
Les journaux sont disponibles pendant sept jours à compter de leur création. Si la taille totale des journaux disponibles dépasse 7,5 Go, les fichiers les plus anciens sont supprimés jusqu’à ce que de l’espace soit disponible. 

Une rotation des journaux s’effectue toutes les 24 heures ou une fois les 7,5 Go atteints, selon ce qui se produit en premier.


## <a name="configure-logging"></a>Configuration de la journalisation 
Par défaut, le journal des requêtes lentes est désactivé. Pour l’activer, définissez slow_query_log sur ON.

Les autres paramètres que vous pouvez ajuster incluent :

- **long_query_time** : si une requête dure plus longtemps que long_query_time (en secondes), cette requête est enregistrée. La valeur par défaut est 10 secondes.
- **log_slow_admin_statements** : si ce paramètre est activé, inclut des instructions d’administration telles que ALTER_TABLE et ANALYZE_TABLE dans les instructions écrites dans le journal des requêtes lentes.
- **log_queries_not_using_indexes** : détermine si les requêtes qui n’utilisent pas les index sont enregistrées dans le journal des requêtes lentes.
- **log_throttle_queries_not_using_indexes** : ce paramètre limite le nombre de requêtes hors index qui peuvent être écrites dans le journal des requêtes lentes. Ce paramètre prend effet lorsque log_queries_not_using_indexes est défini sur ON.

Consultez [la documentation MySQL consacrée au journal des requêtes lentes](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) pour obtenir une description complète des paramètres du journal des requêtes lentes.

## <a name="next-steps"></a>Étapes suivantes
- [Comment configurer et accéder aux journaux des serveurs à l’aide de l’interface de ligne de commande Azure](howto-configure-server-logs-in-cli.md).
