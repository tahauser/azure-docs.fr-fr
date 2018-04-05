---
title: Profiter de la parallélisation de requête dans Azure Stream Analytics| Microsoft Docs
description: Découvrez comment mettre à l’échelle des travaux Stream Analytics en configurant des partitions d’entrée, en réglant la définition de requête et en configurant les unités de diffusion en continu d’un travail.
keywords: diffusion en continu de données, traitement de données de diffusion en continu, régler l’analyse
services: stream-analytics
documentationcenter: ''
author: JSeb225
manager: ryanw
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeanb
ms.openlocfilehash: eb19a9b4e92e7007f64ae7b593663be6a47a7a4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="leverage-query-parallelization-in-azure-stream-analytics"></a>Profiter de la parallélisation de requête dans Azure Stream Analytics
Cet article explique comment tirer parti de la parallélisation dans Azure Stream Analytics. Vous découvrez comment mettre à l’échelle des travaux Stream Analytics en configurant des partitions d’entrée et en réglant la définition de requête Analytics.
Comme prérequis, vous pouvez vous familiariser avec la notion d’unité de streaming décrite dans [Comprendre et ajuster les unités de streaming](stream-analytics-streaming-unit-consumption.md).

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quelles sont les parties d’un travail Stream Analytics ?
La définition d’une tâche Stream Analytics se compose d’entrées, d’une requête et d’une sortie. Les entrées correspondent à l’emplacement où le travail lit le flux de données. La requête permet de transformer le flux d’entrée de données, et la sortie correspond à l’emplacement où le travail envoie ses résultats.  

