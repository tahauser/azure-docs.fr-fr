---
title: "Détection d’anomalies dans le guide d’utilisation d’Azure (version préliminaire) | Microsoft Docs"
description: "Utilisez Stream Analytics et l’apprentissage automatique détecter les anomalies."
services: stream-analytics
documentationcenter: 
author: dubansal
manager: jhubbard
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: dubansal
ms.openlocfilehash: ff8571c6447f32ef9a435f5200803e76f6013ffa
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2018
---
# <a name="using-the-anomalydetection-operator"></a>Utilisation de l’opérateur ANOMALYDETECTION

> [!IMPORTANT]
> Cette fonctionnalité n’existe qu’en version préliminaire. Nous ne recommandons pas une utilisation en production.

L’opérateur **ANOMALYDETECTION** peut être utilisé pour détecter les anomalies dans les flux d’événements.
Par exemple, une diminution lente dans la mémoire disponible sur une longue durée peut traduire une fuite de mémoire, ou le nombre de demandes de service web qui sont stables sur une plage peut considérablement augmenter ou diminuer.

Il vérifie les types suivants d’anomalies pendant la durée spécifiée :

- Changement de niveau bidirectionnel
- Tendance positive lente
- Tendance négative lente

L’intervalle de temps indiquant jusqu’à quand remonter dans l’historique des événements à compter de celui en cours est limité à l’aide de la clause **LIMIT DURATION**. **ANOMALYDETECTION** peut éventuellement être limité aux seuls événements qui correspondent à une certaine propriété ou condition à l’aide de la clause **WHEN**.

Il peut également, facultativement, traiter des groupes d’événements séparément en fonction de la clé spécifiée dans la clause **PARTITION BY**. L’apprentissage et la prédiction se produisent indépendamment dans chaque partition.

## <a name="syntax"></a>Syntaxe

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 


