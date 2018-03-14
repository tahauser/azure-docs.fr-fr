---
title: Monitoring dans Azure Database pour MySQL
description: "Cet article décrit les métriques de monitoring et d’alerte pour Azure Database pour MySQL, notamment les statistiques sur l’UC, les limites, le stockage et la connexion."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 7ecfb8151cd81fb588f964fdfa3a74aacab24874
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Monitoring dans Azure Database pour MySQL
Le monitoring des données sur vos serveurs vous permet de résoudre les problèmes et d’optimiser votre charge de travail. Azure Database pour MySQL propose diverses métriques qui fournissent des insights sur le comportement des ressources prenant en charge le serveur MySQL. 

## <a name="metrics"></a>Mesures
Toutes les métriques Azure présentent une fréquence d’une minute et chaque métrique fournit 30 jours d’historique. 

Vous pouvez configurer des alertes basées sur les métriques. Pour obtenir des instructions détaillées, consultez [Configurer des alertes sur les métriques](howto-alert-on-metric.md). 

Les autres tâches incluent la configuration d’actions automatisées, l’exécution d’analyses avancées et l’archivage de l’historique. Pour plus d’informations, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Liste des métriques
Ces métriques sont disponibles pour Azure Database pour MySQL :

|Métrique|Nom d’affichage de la métrique|Unité|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Pourcentage d’UC en cours d’utilisation.|
|compute_limit|Limite d’unités de calcul|Count|Nombre maximal d’unités de calcul de ce serveur.|
|compute_consumption_percent|Pourcentage d’unités de calcul|Pourcentage|Pourcentage d’unités de calcul utilisées par rapport à la limite maximale du serveur.|
|memory_percent|Pourcentage de mémoire|Pourcentage|Pourcentage de mémoire en cours d’utilisation.|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Pourcentage d’E/S en cours d’utilisation.|
|storage_percent|Pourcentage de stockage|Pourcentage|Pourcentage de stockage utilisé par rapport à la limite maximale du serveur.|
|storage_used|Stockage utilisé|Octets|Quantité de stockage en cours d’utilisation. Le stockage utilisé par le service inclut les fichiers de base de données, les journaux des transactions et les journaux du serveur.|
|storage_limit|Limite de stockage|Octets|Stockage maximal pour ce serveur.|
|active_connections|Nombre total de connexions actif|Count|Nombre de connexions actives sur le serveur.|
|connections_failed|Total de connexions ayant échoué|Count|Nombre de connexions au serveur ayant échoué.|


## <a name="next-steps"></a>Étapes suivantes
- Pour des instructions étape par étape, consultez [Configurer des alertes](howto-alert-on-metric.md). 
- Pour plus d’informations sur la façon d’accéder aux métriques et de les exporter à l’aide du portail Azure, de l’API REST ou de CLI, consultez [Vue d’ensemble des métriques Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
