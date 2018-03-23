---
title: Utilisation du magasin de requêtes dans la base de données SQL Azure
description: Découvrez comment utiliser le magasin de requêtes dans la base de données SQL Azure
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 11/08/2016
ms.author: bonova
ms.openlocfilehash: f0c3780f6efe87437742af7c1b8f6a3e6d0ee243
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Utilisation du magasin de requêtes dans la base de données SQL Azure
Le magasin de requêtes dans Azure est une fonctionnalité de base de données entièrement gérée qui recueille et présente des informations historiques détaillées sur toutes les requêtes. Le magasin de requêtes peut être comparé à l’enregistreur de données de vol d’un avion, qui simplifie considérablement le dépannage des problèmes de performances des requêtes à la fois pour les clients cloud et pour les clients locaux. Cet article présente certains aspects de l’utilisation du magasin de requêtes dans Azure. À l’aide des données de requête collectées au préalable, vous pouvez diagnostiquer et résoudre rapidement les problèmes de performances, ce qui vous permet de consacrer davantage de temps à vos activités. 

Le magasin de requêtes est [disponible dans le monde entier](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) via la base de données SQL Azure depuis novembre 2015. Le magasin de requêtes constitue la base de l’analyse des performances et de l’ajustement des fonctionnalités, comme [SQL Database Advisor et le tableau de bord des performances](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Au moment de la publication de cet article, le magasin de requêtes est en cours d’exécution sur plus de 200 000 bases de données utilisateur dans Azure, et recueille les informations liées aux requêtes depuis plusieurs mois sans interruption.

> [!IMPORTANT]
> Microsoft met actuellement en place le magasin de requêtes pour toutes les bases de données SQL Azure (existantes et nouvelles). 
> 
> 

## <a name="optimal-query-store-configuration"></a>Configuration optimale du magasin de requêtes
Cette section décrit les paramètres optimaux de configuration par défaut, conçus pour garantir un fonctionnement fiable du magasin de requêtes et des fonctionnalités liées, comme [SQL Database Advisor et le tableau de bord des performances](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). La configuration par défaut est optimisée pour la collecte des données en continu, c’est-à-dire pour passer le moins de temps possible aux états OFF/READ_ONLY.

| Configuration | Description | Default | Commentaire |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Spécifie la limite d’espace de données que le magasin de requêtes peut inclure dans la base de données client |100 |Appliqué aux nouvelles bases de données |
| INTERVAL_LENGTH_MINUTES |Définit la durée pendant laquelle les statistiques d’exécution collectées pour les plans de requête sont agrégées et rendues persistantes. Chaque plan de requête actif dispose au maximum d’une ligne pour une période définie avec cette configuration |60 |Appliqué aux nouvelles bases de données |
| STALE_QUERY_THRESHOLD_DAYS |Stratégie de nettoyage basée sur la durée, et qui contrôle la période de rétention des statistiques d’exécution persistantes et des requêtes inactives |30 |Appliqué aux nouvelles bases de données et aux bases de données ayant la valeur par défaut précédente (367) |
| SIZE_BASED_CLEANUP_MODE |Indique si un nettoyage automatique des données a lieu lorsque la taille des données du magasin de requêtes approche de la limite |AUTO |Appliqué à toutes les bases de données |
| QUERY_CAPTURE_MODE |Indique si toutes les requêtes sont suivies, ou seulement un sous-ensemble de requêtes |AUTO |Appliqué à toutes les bases de données |
| FLUSH_INTERVAL_SECONDS |Indique la durée maximale pendant laquelle les statistiques d’exécution capturées sont conservées dans la mémoire, avant le vidage sur disque |900 |Appliqué aux nouvelles bases de données |
|  | | | |

> [!IMPORTANT]
> Ces paramètres par défaut sont automatiquement appliqués à l’étape finale de l’activation du magasin de requêtes dans toutes les bases de données SQL Azure (voir la remarque importante précédente). Après cela, la base de données SQL Azure ne modifiera pas les valeurs de configuration définies par les clients, à moins qu’elles aient un impact négatif sur les charges de travail principales ou sur la fiabilité des opérations du magasin de requêtes.
> 
> 

Si vous souhaitez conserver vos paramètres personnalisés, utilisez [ALTER DATABASE avec les options du magasin de requêtes](https://msdn.microsoft.com/library/bb522682.aspx) pour rétablir la configuration à l’état précédent. Découvrez les [meilleures pratiques liées au magasin de requêtes](https://msdn.microsoft.com/library/mt604821.aspx) pour savoir comment choisir des paramètres de configuration optimaux.

## <a name="next-steps"></a>Étapes suivantes
[Informations sur les performances des bases de données SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Ressources supplémentaires
Pour plus d’informations, consultez les articles suivants :

* [Un enregistreur de données pour votre base de données](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 
* [Analyse des performances à l’aide du magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx)
* [Scénarios d’utilisation du magasin de requêtes](https://msdn.microsoft.com/library/mt614796.aspx)
* [Analyse des performances à l’aide du magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx) 

