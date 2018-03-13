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
ms.date: 02/12/2018
ms.author: dubansal
ms.openlocfilehash: d8762ea608afed707d41a3c0a1a8725457a0e4dc
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Détection d’anomalies dans Azure Stream Analytics

> [!IMPORTANT]
> Cette fonctionnalité est en version préliminaire. Il n’est pas recommandé de l’utiliser avec des charges de travail de production.

L’opérateur **AnomalyDetection** est utilisé pour détecter différents types d’anomalies dans les flux d’événements. Par exemple, une diminution lente dans la mémoire disponible sur une longue durée peut traduire une fuite de mémoire, ou le nombre de demandes de service web qui sont stables sur une plage peut considérablement augmenter ou diminuer.  

L’opérateur AnomalyDetection détecte trois types d’anomalies : 

* **Changement au niveau bidirectionnel** : Une augmentation ou une diminution soutenue du niveau des valeurs, vers le haut comme vers le bas. Cette valeur est différente des pics et chutes, qui sont des changements instantanés ou à court terme.  

* **Tendance positive lente** : une lente augmentation de la tendance au fil du temps.  

* **Tendance négative lente** : une lente diminution de la tendance au fil du temps.  

Lorsque vous utilisez l’opérateur AnomalyDetection, vous devez spécifier la clause **Limit Duration**. Cette clause spécifie l’intervalle de temps (combien de temps avant les événements actuels) qui doit être pris en compte lors de la détection d’anomalies. Cet opérateur peut éventuellement être limité aux seuls événements qui correspondent à une certaine propriété ou condition à l’aide de la clause  **When** . Il peut également, facultativement, traiter des groupes d’événements séparément en fonction de la clé spécifiée dans la clause  **Partition by** . L’apprentissage et la prédiction se produisent indépendamment pour chaque partition. 

## <a name="syntax-for-anomalydetection-operator"></a>Syntaxe de l’opérateur AnomalyDetection

`ANOMALYDETECTION(<scalar_expression>) OVER ([PARTITION BY <partition key>] LIMIT DURATION(<unit>, <length>) [WHEN boolean_expression])` 

**Exemple d’utilisation**  

`SELECT id, val, ANOMALYDETECTION(val) OVER(PARTITION BY id LIMIT DURATION(hour, 1) WHEN id > 100) FROM input`

### <a name="arguments"></a>Arguments

* **scalar_expression** : L’expression scalaire sur laquelle porte la détection d’anomalies. Les valeurs autorisées pour ce paramètre comprennent les types de données Float ou Bigint qui renvoient une seule valeur (scalaire). L’expression générique **\*** n’est pas autorisée. Une expression scalaire ne peut pas contenir d’autres fonctions analytiques ou de fonctions externes. 

* **partition_by_clause** : La clause `PARTITION BY <partition key>` divise l’apprentissage et la formation sur des partitions distinctes. En d’autres termes, un modèle distinct serait être utilisé par valeur de `<partition key>`, et seuls les événements avec la valeur seraient utilisés pour l’apprentissage et la formation dans ce modèle. Par exemple, la requête suivante forme et marque une lecture contre d’autres lectures du même capteur uniquement :

  `SELECT sensorId, reading, ANOMALYDETECTION(reading) OVER(PARTITION BY sensorId LIMIT DURATION(hour, 1)) FROM input`