Un travail nécessite au moins une source d’entrée pour la diffusion de données en continu. La source d’entrée de flux de données peut être stockée dans un concentrateur Azure Event Hub ou dans un stockage d’objets blob Azure. Pour plus d’informations, consultez [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md) et [Prise en main de l’utilisation d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-sources-and-sinks"></a>Partitions dans les sources et récepteurs
La mise à l’échelle d’un travail Stream Analytics tire parti des partitions dans l’entrée ou la sortie. Le partitionnement vous permet de répartir les données en sous-ensembles basés sur une clé de partition. Un processus qui consomme les données (par exemple, un travail Stream Analytics) peut consommer et écrire différentes partitions en parallèle, ce qui augmente le débit. 

### <a name="inputs"></a>Entrées
Toutes les entrées Azure Stream Analytics peuvent tirer parti du partitionnement :
-   EventHub (nécessité de définir la clé de partition explicitement)
-   IoT Hub (nécessité de définir la clé de partition explicitement)
-   Stockage d'objets blob

### <a name="outputs"></a>Outputs

Quand vous utilisez Stream Analytics, vous pouvez tirer parti du partitionnement dans les sorties :
-   Azure Data Lake Storage
-   Azure Functions
-   table Azure
-   Stockage d'objets blob
-   CosmosDB (nécessité de définir la clé de partition explicitement)
-   EventHub (nécessité de définir la clé de partition explicitement)
-   IoT Hub (nécessité de définir la clé de partition explicitement)
-   Service Bus

Les sorties PowerBI, SQL et SQL Data Warehouse ne prennent pas en charge le partitionnement. Toutefois, vous pouvez toujours partitionner l’entrée comme décrit dans [cette section](#multi-step-query-with-a-grouping-key) 

Pour plus d’informations sur les partitions, consultez les articles suivants :

* [Vue d’ensemble des fonctionnalités des concentrateurs d’événements](../event-hubs/event-hubs-features.md#partitions)
* [Partitionnement des données](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="embarrassingly-parallel-jobs"></a>Travaux massivement parallèles
Un travail *massivement parallèle* est le scénario le plus évolutif d’Azure Stream Analytics. Elle permet de connecter une partition de l’entrée à une instance de la requête, puis de connecter celle-ci à une partition de la sortie. Ce parallélisme comporte les exigences suivantes :

1. Si votre logique de requête dépend de la clé qui est actuellement traitée par la même instance de requête, vous devez vous assurer que les événements atteignent la même partition de votre entrée. Pour les concentrateurs d’événements, cela signifie que les données d’événement doivent posséder la valeur **PartitionKey** définie. Par ailleurs, vous pouvez utiliser des expéditeurs partitionnés. Pour le stockage d’objets blob, cela signifie que les événements sont envoyés vers le même dossier de partition. Si votre logique de requête ne requiert pas la même clé pour être traitée par la même instance de requête, vous pouvez ignorer cette condition. Un exemple de cette logique serait une requête simple du type select/project/filter.  

2. Une fois les données disposées dans l’entrée, vous devez vérifier que votre requête est partitionnée. Vous devez utiliser **PARTITION BY** à toutes les étapes. Les étapes multiples sont autorisées, mais elles doivent être partitionnées à l’aide de la même clé. Pour le moment, la clé de partitionnement doit être définie sur **PartitionId** afin que le travail soit entièrement parallèle.  

3. La plupart de nos sorties peuvent tirer parti du partitionnement mais, si vous utilisez un type de sortie qui ne prend pas en charge le partitionnement, votre travail n’est pas totalement parallèle. Reportez-vous à la [section relative aux sorties](#Outputs) pour plus d’informations.

4. Le nombre de partitions d’entrée doit être égal à celui des partitions de sortie. Actuellement, la sortie du stockage d’objets blob ne prend pas en charge les partitions, mais cela ne pose pas de problèmes, car elle hérite du schéma de partitionnement de la requête en amont. Voici des exemples de valeurs de partition qui permettent la création d’un travail entièrement parallèle :  

   * 8 partitions d’entrée de concentrateur Event Hub et 8 partitions de sortie de concentrateur Event Hub
   * 8 partitions d’entrée de concentrateur Event Hub et une sortie de stockage d’objets blob  
   * 8 partitions d’entrée de stockage d’objets blob et une sortie de stockage d’objets blob  
   * 8 partitions d’entrée de stockage d’objets blob et 8 partitions de sortie de concentrateur Event Hub  

Les sections ci-après présentent quelques exemples de parallélisme massif.

### <a name="simple-query"></a>Requête simple

* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : concentrateur Event Hub avec 8 partitions

Requête :

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Cette requête est un filtre simple. Par conséquent, nous n’avons pas à nous préoccuper du partitionnement de l’entrée qui est envoyée au concentrateur Event Hub. Notez que la requête inclut **PARTITION BY PartitionId**. Elle répond donc à l’exigence n°2 indiquée précédemment. Pour la sortie, nous devons configurer la sortie du hub d’événements dans le travail afin que la clé de partition ait la valeur **PartitionId**. La dernière vérification consiste à s’assurer que le nombre de partitions d’entrée est égal au nombre de partitions de sortie.

### <a name="query-with-a-grouping-key"></a>Requête avec clé de regroupement

* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : stockage d’objets blob

Requête :

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cette requête comporte une clé de regroupement. Par conséquent, les événements regroupés doivent être envoyés à la même partition Event Hub. Étant donné que, dans cet exemple, le regroupement est effectué selon TollBoothID, nous devons vérifier que TollBoothID est utilisé comme clé de partition quand les événements sont envoyés à la partition Event Hub. Ensuite, dans ASA, nous pouvons utiliser **PARTITION BY PartitionId** pour hériter de ce schéma de partition et activer la parallélisation complète. Étant donné que la sortie est un stockage d’objets blob, nous n’avons pas à nous soucier de la configuration d’une valeur de clé de partition, conformément à l’exigence n°4.

## <a name="example-of-scenarios-that-are-not-embarrassingly-parallel"></a>Exemples de scénarios qui n’impliquent *pas* un parallélisme massif

Dans la section précédente, nous vous avons présenté certains scénarios impliquant un parallélisme massif. Dans cette section, nous étudions des cas dans lesquels toutes les exigences ne sont pas remplies pour un parallélisme massif. 

### <a name="mismatched-partition-count"></a>Nombre de partitions d’entrée et de sortie différent
* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : concentrateur Event Hub avec 32 partitions

Dans ce cas, le type de requête importe peu. Si le nombre de partitions d’entrée ne correspond pas au nombre de partitions de sortie, la topologie n’est pas massivement parallèle. Toutefois, nous pouvons encore obtenir un certain niveau de parallélisation.

### <a name="query-using-non-partitioned-output"></a>Requête avec une sortie non partitionnée
* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : Power BI

Pour le moment, la sortie Power BI ne prend pas en charge le partitionnement. Par conséquent, ce scénario n’est pas de type massivement parallèle.

### <a name="multi-step-query-with-different-partition-by-values"></a>Requête à plusieurs étapes avec différentes valeurs PARTITION BY
* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : concentrateur Event Hub avec 8 partitions

Requête :

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Comme vous pouvez le voir, la deuxième étape utilise **TollBoothId** comme clé de partitionnement. Cette étape n’est pas la même que la première. Nous devons donc apporter quelques modifications. 

Les exemples précédents décrivent des travaux Stream Analytics qui respectent (ou pas) une topologie de type massivement parallèle. S’ils la respectent, ils présentent alors le potentiel pour une mise à l’échelle maximale. Pour les travaux qui ne correspondent pas à l’un de ces profils, des conseils de mise à l’échelle seront disponibles dans les futures mises à jour. Pour le moment, suivez les instructions générales indiquées dans les sections suivantes.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcul du nombre maximum d'unités de diffusion en continu pour un travail
Le nombre total d'unités de diffusion en continu qui peut être utilisé par un travail Stream Analytics varie selon le nombre d'étapes de la requête définie pour le travail et le nombre de partitions pour chaque étape.

### <a name="steps-in-a-query"></a>Étapes dans une requête
Une requête peut avoir une ou plusieurs étapes. Chaque étape est une sous-requête définie par le mot-clé **WITH**. La requête qui se trouve en dehors du mot-clé **WITH** (une seule requête) est également comptabilisée comme une étape, par exemple, l’instruction **SELECT** dans la requête suivante :

Requête :

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Cette requête compte deux étapes.

> [!NOTE]
> Cette requête est approfondie plus loin dans cet article.
>  

### <a name="partition-a-step"></a>Partitionnement d'une étape
Les conditions suivantes doivent être respectées pour procéder au partitionnement d'une étape :

* La source d'entrée doit être partitionnée. 
* L’instruction **SELECT** de la requête doit lire à partir d’une source d’entrée partitionnée.
* La requête de l’étape doit contenir le mot clé **PARTITION BY**.

Lorsqu’une requête est partitionnée, les événements d’entrée sont traités et agrégés dans des groupes de partition distincts, et les événements de sortie sont générés pour chacun des groupes. Si vous souhaitez procéder à un agrégat combiné, vous devez créer une deuxième étape non partitionnée à agréger.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcul des unités de diffusion en continu maximum pour un travail
Toutes les étapes non partitionnées ensemble peuvent être mises à l’échelle jusqu’à atteindre six unités SU par travail Stream Analytics. En outre, vous pouvez ajouter 6 unités de streaming pour chaque partition dans une étape partitionnée.
Vous pouvez voir quelques **exemples** dans le tableau ci-dessous.

| Requête                                               | Nombre d’unités SU maximal pour le travail |
| --------------------------------------------------- | ------------------- |
| <ul><li>La requête contient une étape.</li><li>L'étape n'est pas partitionnée.</li></ul> | 6. |
| <ul><li>Le flux de données d’entrée est partitionné par 16.</li><li>La requête contient une étape.</li><li>L'étape est partitionnée.</li></ul> | 96 (6 * 16 partitions) |
| <ul><li>La requête contient 2 étapes.</li><li>Aucune des étapes n'est partitionnée.</li></ul> | 6. |
| <ul><li>Le flux de données d'entrée est partitionné par 3.</li><li>La requête contient 2 étapes. L'étape d'entrée est partitionnée et la deuxième étape ne l'est pas.</li><li>L’instruction <strong>SELECT</strong> lit dans l’entrée partitionnée.</li></ul> | 24 (18 pour les étapes partitionnées + 6 pour les étapes non partitionnées) |

### <a name="examples-of-scaling"></a>Exemples de mise à l’échelle

La requête suivante calcule le nombre de voitures, dans une fenêtre de trois minutes, qui traversent un poste de péage pourvu de trois cabines de péage. Cette requête peut être mise à l’échelle jusqu’à six unités SU.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Pour utiliser plus d’unités SU pour la requête, le flux de données d’entrée et la requête doivent être partitionnés. Comme la partition de flux de données est définie sur 3, la requête modifiée suivante peut être mise à l’échelle jusqu’à compter 18 unités SU :

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Lorsqu'une requête est partitionnée, les événements d'entrée sont traités et agrégées dans des groupes de partition distincts. Les événements de sortie sont également générés pour chacun des groupes. Le partitionnement peut provoquer des résultats inattendus si le champ **GROUP BY** n’est pas la clé de partition dans le flux de données d’entrée. Par exemple, dans la requête précédente, le champ **TollBoothId** n’est pas la clé de partition **d’Input1**. Le résultat est le suivant : les données de la cabine de péage « TollBooth 1 » peuvent être réparties dans plusieurs partitions.

Chaque partition **Input1** est traitée séparément par Stream Analytics. En conséquence, plusieurs enregistrements du nombre de voitures pour la même cabine de péage sont créés dans la même fenêtre bascule. Si la clé de partition d’entrée ne peut pas être modifiée, ce problème peut être résolu en ajoutant une étape non partitionnée pour agréger des valeurs sur plusieurs partitions, comme dans l’exemple suivant :

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Cette requête peut être mise à l’échelle jusqu’à comporter 24 unités SU.

> [!NOTE]
> Si vous joignez deux flux de données, assurez-vous qu’ils sont partitionnés par la clé de partition de la colonne que vous utilisez pour créer les jointures. Assurez-vous également d’avoir le même nombre de partitions dans les deux flux de données.
> 
> 





## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

