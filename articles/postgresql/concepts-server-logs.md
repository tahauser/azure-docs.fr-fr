---
title: Journaux de serveur dans Azure Database pour PostgreSQL
description: "Cet article explique comment Azure Database pour PostgreSQL génère les journaux des requêtes et des erreurs et comment la rétention de journal est configurée."
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: a8d560aa8906e3ba1f65758239b645cd1b1df032
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="server-logs-in-azure-database-for-postgresql"></a>Journaux de serveur dans Azure Database pour PostgreSQL 
Azure Database pour PostgreSQL génère des journaux des requêtes et des erreurs. Toutefois, l’accès aux journaux des transactions n’est pas pris en charge. Les journaux des requêtes et des erreurs peuvent être utilisés pour identifier, résoudre et réparer les erreurs de configuration et les problèmes de performances. Pour plus d’informations, consultez la page [Signalement et journalisation des erreurs](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html).

## <a name="access-server-logs"></a>Accéder aux journaux du serveur
Vous pouvez lister et télécharger les journaux des erreurs du serveur PostgreSQL Azure à l’aide du portail Azure, [d’Azure CLI](howto-configure-server-logs-using-cli.md) et des API REST Azure.

## <a name="log-retention"></a>Rétention des journaux
Vous pouvez définir la période de rétention des journaux système en utilisant le paramètre **log\_retention\_period** associé à votre serveur. Ce paramètre a pour unité les jours. La valeur par défaut est de 3 jours. La valeur maximale est de sept jours. Votre serveur doit posséder suffisamment de stockage pour contenir les fichiers journaux conservés.
Les fichiers journaux tournent toutes les heures ou par tranches de 100 Mo, selon la limite atteinte en premier.

## <a name="configure-logging-for-azure-postgresql-server"></a>Configurer la journalisation pour le serveur PostgreSQL Azure
Vous pouvez activer la journalisation des requêtes et des erreurs pour votre serveur. Les journaux d’erreurs peuvent contenir des informations sur le nettoyage automatique, la connexion et les points de contrôle.

Vous pouvez activer la journalisation des requêtes pour votre instance de base de données PostgreSQL en définissant deux paramètres de serveur : `log\_statement` et `log\_min\_duration\_statement`.

Le paramètre **log\_statement** contrôle quelles instructions SQL sont consignées. Nous vous recommandons de définir ce paramètre sur ***all*** pour consigner toutes les instructions ; la valeur par défaut est none.

Le paramètre **log\_min\_duration\_statement** définit la limite en millisecondes pour qu’une instruction soit consignée. Toutes les instructions SQL qui s’exécutent plus longtemps que la valeur du paramètre sont consignées. Par défaut, ce paramètre est désactivé et a la valeur moins 1 (-1). Il peut être utile d’activer ce paramètre pour dépister les requêtes non optimisées dans vos applications.

**log\_min\_messages** permet de contrôler quels niveaux de message sont écrits dans le journal du serveur. La valeur par défaut est WARNING 

Pour plus d’informations sur ces paramètres, consultez la documentation [Signalement et journalisation des erreurs](https://www.postgresql.org/docs/9.6/static/runtime-config-logging.html). En particulier, pour configurer les paramètres du serveur Azure Database pour PostgreSQL, consultez la page [Personnalisation des paramètres de configuration du serveur à l’aide de l’interface de ligne de commande Azure](howto-configure-server-parameters-using-cli.md).

## <a name="next-steps"></a>Étapes suivantes
- Pour accéder aux journaux à l’aide de l’interface de ligne de commande Azure CLI, consultez [Configuration et accès aux journaux du serveur à l’aide de la ligne de commande Azure](howto-configure-server-logs-using-cli.md).
- Pour plus d’informations sur les paramètres du serveur, consultez la rubrique [Personnaliser les paramètres de configuration de serveur à l’aide d’Azure CLI](howto-configure-server-parameters-using-cli.md).
