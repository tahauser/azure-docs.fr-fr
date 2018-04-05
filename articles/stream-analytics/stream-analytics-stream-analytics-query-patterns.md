---
title: Exemples de requête pour les modes d’utilisation courants dans Stream Analytics | Microsoft Docs
description: Modèles courants de requêtes Azure Stream Analytics
keywords: exemples de requête
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 6b9a7d00-fbcc-42f6-9cbb-8bbf0bbd3d0e
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/08/2017
ms.author: jeanb
ms.openlocfilehash: 9632a77afff6ba47d6ce80457e02f1f6194362a1
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exemples de requête pour les modes d’utilisation courants dans Stream Analytics
## <a name="introduction"></a>Introduction
Les requêtes dans Azure Stream Analytics sont exprimées dans un langage de requête de type SQL. Ces requêtes sont documentées dans le guide [Stream Analytics query language reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Informations de référence sur le langage de requête Stream Analytics). Cet article décrit les solutions à plusieurs modèles de requête habituels, inspirés de scénarios réels. Il est en cours et mis à jour avec de nouveaux modèles de manière continue.

## <a name="query-example-convert-data-types"></a>Exemple de requête : Convertir des types de données
**Description** : Définir les types des propriétés sur le flux d’entrée.
Par exemple, le poids de la voiture arrive sur le flux d’entrée sous forme de chaîne, et doit être converti en **INT** pour exécuter la fonction **SUM**.

**Entrée**:

| Marque | Temps | Poids |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |"1000" |
| Honda |2015-01-01T00:00:02.0000000Z |"2000" |

**Sortie**:

| Marque | Poids |
| --- | --- |
| Honda |3000 |

