---
title: 'Azure Stream Analytics : Comprendre et ajuster les unités de streaming | Microsoft Docs'
description: Comprenez quels facteurs affectent les performances dans Azure Stream Analytics.
keywords: unité de streaming, performance des requêtes
services: stream-analytics
documentationcenter: ''
author: JSeb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 4312b5f5a64048ad697ae398697da1c59a75e75c
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="understand-and-adjust-streaming-units"></a>Comprendre et ajuster les unités de streaming

Azure Stream Analytics agrège la pondération des performances d’exécution d’un travail dans des unités de streaming. Les unités de streaming représentent les ressources informatiques consommées pour exécuter un travail. Ces unités permettent de décrire la capacité relative de traitement des événements basée sur une mesure mixte du processeur, de la mémoire et des taux de lecture et d’écriture. Cette capacité vous permet de vous concentrer sur la logique de requête. Vous ne devez plus tenir compte des performances des niveaux de stockage, ni allouer de mémoire pour votre travail manuellement et calculer approximativement le nombre de processeurs nécessaires pour exécuter votre travail en temps voulu.

Afin d’obtenir un traitement du streaming à faible latence, les travaux Azure Stream Analytics effectuent tout le traitement en mémoire. Quand la mémoire devient insuffisante, le travail de streaming échoue. Par conséquent, pour un travail de production, il est important de surveiller l’utilisation des ressources d’un travail de streaming et de vérifier qu’il existe suffisamment de ressources allouées afin d’assurer l’exécution des travaux 24 heures sur 24 et 7 jours sur 7.

La métrique correspond à un pourcentage compris entre 0 % et 100 %. Pour un travail de streaming avec un encombrement minimal, la métrique % d’utilisation de SU se situe généralement entre 10 et 20 %. Il est préférable de conserver la métrique sous 80 % pour prendre en compte les pics occasionnels.  Vous pouvez définir une alerte sur la métrique (reportez-vous [ici pour configurer des alertes de métriques](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)).