## <a name="example-usage"></a>Exemple d’utilisation

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`|


## <a name="arguments"></a>Arguments

- **scalar_expression**

  L’expression scalaire sur laquelle porte la détection d’anomalies. Il s’agit d’une expression de type float ou bigint qui retourne une valeur (scalaire) unique. L’expression générique **\*** n’est pas autorisée. **scalar_expression** ne peut pas contenir d’autres fonctions analytiques ou de fonctions externes.

- **OVER ( [ clause_partition_by ] clause_limit_duration [clause_when])**

- **clause_partition_by** 

  La clause `PARTITION BY <partition key>` divise l’apprentissage et la formation sur des partitions distinctes. En d’autres termes, un modèle distinct serait être utilisé par valeur de `<partition key>`, et seuls les événements avec la valeur seraient utilisés pour l’apprentissage et la formation dans ce modèle. Par exemple,

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

  formera et notera une lecture par rapport à d’autres lectures du même capteur uniquement.

- **clause_limit_duration** DURATION (\<unité\>, \<longueur\>)

  Spécifie la quantité de l’historique de l’événement à compter de l’événement actuel prise en compte dans le calcul de **ANOMALYDETECTION**. Consultez DATEDIFF pour obtenir une description détaillée des unités prises en charge et de leurs abréviations.

- **clause_when** 

  Spécifie une condition booléenne pour les événements considérés dans le calcul de **ANOMALYDETECTION**.

## <a name="return-types"></a>Types de retour

La fonction retourne un enregistrement qui contient les trois résultats comme sortie. Les propriétés associées aux différents types de détection d’anomalies sont appelées :

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Pour extraire les valeurs individuelles de l’enregistrement, utilisez la fonction **GetRecordPropertyValue**. Par exemple : 

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 


Une anomalie d’un type particulier est détectée lorsqu’un de ces scores d’anomalie dépasse un seuil. Le seuil peut être n’importe quel nombre à virgule flottante \>= 0. Le seuil est un compromis entre la sensibilité et la confiance. Par exemple, un seuil inférieur rend les détections plus sensibles aux modifications et génère davantage d’alertes, tandis qu’un seuil plus élevé peut rendre la détection plus confiante et moins sensible, mais masquer certaines anomalies. La valeur de seuil exacte à utiliser dépend du scénario. Il n’existe aucune limite supérieure, mais l’intervalle recommandé est 3,25-5.

## <a name="algorithm"></a>Algorithme

**ANOMALYDETECTION** utilise sémantique de fenêtre glissante, ce qui signifie que le calcul s’exécute par événement qui entre dans la fonction, et un score est généré pour cet événement. Le calcul est basé sur les martingales d’échangeabilité, qui fonctionnent en vérifiant si la distribution des valeurs de l’événement a été modifiée. Dans ce cas, une anomalie potentielle a été détectée. Le score retourné est une indication sur le niveau de confiance de cette anomalie. En guise d’optimisation interne, **ANOMALYDETECTION** calcule le score d’anomalie d’un événement en fonction des événements *d* à *2d*, où *d* est la taille de la fenêtre de détection spécifiée.

**ANOMALYDETECTION** s’attend à ce que la série chronologique d’entrée soit uniforme. Un flux d’événements peut être rendu uniforme par agrégation sur une fenêtre récurrente ou bascule. Dans les scénarios où l’écart entre les événements est toujours inférieur à la fenêtre d’agrégation, une fenêtre bascule est suffisante pour rendre la série chronologique uniforme. Lorsque l’intervalle peut être plus grand, les vides peuvent être remplis en répétant la dernière valeur à l’aide d’une fenêtre récurrente. Ces deux scénarios peuvent être gérés par l’exemple qui suit.

## <a name="performance-guidance"></a>Guide sur les performances

- Utilisez 6 SU pour les travaux. 
- Envoyez les événements avec au moins 1 seconde d’écart.
- Une requête non partitionnée utilisant la fonction **ANOMALYDETECTION** peut produire des résultats avec une latence de calcul d’environ 25 ms en moyenne.
- La latence subie par une requête partitionnée varie légèrement suivant le nombre de partitions, car le nombre de calculs peut alors être plus élevé. Toutefois, la latence est approximativement la même que dans le cas non partitionné pour un total comparable d’événements dans toutes les partitions.
- Lors de la lecture de données en différé, une grande quantité de données est intégrée rapidement. Le traitement de ces données est actuellement beaucoup plus lent. La latence dans de tels scénarios augmente de façon linéaire avec le nombre de points de données dans la fenêtre plutôt qu’avec la taille de fenêtre ou l’intervalle d’événements eux-mêmes. Pour réduire la latence dans les cas en différé, envisagez d’utiliser une taille de fenêtre plus petite. Vous pouvez également envisager de commencer votre projet à partir de l’heure actuelle. Voici quelques exemples de latence dans une requête non partitionnée : 
    - 60 points de données dans la fenêtre de détection peuvent entraîner une latence de 10 secondes avec un débit de 3 Mbits/s. 
    - À 600 points de données, la latence peut atteindre environ 80 secondes avec un débit de 0,4 Mbits/s.

## <a name="example"></a>exemples

La requête suivante peut être utilisée pour générer une alerte si une anomalie est détectée.
Lorsque le flux d’entrée n’est pas uniforme, l’étape d’agrégation peut vous aider à le transformer en une série chronologique uniforme. L’exemple utilise **AVG**, mais le type d’agrégation varie selon le scénario utilisateur. En outre, lorsqu’une série chronologique a des écarts supérieurs à la fenêtre d’agrégation, il n’y aura aucun événement dans la série chronologique pour déclencher la détection d’anomalie (conformément à sémantique de fenêtre glissante). Par conséquent, l’hypothèse d’uniformité est rompue lorsque l’événement suivant arrive. Dans ce cas, il convient de combler les vides dans la série chronologique. Une approche possible consiste à prendre le dernier événement dans chaque fenêtre, comme indiqué ci-dessous.

    WITH AggregationStep AS 
    (
         SELECT
               System.Timestamp as tumblingWindowEnd,

               AVG(value) as avgValue

         FROM input
         GROUP BY TumblingWindow(second, 5)
    ),

    FillInMissingValuesStep AS
    (
          SELECT
                System.Timestamp AS hoppingWindowEnd,

                TopOne() OVER (ORDER BY tumblingWindowEnd DESC) AS lastEvent

         FROM AggregationStep
         GROUP BY HOPPINGWINDOW(second, 300, 5)

    ),

    AnomalyDetectionStep AS
    (

          SELECT
                hoppingWindowEnd,
                lastEvent.tumblingWindowEnd as lastTumblingWindowEnd,
                lastEvent.avgValue as lastEventAvgValue,
                system.timestamp as anomalyDetectionStepTimestamp,

                ANOMALYDETECTION(lastEvent.avgValue) OVER (LIMIT DURATION(hour, 1)) as
                scores

          FROM FillInMissingValuesStep
    )

    SELECT
          alert = 1,
          hoppingWindowEnd,
          lastTumblingWindowEnd,
          lastEventAvgValue,
          anomalyDetectionStepTimestamp,
          scores

    INTO output

    FROM AnomalyDetectionStep

    WHERE

        CAST(GetRecordPropertyValue(scores, 'BiLevelChangeScore') as float) >= 3.25

        OR CAST(GetRecordPropertyValue(scores, 'SlowPosTrendScore') as float) >=
        3.25

       OR CAST(GetRecordPropertyValue(scores, 'SlowNegTrendScore') as float) >=
       3.25

## <a name="references"></a>Références

* [Détection des anomalies – Utilisation de Machine Learning pour détecter les anomalies dans les données de série chronologique](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [API de détection des anomalies Machine Learning](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Détection des anomalies de série chronologique](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="get-support"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