**Solution**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explication** : Utilisez une instruction **CAST** dans le champ **Poids** pour spécifier son type de données. Voir la liste des types de données pris en charge dans [Data types (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835065.aspx) (Types de données (Azure Stream Analytics)).

## <a name="query-example-use-likenot-like-to-do-pattern-matching"></a>Exemple de requête : Utiliser Like/Not like pour les critères spéciaux
**Description**: Vérifier qu’une valeur de champ sur l’événement correspond à un certain modèle.
Par exemple, vérifier que le résultat retourne des plaques d’immatriculation qui commencent par A et se terminent par 9.

**Entrée**:

| Marque | LicensePlate | Temps |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Sortie**:

| Marque | LicensePlate | Temps |
| --- | --- | --- |
| Toyota |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Nissan |ABC-369 |2015-01-01T00:00:03.0000000Z |

**Solution**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Explication** : Utilisez l’instruction **LIKE** pour vérifier la valeur du champ **LicensePlate**. Il doit commencer par un A, puis avoir une chaîne de zéro, un ou plusieurs caractères, puis se terminer par un 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exemple de requête : Spécifier la logique pour différentes casses/valeurs (instructions CASE)
**Description** : Fournir un calcul différent pour un champ en fonction de certains critères.
Par exemple, fournir une description de chaîne pour le nombre de voitures de la même marque avec une casse spéciale pour 1.

**Entrée**:

| Marque | Temps |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Sortie**:

| CarsPassed | Temps |
| --- | --- | --- |
| 1 Honda |2015-01-01T00:00:10.0000000Z |
| 2 Toyota |2015-01-01T00:00:10.0000000Z |

**Solution**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explication** : La clause **CASE** permet de fournir un calcul différent en fonction de certains critères (ici, le nombre de voitures dans la fenêtre d’agrégation).

## <a name="query-example-send-data-to-multiple-outputs"></a>Exemple de requête : envoi de données vers plusieurs sorties
**Description** : Envoyer des données à plusieurs cibles de sortie à partir d’un travail unique.
Par exemple, analyser des données relatives à une alerte basée sur un seuil et archiver tous les événements dans le Stockage Blob.

**Entrée**:

| Marque | Temps |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output1**:

| Marque | Temps |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output2**:

| Marque | Temps | Count |
| --- | --- | --- |
| Toyota |2015-01-01T00:00:10.0000000Z |3 |

**Solution**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Explication** : La clause **INTO** indique à Stream Analytics la sortie sur laquelle écrire les données à partir de cette instruction.
La première requête est un transfert des données que nous avons reçues vers une sortie nommée **ArchiveOutput**.
La deuxième requête effectue une agrégation et un filtrage simples, et envoie les résultats vers un système d’alerte en aval.

Notez que vous pouvez également réutiliser les résultats d’expressions de table communes (par exemple avec des instructions **WITH**) dans plusieurs instructions de sortie. Cette option a l’avantage d’ouvrir moins de lecteurs vers la source d’entrée.
Par exemple :  

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-count-unique-values"></a>Exemple de requête : Compter des valeurs uniques
**Description** : Compter le nombre de valeurs de champ uniques qui apparaissent dans le flux au cours d’une fenêtre de temps.
Par exemple, combien de voitures d’une même marque ont franchi le péage dans une fenêtre de temps de deux secondes ?

**Entrée**:

| Marque | Temps |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Honda |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |
| Toyota |2015-01-01T00:00:03.0000000Z |

**Output:**

| CountMake | Temps |
| --- | --- |
| 2 |2015-01-01T00:00:02.000Z |
| 1 |2015-01-01T00:00:04.000Z |

**Solution :**

````
SELECT
     COUNT(DISTINCT Make) AS CountMake,
     System.TIMESTAMP AS TIME
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
````


**Explication :**
**COUNT(DISTINCT Make)** retourne le nombre de valeurs distinctes de la colonne **Marque** dans une fenêtre de temps.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exemple de requête : Déterminer si une valeur a changé
**Description** : Examiner une valeur précédente pour déterminer si elle est différente de la valeur actuelle.
Par exemple, la voiture précédente sur la route à péage est-elle de la même marque que la voiture actuelle ?

**Entrée**:

| Marque | Temps |
| --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Sortie**:

| Marque | Temps |
| --- | --- |
| Toyota |2015-01-01T00:00:02.0000000Z |

**Solution**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explication** : Utilisez **LAG** pour lire le flux d’entrée de l’événement précédent et obtenir la valeur de **Marque**. Ensuite, la comparer à la valeur **Marque** de l’événement en cours, puis générer l’événement si elles sont différentes.

## <a name="query-example-find-the-first-event-in-a-window"></a>Exemple de requête : Rechercher le premier événement dans une fenêtre
**Description** : Rechercher la première voiture dans chaque intervalle de 10 minutes.

**Entrée**:

| LicensePlate | Marque | Temps |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Sortie**:

| LicensePlate | Marque | Temps |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |

**Solution**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

À présent, nous allons changer le problème et rechercher la première voiture d’une marque donnée dans chaque intervalle de 10 minutes.

| LicensePlate | Marque | Temps |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solution**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-the-last-event-in-a-window"></a>Exemple de requête : Rechercher le dernier événement dans une fenêtre
**Description** : Rechercher la dernière voiture dans chaque intervalle de 10 minutes.

**Entrée**:

| LicensePlate | Marque | Temps |
| --- | --- | --- |
| DXE 5291 |Honda |2015-07-27T00:00:05.0000000Z |
| YZK 5704 |Ford |2015-07-27T00:02:17.0000000Z |
| RMV 8282 |Honda |2015-07-27T00:05:01.0000000Z |
| YHN 6970 |Toyota |2015-07-27T00:06:00.0000000Z |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| QYF 9358 |Honda |2015-07-27T00:12:02.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Sortie**:

| LicensePlate | Marque | Temps |
| --- | --- | --- |
| VFE 1616 |Toyota |2015-07-27T00:09:31.0000000Z |
| MDR 6128 |BMW |2015-07-27T00:13:45.0000000Z |

**Solution**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Explication** : La requête comporte deux étapes. La première recherche l’horodatage le plus récent dans les fenêtres de 10 minutes. La deuxième joint les résultats de la première requête avec le flux d’origine pour rechercher les événements qui correspondent aux derniers horodatages dans chaque fenêtre. 

## <a name="query-example-detect-the-absence-of-events"></a>Exemple de requête : Détecter l’absence d’événements
**Description** : Vérifier qu’un flux ne contient aucune valeur correspondant à certains critères.
Par exemple, deux voitures consécutives de la même marque se sont-elles engagées dans la route à péage durant les 90 dernières secondes ?

**Entrée**:

| Marque | LicensePlate | Temps |
| --- | --- | --- |
| Honda |ABC-123 |2015-01-01T00:00:01.0000000Z |
| Honda |AAA-999 |2015-01-01T00:00:02.0000000Z |
| Toyota |DEF-987 |2015-01-01T00:00:03.0000000Z |
| Honda |GHI-345 |2015-01-01T00:00:04.0000000Z |

**Sortie**:

| Marque | Temps | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda |2015-01-01T00:00:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:00:01.0000000Z |

**Solution**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explication** : Utilisez **LAG** pour lire le flux d’entrée de l’événement précédent et obtenir la valeur de **Marque**. La comparer à la valeur **Marque** de l’événement en cours, puis générer l’événement si elles sont identiques. Vous pouvez également utiliser **LAG** pour obtenir des données relatives à la voiture précédente.

## <a name="query-example-detect-the-duration-between-events"></a>Exemple de requête : Détecter la durée entre des événements
**Description** : Rechercher la durée d’un événement donné. Par exemple, sur la base d’un parcours web, déterminer le temps passé sur une fonctionnalité.

**Entrée**:  

| Utilisateur | Fonctionnalité | Événement | Temps |
| --- | --- | --- | --- |
| user@location.com |RightMenu |Démarrer |2015-01-01T00:00:01.0000000Z |
| user@location.com |RightMenu |Terminer |2015-01-01T00:00:08.0000000Z |

**Sortie**:  

| Utilisateur | Fonctionnalité | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**Solution**:

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Explication** : Utilisez la fonction **LAST** pour récupérer la dernière valeur **TEMPS** quand le type d’événement était **Démarrer**. Notez que la fonction **LAST** utilise **PARTITION BY [user]** pour indiquer que le résultat est calculé par utilisateur unique. La requête a un seuil maximal d’une heure pour la différence de temps entre les événements **Démarrer** et **Terminer** **(LIMIT DURATION(hour, 1)**, mais ce seuil est configurable en fonction des besoins.

## <a name="query-example-detect-the-duration-of-a-condition"></a>Exemple de requête : Détecter la durée d’une condition
**Description** : Rechercher la durée pendant laquelle une condition s’est produite.
Par exemple, supposez qu’à la suite d’un bogue, le poids de toutes les voitures est incorrect (supérieur à 20 000 livres). Nous voulons calculer la durée du bogue.

**Entrée**:

| Marque | Temps | Poids |
| --- | --- | --- |
| Honda |2015-01-01T00:00:01.0000000Z |2000 |
| Toyota |2015-01-01T00:00:02.0000000Z |25000 |
| Honda |2015-01-01T00:00:03.0000000Z |26000 |
| Toyota |2015-01-01T00:00:04.0000000Z |25000 |
| Honda |2015-01-01T00:00:05.0000000Z |26000 |
| Toyota |2015-01-01T00:00:06.0000000Z |25000 |
| Honda |2015-01-01T00:00:07.0000000Z |26000 |
| Toyota |2015-01-01T00:00:08.0000000Z |2000 |

**Sortie**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z |2015-01-01T00:00:07.000Z |

**Solution**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Explication** : Utilisez **LAG** pour afficher le flux d’entrée sur 24 heures et recherchez les instances dans lesquelles **StartFault** et **StopFault** sont couvertes par la condition « weight &lt; 20000 » (poids inférieur à 20 000 livres).

## <a name="query-example-fill-missing-values"></a>Exemple de requête : Remplir les valeurs manquantes
**Description** : Pour le flux des événements qui ont des valeurs manquantes, produire un flux d’événements à intervalles réguliers.
Par exemple, générer toutes les cinq secondes un événement qui indique le point de données le plus récemment observé.

**Entrée**:

| t | value |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:01:05" |2 |
| "2014-01-01T06:01:10" |3 |
| "2014-01-01T06:01:15" |4 |
| "2014-01-01T06:01:30" |5. |
| "2014-01-01T06:01:35" |6. |

**Sortie (10 premières lignes)**:

| windowend | lastevent.t | lastevent.value |
| --- | --- | --- |
| 2014-01-01T14:01:00.000Z |2014-01-01T14:01:00.000Z |1 |
| 2014-01-01T14:01:05.000Z |2014-01-01T14:01:05.000Z |2 |
| 2014-01-01T14:01:10.000Z |2014-01-01T14:01:10.000Z |3 |
| 2014-01-01T14:01:15.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:20.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:25.000Z |2014-01-01T14:01:15.000Z |4 |
| 2014-01-01T14:01:30.000Z |2014-01-01T14:01:30.000Z |5. |
| 2014-01-01T14:01:35.000Z |2014-01-01T14:01:35.000Z |6. |
| 2014-01-01T14:01:40.000Z |2014-01-01T14:01:35.000Z |6. |
| 2014-01-01T14:01:45.000Z |2014-01-01T14:01:35.000Z |6. |

**Solution**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Explication** : Cette requête génère des événements toutes les cinq secondes et indique le dernier événement précédemment reçu. La [Fenêtre récurrente](https://msdn.microsoft.com/library/dn835041.aspx "Fenêtre récurrente - Azure Stream Analytics") détermine la période que la requête remonte pour rechercher le dernier événement (300 secondes, dans cet exemple).


## <a name="query-example-correlate-two-event-types-within-the-same-stream"></a>Exemple de requête : mettre en corrélation deux types d’événement dans le même flux
**Description** : nous avons parfois besoin de générer des alertes en fonction de plusieurs types d’événement qui se sont produits au cours d’un intervalle de temps donné.
Par exemple, dans un scénario IoT concernant des fours domestiques, nous souhaitons déclencher une alerte lorsque la température du ventilateur est inférieure à 40 et que la puissance maximale au cours des 3 dernières minutes a été inférieure à 10.

**Entrée**:

| time | deviceId | sensorName | value |
| --- | --- | --- | --- |
| « 2018-01-01T16:01:00 » | « Oven1 » | « temp » |120 |
| « 2018-01-01T16:01:00 » | « Oven1 » | « power » |15 |
| « 2018-01-01T16:02:00 » | « Oven1 » | « temp » |100 |
| « 2018-01-01T16:02:00 » | « Oven1 » | « power » |15 |
| « 2018-01-01T16:03:00 » | « Oven1 » | « temp » |70 |
| « 2018-01-01T16:03:00 » | « Oven1 » | « power » |15 |
| « 2018-01-01T16:04:00 » | « Oven1 » | « temp » |50 |
| « 2018-01-01T16:04:00 » | « Oven1 » | « power » |15 |
| « 2018-01-01T16:05:00 » | « Oven1 » | « temp » |30 |
| « 2018-01-01T16:05:00 » | « Oven1 » | « power » |8 |
| « 2018-01-01T16:06:00 » | « Oven1 » | « temp » |20 |
| « 2018-01-01T16:06:00 » | « Oven1 » | « power » |8 |
| « 2018-01-01T16:07:00 » | « Oven1 » | « temp » |20 |
| « 2018-01-01T16:07:00 » | « Oven1 » | « power » |8 |
| « 2018-01-01T16:08:00 » | « Oven1 » | « temp » |20 |
| « 2018-01-01T16:08:00 » | « Oven1 » | « power » |8 |

**Sortie**:

| eventTime | deviceId | temp | alertMessage | maxPowerDuringLast3mins |
| --- | --- | --- | --- | --- | 
| « 2018-01-01T16:05:00 » | « Oven1 » |30 | « Short circuit heating elements » |15 |
| « 2018-01-01T16:06:00 » | « Oven1 » |20 | « Short circuit heating elements » |15 |
| « 2018-01-01T16:07:00 » | « Oven1 » |20 | « Short circuit heating elements » |15 |

**Solution**:

````
WITH max_power_during_last_3_mins AS (
    SELECT 
        System.TimeStamp AS windowTime,
        deviceId,
        max(value) as maxPower
    FROM
        input TIMESTAMP BY t
    WHERE 
        sensorName = 'power' 
    GROUP BY 
        deviceId, 
        SlidingWindow(minute, 3) 
)

SELECT 
    t1.t AS eventTime,
    t1.deviceId, 
    t1.value AS temp,
    'Short circuit heating elements' as alertMessage,
    t2.maxPower AS maxPowerDuringLast3mins
    
INTO resultsr

FROM input t1 TIMESTAMP BY t
JOIN max_power_during_last_3_mins t2
    ON t1.deviceId = t2.deviceId 
    AND t1.t = t2.windowTime
    AND DATEDIFF(minute,t1,t2) between 0 and 3
    
WHERE
    t1.sensorName = 'temp'
    AND t1.value <= 40
    AND t2.maxPower > 10
````

**Explication** : la première requête `max_power_during_last_3_mins` utilise la [fenêtre glissante](https://msdn.microsoft.com/en-us/azure/stream-analytics/reference/sliding-window-azure-stream-analytics) pour rechercher la valeur maximale du capteur de puissance pour chaque appareil, au cours des 3 dernières minutes. La seconde requête est jointe à la première pour rechercher la valeur de la puissance dans la fenêtre la plus récente, pertinente pour l’événement actuel. Une alerte est alors générée pour l’appareil sous réserve que les conditions soient remplies.


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

