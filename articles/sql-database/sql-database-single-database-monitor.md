---
title: Surveillance des performances d’une base de données dans Azure SQL Database | Microsoft Docs
description: Découvrez les options d’analyse de votre base de données à l’aide des outils Azure et des vues de gestion dynamique.
keywords: analyse de base de données, performances des bases de données du cloud
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 09/20/2017
ms.author: carlrab
ms.openlocfilehash: ba2239b1a4cd14f7723e88ee83f7ad93da717e0a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Analyse des performances d’une base de données dans une base de données SQL Azure
L’analyse des performances d’une base de données SQL dans Azure démarre par l’analyse de l’utilisation des ressources par rapport au niveau de performances que vous avez choisi pour votre base de données. La surveillance permet de déterminer si votre base de données a une capacité excédentaire ou rencontre des problèmes parce que les ressources ont atteint leur maximum, et ensuite de décider s’il est temps d’ajuster le niveau de performances et le [niveau de service](sql-database-service-tiers.md) de votre base de données. Vous pouvez surveiller votre base de données à l’aide d’outils graphiques dans le [portail Azure](https://portal.azure.com) ou à l’aide de [vues de gestion dynamique](https://msdn.microsoft.com/library/ms188754.aspx) SQL.

> [!TIP]
> Utilisez [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) pour le monitoring automatique des performances de votre base de données. Si un problème de performances est détecté, un journal de diagnostic est généré avec les détails correspondants et une analyse de la cause première du problème. Des recommandations sur l’amélioration des performances sont fournies quand cela est possible.
>

## <a name="monitor-databases-using-the-azure-portal"></a>Analyser des bases de données au moyen du portail Azure
Dans le [portail Azure](https://portal.azure.com/), vous pouvez surveiller l’utilisation d’une base de données unique en sélectionnant votre base de données et en cliquant sur le graphique **Surveillance**. Une fenêtre **Métrique** apparaît. Vous pouvez la modifier en cliquant sur le bouton **Modifier le graphique**. Ajoutez les mesures suivantes :

* Pourcentage UC
* Pourcentage DTU
* Pourcentage E/S des données
* Pourcentage de la taille de la base de données

Une fois que vous avez ajouté ces métriques, vous pouvez continuer à les afficher dans le graphique **Surveillance** avec plus d’informations dans la fenêtre **Métrique**. Les quatre mesures montrent le pourcentage d’utilisation moyen correspondant aux **DTU** de votre base de données. Consultez l’article concernant les [niveaux de service](sql-database-service-tiers.md) pour plus d’informations sur les DTU.

![Surveillance des niveaux de service des performances de la base de données.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

Vous pouvez également configurer des alertes sur les mesures de performances. Cliquez sur le bouton **Ajouter une alerte** situé dans la fenêtre **Métrique**. Suivez l'assistant pour configurer votre alerte. Vous avez la possibilité de configurer une alerte si les mesures dépassent un certain seuil ou si la mesure tombe en dessous d’un certain seuil.

Par exemple, si vous pensez que la charge de travail dans votre base de données va augmenter, vous pouvez choisir de configurer une alerte par courrier électronique chaque fois que votre base de données atteint 80 % de n'importe quelle mesure de performances. Vous pouvez utiliser cette fonction comme un avertissement pour déterminer le moment auquel il se peut que vous deviez basculer vers le niveau de performance supérieur.

Les mesures de performance peuvent également vous aider à déterminer si vous pouvez passer à un niveau inférieur. Supposons que vous utilisez une base de données standard S2 et que toutes les mesures de performance indiquent que la base de données n'utilise pas plus de 10 % des performances en moyenne. Cette base de données fonctionnerait très bien en version S1 standard. Toutefois, prenez en considération les éventuels pics ou baisses de charges de travail avant de décider de passer à un niveau de performances inférieur.

## <a name="monitor-databases-using-dmvs"></a>Analyser des bases de données à l’aide des vues de gestion dynamique
Les métriques exposées dans le portail sont également disponibles dans des vues système : [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) dans la base de données **master** logique de votre serveur et [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dans la base de données utilisateur. Utilisez **sys.resource_stats** si vous devez surveiller des données moins granulaires sur une longue période de temps. Utilisez **sys.dm_db_resource_stats** si vous devez surveiller des données plus granulaires dans un laps de temps plus court. Pour en savoir plus, consultez [Guide des performances de base de données SQL Azure](sql-database-single-database-monitor.md#monitor-resource-use).

> [!NOTE]
> **sys.dm_db_resource_stats** retourne un résultat vide quand elle est utilisée dans les éditions de base de données Web et Business, qui sont supprimées.
>
>

### <a name="monitor-resource-use"></a>Surveiller l’utilisation des ressources

Vous pouvez surveiller l’utilisation des ressources à l’aide de [SQL Database Query Performance Insight](sql-database-query-performance.md) et du [magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx).

Vous pouvez également surveiller l’utilisation à l’aide de ces deux vues :

* [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
* [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

#### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats
Vous pouvez utiliser la vue [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dans chaque SQL Database. La vue **sys.dm_db_resource_stats** représente les données d’utilisation récente des ressources par rapport au niveau de service. Les pourcentages moyens d’UC, d’E/S des données, d’écritures du journal et de mémoire sont enregistrés toutes les 15 secondes et conservés pendant une heure.

Étant donné que cette vue fournit un aperçu plus granulaire de l’utilisation des ressources, utilisez d’abord **sys.dm_db_resource_stats** pour n’importe quelle analyse d’état actuel ou pour la résolution des problèmes. Par exemple, cette requête affiche l’utilisation moyenne et maximale des ressources pour la base de données actuelle sur la dernière heure :

    SELECT  
        AVG(avg_cpu_percent) AS 'Average CPU use in percent',
        MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
        AVG(avg_data_io_percent) AS 'Average data I/O in percent',
        MAX(avg_data_io_percent) AS 'Maximum data I/O in percent',
        AVG(avg_log_write_percent) AS 'Average log write use in percent',
        MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
        AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
        MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
    FROM sys.dm_db_resource_stats;  

Pour les autres requêtes, consultez les exemples dans [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx).

#### <a name="sysresourcestats"></a>sys.resource_stats
La vue [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) de la base de données **master** fournit des informations supplémentaires vous permettant d’analyser les performances de votre SQL Database à ses niveaux de service et de performances spécifiques. Les données, qui sont collectées toutes les cinq minutes, sont conservées pendant environ 14 jours. Cette vue est utile pour une analyse historique de plus long terme sur l’utilisation des ressources par votre base de données SQL.

Le graphique suivant illustre l’utilisation des ressources d’UC pour une base de données Premium avec le niveau de performances P2 pour chaque heure de la semaine. Ce graphique spécifique commence un lundi, affiche 5 journées de travail, puis un week-end où l’application connaît une activité réduite.

![Utilisation des ressources de base de données SQL](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

D’après les données, cette base de données présente actuellement une charge d’UC maximum juste supérieure à 50 % de l’utilisation de l’UC par rapport au niveau de performances P2 (à la mi-journée le mardi). Si l’UC est le facteur dominant dans le profil de ressource de l’application, vous pourriez décider que P2 est le niveau de performances approprié pour garantir que la charge de travail est toujours adaptée. Si vous prévoyez une croissance de l’application au fil du temps, vous avez tout intérêt à avoir une mémoire-tampon de ressources supplémentaires, afin que l’application n’atteigne jamais le plafond de performances. Si vous augmentez le niveau de performances, vous pouvez éviter les erreurs consultables par les clients susceptibles de se produire lorsqu’une application ne dispose pas de la puissance nécessaire au traitement efficace des requêtes, plus particulièrement au sein des environnements sensibles à la latence. Comme exemple, considérons une base de données qui prend en charge une application remplissant les pages web en fonction des résultats des appels de la base de données.

D’autres types d’applications peuvent interpréter différemment le même graphique. Par exemple, si une application essaie de traiter les données de paie chaque jour et obtient le même graphique, ce genre de modèle de « traitement par lot » peut convenir dans un niveau de performances P1. Le niveau de performances P1 possède 100 DTU, contre 200 DTU pour le niveau de performances P2. Le niveau de performances P1 fournit donc la moitié des performances du niveau de performances P2. Par conséquent, 50 % d’utilisation de l’UC au niveau P2 correspond à 100 % d’utilisation de l’UC au niveau de performance P1. Si l’application n’a pas de délai d’expiration, le fait qu’une tâche volumineuse s’exécute en 2 h ou 2 h 30 peut ne pas avoir d’importance à condition qu’elle soit effectuée le jour même. Une application de cette catégorie peut probablement utiliser un niveau de performances P1. Vous pouvez tirer parti du fait qu’il y a des périodes pendant la journée où l’utilisation des ressources est moindre, ce qui signifie que toute période de pointe peut déborder sur l’un des creux plus tard dans la journée. Le niveau de performances P1 peut convenir pour une application de ce type (et permettre de réaliser des économies) tant que les tâches peuvent se terminer à temps chaque jour.

La SQL Database Azure expose les informations sur les ressources utilisées pour chaque base de données active dans la vue **sys.resource_stats** de la base de données **master** dans chaque serveur. Les données de la table sont agrégées par intervalle de 5 minutes. Avec les niveaux de service De base, Standard et Premium, les données peuvent prendre plus de 5 minutes pour apparaître dans la table, ce qui signifie qu’elles conviennent mieux aux analyses historiques qu’aux analyses en temps réel. L’interrogation de la vue **sys.resource_stats** affiche l’historique récent d’une base de données et confirme si la réservation sélectionnée a fourni la performance souhaitée lorsque c’était nécessaire.

> [!NOTE]
> Vous devez être connecté à la base de données **master** de votre serveur SQL Database logique pour interroger **sys.resource_stats** dans les exemples suivants.
> 
> 

Cet exemple illustre l’exposition des données dans cette vue :

    SELECT TOP 10 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![La vue de catalogue sys.resource_stats](./media/sql-database-performance-guidance/sys_resource_stats.png)

L’exemple suivant vous montre différentes manières d’utiliser la vue du catalogue **sys.resource_stats** pour obtenir des informations sur l’utilisation des ressources par votre SQL Database :

1. Pour consulter l’utilisation des ressources pour la base de données userdb1 au cours de la semaine passée, exécutez cette requête :
   
        SELECT *
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND
              start_time > DATEADD(day, -7, GETDATE())
        ORDER BY start_time DESC;
2. Afin d’évaluer l’adéquation entre votre charge de travail et le niveau de performances, vous devez étudier les différents aspects des mesures de ressources : UC, lectures, écritures, nombre de Workers et nombre de sessions. Voici une requête modifiée à l’aide de **sys.resource_stats** afin d’indiquer les valeurs moyennes et maximales de ces mesures de ressources :
   
        SELECT
            avg(avg_cpu_percent) AS 'Average CPU use in percent',
            max(avg_cpu_percent) AS 'Maximum CPU use in percent',
            avg(avg_data_io_percent) AS 'Average physical data I/O use in percent',
            max(avg_data_io_percent) AS 'Maximum physical data I/O use in percent',
            avg(avg_log_write_percent) AS 'Average log write use in percent',
            max(avg_log_write_percent) AS 'Maximum log write use in percent',
            avg(max_session_percent) AS 'Average % of sessions',
            max(max_session_percent) AS 'Maximum % of sessions',
            avg(max_worker_percent) AS 'Average % of workers',
            max(max_worker_percent) AS 'Maximum % of workers'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
3. Avec les informations ci-dessus relatives aux valeurs moyennes et maximales de chaque mesure de ressources, vous pouvez évaluer l’adéquation entre votre charge de travail et le niveau de performances que vous avez choisi. En général, les valeurs moyennes de **sys.resource_stats** vous offrent une bonne référence à utiliser par rapport à la taille cible. Elles doivent constituer votre principale jauge de mesure. Par exemple, il se peut que vous utilisiez le niveau de service Standard avec le niveau de performance S2. Les pourcentages d’utilisation moyens pour les lectures et écritures d’UC et d’E/S se situent en deçà de 40 %, le nombre moyen de Workers est inférieur à 50 et le nombre moyens de sessions est inférieur à 200. Votre charge de travail peut être prise en charge par le niveau de performance S1. Il est facile de voir si votre base de données s’intègre dans les limites de Workers et de sessions. Pour voir si une base de données s’adapte à un niveau de performances inférieur en ce qui concerne l’UC, les lectures et les écritures, divisez le nombre de DTU du niveau de performances inférieur par le nombre de DTU de votre niveau de performances actuel et multipliez le résultat par 100 :
   
    **S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40**
   
    Le résultat est la différence de performances relative entre les deux niveaux de performances en pourcentage. Si votre utilisation des ressources ne dépasse pas cette quantité, votre charge de travail peut être traitée par ce niveau de performances inférieur. Toutefois, vous devez également examiner toutes les plages de valeurs d’utilisation des ressources et déterminer, en vous appuyant sur le pourcentage, la fréquence selon laquelle la charge de travail de votre base de données s’adapterait au niveau de performances inférieur. La requête suivante génère le pourcentage d’adéquation par dimension de ressource, selon le seuil de 40 % calculé dans cet exemple :
   
        SELECT
            (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent'
            ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    En fonction de votre objectif de niveau de service de la base de données, vous pouvez décider si votre charge de travail s’adapte au niveau de performances inférieur. Si votre objectif de niveau de service de charge de travail de la base de données est de 99,9 % et que la requête précédente retourne des valeurs supérieures à 99,9 % pour les trois dimensions de ressources, il est probable que votre charge de travail s’adapte au niveau de performances inférieur.
   
    En examinant le pourcentage d’adéquation, vous savez par ailleurs si vous devez passer au niveau de performances supérieur pour atteindre votre objectif de niveau de service. Par exemple, l’utilisation d’UC suivante est indiquée pour la base de données userdb1 pour la semaine passée :
   
   | Pourcentage moyen d’UC | Pourcentage maximum d’UC |
   | --- | --- |
   | 24,5 |100,00 |
   
    Le pourcentage moyen d’UC représente environ un quart de la limite du niveau de performances, ce qui conviendrait bien au niveau de performances de la base de données. Toutefois, la valeur maximum montre que la base de données atteint la limite du niveau de performances. Devez-vous passer au niveau de performances supérieur ? Examinez le nombre de fois où votre charge de travail atteint 100 % et comparez ce chiffre à votre objectif de niveau de service de charge de travail de la base de données.
   
        SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data I/O fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
   
    Si cette requête retourne une valeur inférieure à 99,9 % pour l’une des trois dimensions de ressources, vous devez envisager de passer au niveau de performances supérieur, ou employer des techniques de paramétrage de l’application afin de réduire la charge sur la base de données SQL.
4. Cet exercice tient également compte de l’augmentation prévue de votre charge de travail à l’avenir.

Pour les pools élastiques, vous pouvez surveiller des bases de données individuelles dans le pool avec les techniques décrites dans cette section. Mais vous pouvez également surveiller le pool dans son ensemble. Pour plus d’informations, consultez l’article [Surveiller et gérer un pool élastique](sql-database-elastic-pool-manage-portal.md).


### <a name="maximum-concurrent-requests"></a>Nombre maximal de requêtes simultanées
Pour afficher le nombre de requêtes simultanées, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R

Pour analyser la charge de travail d’une base de données SQL Server locale, modifiez cette requête pour la filtrer selon la base de données spécifique que vous analysez. Par exemple, si vous utilisez une base de données locale nommée MyDatabase, cette requête Transact-SQL renvoie le nombre de requêtes simultanées dans cette base de données :

    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase'

Il s’agit simplement d’un instantané à un point unique dans le temps. Pour obtenir une meilleure compréhension de votre charge de travail et des exigences liées aux demandes simultanées, il vous faut collecter plusieurs échantillons au fil du temps.

### <a name="maximum-concurrent-logins"></a>Nombre maximal de connexions simultanées
Vous pouvez analyser vos modèles d'utilisateur et d'application pour avoir une idée de la fréquence des connexions. Vous pouvez également exécuter des charges réelles dans un environnement de test pour vous assurer que vous n’atteignez pas cette limite ou d’autres limites décrites dans cet article. Il n’existe aucune requête ou vue de gestion dynamique qui peut vous indiquer le nombre de connexions simultanées ou un historique dédié.

Si plusieurs clients utilisent la même chaîne de connexion, le service authentifie chaque connexion. Si 10 utilisateurs se connectent simultanément à une base de données avec les mêmes nom d’utilisateur et mot de passe, dix connexions simultanées seront établies. Cette limite s’applique uniquement à la durée de la connexion et de l’authentification. Si ces mêmes 10 utilisateurs se connectent séquentiellement à la base de données, le nombre de connexions simultanées ne sera jamais supérieur à 1.

> [!NOTE]
> Actuellement, cette limite ne s’applique pas aux bases de données de pools élastiques.
> 
> 

### <a name="maximum-sessions"></a>Nombre maximal de sessions
Pour afficher le nombre de sessions simultanément actives, exécutez cette requête Transact-SQL sur votre base de données SQL :

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

Si vous analysez une charge de travail SQL Server locale, modifiez la requête pour vous concentrer sur une base de données spécifique. Cette requête vous aide à déterminer les éventuels besoins de votre session pour la base de données si vous envisagez de la déplacer vers Azure SQL Database.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

Là encore, ces requêtes renvoient un nombre à un point dans le temps. Si vous collectez plusieurs échantillons au fil du temps, vous bénéficiez d’une meilleure compréhension de l’utilisation de votre session.

Pour une analyse SQL Database, vous pouvez obtenir des statistiques d’historique sur les sessions en interrogeant la vue [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) et en consultant la colonne **active_session_count**. 

## <a name="next-steps"></a>Étapes suivantes

- Réglez automatiquement les index de base de données et interrogez les plans d’exécution avec le [réglage automatique d’Azure SQL Database](sql-database-automatic-tuning.md).
- Effectuez automatiquement le monitoring des performances de base de données avec[Azure SQL Intelligent Insights](sql-database-intelligent-insights.md). Cette fonctionnalité fournit des informations de diagnostic et une analyse des causes racines des problèmes de performances.