## <a name="configure-stream-analytics-streaming-units-sus"></a>Configurer des unités de streaming Stream Analytics
1. Connectez-vous au [portail Azure](http://portal.azure.com/)
2. Dans la liste des ressources, recherchez le travail Stream Analytics que vous souhaitez mettre à l’échelle, puis ouvrez-le. 
3. Dans la page du travail, sous Configurer, cliquez sur **Échelle**. 

    ![Configuration d’un travail Stream Analytics sur le portail Azure][img.stream.analytics.preview.portal.settings.scale]
    
4. Définissez les unités SU pour le travail à l’aide du curseur. Notez que vous êtes limité à des paramètres d’unité SU spécifiques. 


## <a name="monitor-job-performance"></a>Surveillance des performances du travail
À l’aide du portail Azure, vous pouvez suivre le débit d’un travail :

![Surveillance des travaux Azure Stream Analytics][img.stream.analytics.monitor.job]

Calculez le débit prévu pour la charge de travail. Si le débit est plus faible que prévu, paramétrez la partition d’entrée ainsi que la requête, puis ajoutez des unités SU à votre travail.


## <a name="how-many-sus-are-required-for-a-job"></a>Combien d’unités de streaming sont requises pour un travail ?

Le choix du nombre d’unités de streaming requises pour un travail particulier dépend de la configuration de la partition pour les entrées et de la requête définie pour le travail. La page **Mise à l’échelle** vous permet de définir le nombre adapté d’unités de streaming. Il est recommandé d’allouer plus d’unités de streaming que nécessaire. Le moteur de traitement Stream Analytics est configuré pour une latence et un débit supérieurs, en allouant une capacité de mémoire supplémentaire.

En règle générale, la meilleure pratique consiste à démarrer avec 6 unités de streaming pour les requêtes qui n’utilisent pas **PARTITION BY**. Déterminez ensuite la configuration idéale en utilisant une méthode d’essai et d’erreur où vous modifiez le nombre d’unités de streaming, une fois que vous avez transmis le volume représentatif de données et examiné la métrique % d’utilisation de SU.

Pour plus d’informations sur le choix du nombre adapté d’unités de streaming, consultez cette page : [Mettre à l’échelle des travaux Azure Stream Analytics pour augmenter le débit](stream-analytics-scale-jobs.md)

> [!Note]
> Le choix du nombre d’unités SU requises pour un travail particulier dépend de la configuration de la partition pour les entrées et de la requête définie pour le travail. Vous pouvez sélectionner votre quota en unités SU pour un travail. Par défaut, chaque abonnement Azure dispose d’un quota pouvant atteindre 200 unités de streaming pour tous les travaux Stream Analytics d’une région spécifique. Pour augmenter ce quota d’unités SU pour vos abonnements, contactez le [Support Microsoft](http://support.microsoft.com). Valeurs valides pour les unités SU par travail : 1, 3, 6 et au-dessus par incréments de 6.



## <a name="factors-increasing-su-utilization"></a>Facteurs qui augmentent le pourcentage d’utilisation d’unités de streaming 
### <a name="stateful-query-logic"></a>Logique de requête avec état 
L’une des caractéristiques propres à un travail Azure Stream Analytics consiste à effectuer un traitement avec état, comme des agrégations fenêtrées, jointures temporelles et fonctions d’analyse temporelle. Chacun de ces opérateurs conserve certains états. 
#### <a name="windowed-aggregates"></a>Agrégations fenêtrées
La taille de l’état d’une agrégation fenêtrée est proportionnelle au nombre de groupes (cardinalité) dans le groupe par opérateur. Par exemple, dans la requête suivante, le nombre associé à clusterid est la cardinalité de la requête. 

    SELECT count(*)
    FROM input 
    GROUP BY  clusterid, tumblingwindow (minutes, 5)


Afin d’atténuer les problèmes provoqués par une cardinalité élevée dans la requête précédente, vous pouvez envoyer des événements à Event Hub avec un partitionnement par « clusterid » et augmenter la taille des instances de la requête en autorisant le système à traiter chaque partition d’entrée séparément à l’aide de **PARTITION BY**  comme indiqué dans l’exemple ci-dessous :


    SELECT count(*) 
    FROM PARTITION BY PartitionId
    GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)

Une fois que la requête est partitionnée, elle est répartie sur plusieurs nœuds. Par conséquent, le nombre de clusterid arrivant dans chaque nœud est réduit, ce qui réduit d’autant la cardinalité du groupe par opérateur. 

Les partitions Event Hub doivent être partitionnées par la clé de regroupement pour éviter une étape de réduction. Des détails supplémentaires sont couverts [ici](https://docs.microsoft.com/azure/event-hubs/event-hubs-overview). 
#### <a name="temporal-join"></a>Jointure temporelle
La taille de l’état d’une jointure temporelle est proportionnelle au nombre d’événements dans la marge de manœuvre temporelle de la jointure, qui est la vitesse d’entrée des événements multipliée par la taille de la marge de manœuvre. 

Le nombre d’événements sans correspondance de la jointure affecte la consommation de mémoire pour la requête. La requête suivante vise à identifier l’exposition publicitaire qui génère des clics :

    SELECT clicks.id
    FROM clicks 
    INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.

Dans cet exemple, il est possible qu’un grand nombre de publicités s’affichent et que quelques personnes cliquent dessus ; cette configuration est requise pour conserver l’ensemble des événements dans la fenêtre de temps. La consommation de mémoire est proportionnelle à la taille de la fenêtre et au taux d’événements. 

Pour corriger ce problème, envoyez des événements à Event Hub avec un partitionnement par clés de jointure (id dans ce cas) et augmentez la taille des instances de la requête en autorisant le système à traiter chaque partition d’entrée séparément à l’aide de **PARTITION BY** comme indiqué :

    SELECT clicks.id
    FROM clicks PARTITION BY PartitionId
    INNER JOIN impressions PARTITION BY PartitionId 
    ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>

Une fois que la requête est partitionnée, elle est répartie sur plusieurs nœuds. Par conséquent, le nombre d’événements arrivant dans chaque nœud est réduit, ce qui réduit d’autant la taille de l’état dans la fenêtre de jointure. 
#### <a name="temporal-analytic-function"></a>Fonction d’analyse temporelle
La taille de l’état d’une fonction d’analyse temporelle est proportionnelle au taux d’événements multiplié par la durée. La solution est similaire à celle de la jointure temporelle. Vous pouvez augmenter la taille des instances de la requête à l’aide de **PARTITION BY**. 

### <a name="out-of-order-buffer"></a>Mémoire tampon d’événements en désordre 
L’utilisateur peut configurer la taille de la mémoire tampon d’événements en désordre dans le volet de configuration Ordre des événements. La mémoire tampon est utilisée pour contenir des entrées pendant la durée d’affichage de la fenêtre et les réorganiser. La taille de la mémoire tampon est proportionnelle à la vitesse d’entrée des événements multipliée par la taille de la fenêtre d’événements en désordre. La taille de fenêtre par défaut est égale à 0. 

Pour corriger ce problème, augmentez la taille des instances de la requête à l’aide de **PARTITION BY**. Une fois que la requête est partitionnée, elle est répartie sur plusieurs nœuds. Par conséquent, le nombre d’événements arrivant dans chaque nœud est réduit, ce qui réduit d’autant le nombre d’événements dans chaque mémoire tampon de réorganisation. 

### <a name="input-partition-count"></a>Nombre de partitions d’entrée 
Chaque partition d’entrée d’une entrée de travail a une mémoire tampon. Plus le nombre de partitions d’entrée est élevé, plus le travail consomme de ressources. Pour chaque unité de streaming, Azure Stream Analytics peut traiter environ 1 Mo/s d’entrées et vous pouvez donc associer le nombre d’unités de streaming ASA au nombre de partitions de votre Event Hub. En règle générale, un travail avec 1 unité de streaming est suffisant pour un Event Hub avec 2 partitions (ce qui est le nombre minimal pour Event Hub). Si Event Hub a davantage de partitions, votre travail ASA consomme davantage de ressources, mais n’utilise pas nécessairement le débit supplémentaire fourni par Event Hub. Pour un travail avec 6 unités de streaming, vous devrez peut-être demander 4 ou 8 partitions à Event Hub. L’utilisation d’un Event Hub avec 16 partitions ou plus dans un travail avec 1 unité de streaming contribue souvent à une utilisation excessive des ressources et doit être évitée. 

### <a name="reference-data"></a>Données de référence 
Les données de référence dans ASA sont chargées en mémoire pour la recherche rapide. Avec l’implémentation actuelle, chaque opération de jointure avec les données de référence conserve une copie des données de référence en mémoire, même si vous effectuez la jointure plusieurs fois avec les mêmes données de référence. Pour les requêtes avec **PARTITION BY**, comme chaque partition possède une copie des données de référence, les partitions sont entièrement découplées. Avec l’effet multiplicateur, l’utilisation de la mémoire peut être rapidement très élevée si vous effectuez la jointure avec les données de référence plusieurs fois avec plusieurs partitions.  

#### <a name="use-of-udf-functions"></a>Utilisation de fonctions UDF
Quand vous ajoutez une fonction UDF, Azure Stream Analytics charge le runtime JavaScript en mémoire. Cela affecte le pourcentage d’utilisation d’unités de streaming.


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Créer des requêtes parallélisables dans Azure Stream Analytics](stream-analytics-parallelization.md)
* [Mettre à l’échelle des travaux Azure Stream Analytics pour augmenter le débit](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   