* **limit_duration clause** `DURATION(<unit>, <length>)` : Cette clause spécifie l’intervalle de temps (combien de temps avant les événements actuels) qui doit être pris en compte lors de la détection d’anomalies. Consultez [DATEDIFF](https://msdn.microsoft.com/azure/stream-analytics/reference/datediff-azure-stream-analytics) pour obtenir une description détaillée des unités prises en charge et de leurs abréviations. 

* **when_clause** : Spécifie une condition booléenne pour les événements considérés dans le calcul de la détection d’anomalie.

### <a name="return-types"></a>Types de retour

L’opérateur AnomalyDetection retourne un enregistrement qui contient les trois résultats comme sortie. Les propriétés associées aux différents types de détection d’anomalies sont :

- BiLevelChangeScore
- SlowPosTrendScore
- SlowNegTrendScore

Pour extraire les valeurs individuelles de l’enregistrement, utilisez la fonction **GetRecordPropertyValue**. Par exemple : 

`SELECT id, val FROM input WHERE (GetRecordPropertyValue(ANOMALYDETECTION(val) OVER(LIMIT DURATION(hour, 1)), 'BiLevelChangeScore')) > 3.25` 

Une anomalie d’un type est détectée lorsqu’un des scores d’anomalie dépasse un seuil. Le seuil peut être n’importe quel nombre à virgule flottante >= 0. Le seuil est un compromis entre la sensibilité et la confiance. Par exemple, un seuil inférieur rend les détections plus sensibles aux modifications et génère davantage d’alertes, tandis qu’un seuil plus élevé peut rendre la détection plus confiante et moins sensible, mais masquer certaines anomalies. La valeur de seuil exacte à utiliser dépend du scénario. Il n’existe aucune limite supérieure, mais l’intervalle recommandé est 3,25-5. 

## <a name="anomaly-detection-algorithm"></a>Algorithme de détection d’anomalie

* L’opérateur AnomalyDetection utilise une approche **d’apprentissage non supervisée** où il ne traite aucun type de distribution dans les événements. En général, deux modèles sont maintenus en parallèle en tout temps, où l’un d’eux est utilisé pour les résultats et l’autre est formé en arrière-plan. Les modèles de détection d’anomalie sont formés pour utiliser des données des flux actuels plutôt que pour utiliser un mécanisme hors bande. La quantité de données utilisées pour la formation dépend de la taille de la fenêtre spécifiée par l’utilisateur dans le paramètre Limit Duration. Chaque modèle finit par obtenir la formation basée sur des événements de valeur d à 2d. Nous vous recommandons d’avoir au moins 50 événements dans chaque fenêtre pour de meilleurs résultats. 

* L’opérateur AnomalyDetection utilise la **sémantique de fenêtre glissante** pour former des modèles et marquer des événements. Ce qui signifie qu’une évaluation d’anomalie est effectuée pour chaque événement, et un résultat est retourné. Le score est une indication sur le niveau de confiance de cette anomalie. 

* L’opérateur AnomalyDetection fournit une **garantie de répétabilité** : la même entrée produit toujours le même résultat, peu importe l’heure de début de la sortie de la tâche. L’heure de début de la sortie de la tâche représente l’heure à laquelle le premier événement de sortie est produit par la tâche. Elle est définie par l’utilisateur au moment actuel, à une valeur personnalisée ou à la dernière heure de sortie (si la tâche avait déjà produit une sortie). 

### <a name="training-the-models"></a>Formation des modèles 

Tandis que le temps défile, les modèles sont formés avec des données différentes. Pour donner du sens aux résultats, la formation aide à comprendre les mécanismes sous-jacents selon lesquels les modèles sont formés. Ici, **t<sub>0</sub>** est l’**heure de début de la sortie de la tâche** et **d** est la **taille de la fenêtre** du paramètre Limit Duration. Supposons que le temps est divisé en **tronçons de taille d**, à partir de `01/01/0001 00:00:00`. Les étapes suivantes sont utilisées pour former le modèle et marquer les événements :

* Lorsqu’une tâche débute, on peut lire les données qui commencent au temps t<sub>0</sub> – 2d.  
* Lorsque le temps atteint le tronçon suivant, un modèle M1 est créé et commence à être formé.  
* Lorsque le temps avance jusqu’à un autre tronçon, un modèle M2 est créé et commence à être formé.  
* Lorsque le temps atteint t<sub>0</sub>, M1 est rendu actif et son résultat commence à être traité.  
* Au tronçon suivant, trois choses se produisent :  

  * M1 n’est plus nécessaire et est abandonné.  
  * M2 a été suffisamment formé et est utilisé pour les résultats.  
  * Un modèle M3 est créé et commence à être formé en arrière-plan.  

* Ce cycle se répète à chaque tronçon, où le modèle actif est abandonné, échangé avec le modèle parallèle, et un troisième modèle commence à être formé en arrière-plan. 

Sous forme de diagramme, les étapes ressemblent à ce qui suit : 

![Formation de modèles](media/stream-analytics-machine-learning-anomaly-detection/training_model.png)

|**Modèle** | **Heure de début de formation** | **Heure pour commencer à utiliser le résultat** |
|---------|---------|---------|
|M1     | 11 h 20   | 11 h 33   |
|M2     | 11 h 30   | 11 h 40   |
|M3     | 11 h 40   | 11 h 50   |

* Le modèle M1 commence à être formé à 11 h 20, qui correspond au tronçon suivant le début de la lecture de la tâche à 11 h 13. La première sortie est produite par M1 à 11 h 33 après une formation avec 13 minutes de données. 

* Un nouveau modèle M2 commence aussi sa formation à 11 h 30 mais son résultat ne sera pas utilisé avant 11 h 40, après une formation avec 10 minutes de données. 

* M3 suit le même schéma que M2. 

### <a name="scoring-with-the-models"></a>Notations avec les modèles 

Au niveau Machine Learning, l’algorithme de détection d’anomalie calcule une valeur d’étrangeté pour chaque événement entrant en les comparant avec les événements dans une fenêtre d’historique. Le calcul d’étrangeté est différent pour chaque type d’anomalie.  

Examinons le calcul d’étrangeté en détail (en supposant qu’un ensemble de fenêtres historiques avec des événements existe) : 

1. **Changement au niveau bidirectionnel** : Basée sur la fenêtre d’historique, une plage opérationnelle normale est calculée par [ 10e centile, 90e centile]. La limite la plus basse étant la valeur du 10e centile, et la limite la plus haute étant la valeur du 90e centile. Une valeur d’étrangeté pour l’événement actuel est calculée par :  

   - 0, si event_value est dans la plage opérationnelle normale  
   - event_value/90th_percentile, si event_value est supérieur à 90th_percentile  
   - 10th_percentile/event_value, si event_value est inférieur à 10th_percentile  

2. **Tendance positive lente** : une ligne de tendance sur les valeurs d’événement dans la fenêtre d’historique est calculée et nous cherchons une tendance positive dans la ligne. La valeur d’étrangeté est calculée par :  

   - Slope, si slope est positif  
   - 0, dans le cas contraire 

1. **Tendance négative lente** : une ligne de tendance sur les valeurs d’événement dans la fenêtre d’historique est calculée et nous cherchons une tendance négative dans la ligne. La valeur d’étrangeté est calculée par : 

   - Slope, si slope est négatif  
   - 0, dans le cas contraire  

Lorsque la valeur d’étrangeté pour l’événement entrant est calculée, une valeur martingale est calculée, selon la valeur d’étrangeté (voir le [blog Machine Learning](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/) pour savoir comment la valeur martingale est calculée). Cette valeur martingale est retournée comme score d’anomalie. La valeur martingale augmente lentement en réponse aux valeurs étranges, ce qui permet au détecteur de demeurer résistant face à des changements sporadiques et réduit les fausses alertes. Elle a aussi une propriété utile : 

Probabilité [où t égal M<sub>t</sub> > λ ] < 1/λ, où M<sub>t</sub> est la valeur martingale à l’instant t et λ est une valeur réelle. Par exemple, si nous envoyons une alerte quand M<sub>t</sub>>100, alors la probabilité de faux positifs est inférieure à 1/100.  

## <a name="guidance-for-using-the-bi-directional-level-change-detector"></a>Conseils d’utilisation du détecteur de changements de niveau bidirectionnels 

Le détecteur de changements de niveau bidirectionnel peut être utilisé dans des scénarios tels que des pannes de courant ou des récupérations, du trafic saturé, etc. Cependant, il opère de telle façon qu’une fois le modèle formé avec certaines données, un autre changement de niveau est anormal si et seulement si la nouvelle valeur est supérieure à la limite supérieure précédente (cas de changement de niveau vers le haut) ou inférieure à la limite inférieure précédente (cas de changement de niveau vers le bas). Par conséquent, un modèle ne devrait pas voir les valeurs des données dans la plage du nouveau niveau (vers le haut ou vers le bas) dans la fenêtre de sa formation pour être considéré comme anormal. 

Les points suivants doivent être pris en compte lors de l’utilisation de ce détecteur : 

1. Lorsque la série chronologique rencontre soudainement un pic ou une chute dans ses valeurs, l’opérateur AnomalyDetection le détecte. Mais la détection d’un retour à la normale nécessite davantage de planification. Si une série chronologique était dans un état stable avant l’anomalie, ce qui peut être le cas en réglant la fenêtre de détection de l’opérateur AnomalyDetection à au plus la moitié de la longueur de l’anomalie. Ce scénario part du principe que la durée minimum de l’anomalie peut être estimée en avance, et qu’il y a assez d’événements dans ce délai d’exécution pour former suffisamment un modèle (au moins 50 événements). 

   Ceci est montré dans les figure 1 et 2 ci-dessous, en utilisant un changement de limite supérieure (la même logique s’applique à un changement de limite inférieure). Sur les deux figures, les formes d’onde représentent un changement de niveau anormal. Les lignes verticales orange représentent les limites de tronçon et la taille de tronçon est similaire à la fenêtre de détection spécifiée dans l’opérateur AnomalyDetection. Les lignes vertes indiquent la taille de la fenêtre de formation. Dans la Figure 1, la taille du tronçon correspond à la durée de l’anomalie. Dans la Figure 2, la taille du tronçon correspond à la moitié de la durée de l’anomalie. Dans tous les cas, un changement vers le haut est détecté, car le modèle utilisé pour marquer a été formé sur des données normales. Mais en se basant sur le fonctionnement du détecteur de changement au niveau bidirectionnel, nous devons exclure les valeurs de la fenêtre de formation utilisées pour le modèle qui marque le retour à la normale. Dans la Figure 1, la formation du modèle de notation inclut des événements normaux. Le retour à la normale ne peut donc être détecté. Mais dans la Figure 2, elle inclut la partie anormale, ce qui assure la détection du retour à la normale. Toute valeur inférieure à la moitié fonctionne aussi pour la même raison, alors que toute valeur supérieure inclura au final une petite part des événements normaux. 

   ![AD plus taille de la fenêtre égal durée de l’anomalie](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_anomaly_length.png)

   ![AD plus taille de la fenêtre égal moitié de la durée de l’anomalie](media/stream-analytics-machine-learning-anomaly-detection/windowsize_equal_half_anomaly_length.png)

2. Dans les cas où la durée de l’anomalie ne peut être prédite, ce détecteur opère à effort maximum. Toutefois, choisir une fenêtre de temps plus courte limite les données formées, ce qui augmente la probabilité de détecter le retour à la normale. 

3. Dans le scénario suivant, l’anomalie la plus longue n’est pas détectée, car la fenêtre de formation inclut déjà une anomalie de la même valeur haute. 

   ![Anomalies avec la même taille](media/stream-analytics-machine-learning-anomaly-detection/anomalies_with_same_length.png)

## <a name="example-query-to-detect-anomalies"></a>Exemple de requête pour détecter les anomalies 

La requête suivante peut être utilisée pour générer une alerte si une anomalie est détectée.
Lorsque le flux d’entrée n’est pas uniforme, l’étape d’agrégation peut vous aider à le transformer en une série chronologique uniforme. L’exemple utilise AVG, mais le type d’agrégation varie selon le scénario utilisateur. En outre, lorsqu’une série chronologique a des écarts supérieurs à la fenêtre d’agrégation, il n’y aura aucun événement dans la série chronologique pour déclencher la détection d’anomalie (conformément à sémantique de fenêtre glissante). Par conséquent, l’hypothèse d’uniformité est rompue lorsque l’événement suivant arrive. Dans de telles situations, les écarts dans les séries chronologiques doivent être remplis. Une approche possible consiste à prendre le dernier événement dans chaque fenêtre, comme indiqué ci-dessous.

```sql
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
```

## <a name="performance-guidance"></a>Guide sur les performances

* Utilisez six unités de streaming pour des tâches. 
* Envoyez des événements à au moins une seconde d’intervalle.
* Une requête non partitionnée utilisant l’opérateur AnomalyDetection peut produire des résultats avec une latence de calcul d’environ 25 ms en moyenne.
* La latence subie par une requête partitionnée varie légèrement suivant le nombre de partitions, car le nombre de calculs peut alors être plus élevé. Toutefois, la latence est approximativement la même que dans le cas non partitionné pour un total comparable d’événements dans toutes les partitions.
* Lors de la lecture de données en différé, une grande quantité de données est intégrée rapidement. Le traitement de ces données est actuellement plus lent. La latence dans de tels scénarios augmente de façon linéaire avec le nombre de points de données dans la fenêtre plutôt qu’avec la taille de fenêtre ou l’intervalle d’événements. Pour réduire la latence dans les cas en différé, envisagez d’utiliser une taille de fenêtre plus petite. Vous pouvez également envisager de commencer votre projet à partir de l’heure actuelle. Voici quelques exemples de latence dans une requête non partitionnée : 
    - 60 points de données dans la fenêtre de détection peuvent entraîner une latence de 10 secondes avec un débit de 3 Mbits/s. 
    - À 600 points de données, la latence peut atteindre environ 80 secondes avec un débit de 0,4 Mbits/s.

## <a name="limitations-of-the-anomalydetection-operator"></a>Limitations de l’opérateur AnomalyDetection 

* Cet opérateur ne prend actuellement pas en charge la détection des pics et des chutes. Les pics et les chutes sont des changements spontanés ou à court terme dans les séries chronologiques. 

* Cet opérateur ne gère actuellement pas les modèles à caractère saisonnier. Les modèles à caractère saisonnier sont des modèles répétés dans les données. Par exemple, un comportement dans le trafic web différent pendant les week-ends ou des tendances d’achat différentes lors du Black Friday, qui ne sont pas des anomalies, mais des modèles de comportement attendus. 

* Cet opérateur attend de la série chronologique d’entrée qu’elle soit uniforme. Un flux d’événements peut être rendu uniforme par agrégation sur une fenêtre récurrente ou bascule. Dans les scénarios où l’écart entre les événements est toujours inférieur à la fenêtre d’agrégation, une fenêtre bascule est suffisante pour rendre la série chronologique uniforme. Lorsque l’intervalle peut être plus grand, les vides peuvent être remplis en répétant la dernière valeur à l’aide d’une fenêtre récurrente. 

## <a name="references"></a>Références

* [Détection des anomalies – Utilisation de Machine Learning pour détecter les anomalies dans les données de série chronologique](https://blogs.technet.microsoft.com/machinelearning/2014/11/05/anomaly-detection-using-machine-learning-to-detect-abnormalities-in-time-series-data/)
* [API de détection des anomalies Machine Learning](https://docs.microsoft.com/en-gb/azure/machine-learning/machine-learning-apps-anomaly-detection-api)
* [Détection des anomalies de série chronologique](https://msdn.microsoft.com/library/azure/mt775197.aspx)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

