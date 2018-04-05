---
title: Mettre à l’échelle des travaux Stream Analytics pour augmenter le débit | Microsoft Docs
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
ms.openlocfilehash: 2e0487a9e4cd6346312c6817ef2768556cba72ba
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="scale-azure-stream-analytics-jobs-to-increase--throughput"></a>Mettre à l’échelle des travaux Azure Stream Analytics pour augmenter le débit
Cet article vous indique comment régler une requête Stream Analytics pour augmenter le débit des travaux Stream Analytics. Vous pouvez utiliser le guide suivant pour mettre à l’échelle votre travail afin de gérer une charge plus élevée et de bénéficier de davantage de ressources système (par exemple, plus de bande passante, de ressources processeur, de mémoire).
Comme prérequis, vous devrez peut-être consulter les articles suivants :
-   [Comprendre et ajuster les unités de diffusion en continu](stream-analytics-streaming-unit-consumption.md)
-   [Créer des travaux parallélisables](stream-analytics-parallelization.md)


## <a name="case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions"></a>Cas 1 : Votre requête est par définition entièrement parallélisable sur plusieurs partitions d’entrée
Si votre requête est par définition entièrement parallélisable sur plusieurs partitions d’entrée, vous pouvez suivre les étapes suivantes :
1.  Créez une requête massivement parallèle en utilisant le mot clé **PARTITION BY**. Pour plus d’informations, consultez la section Travaux massivement parallèles [dans cette page](stream-analytics-parallelization.md).
2.  En fonction des types de sorties utilisés dans votre requête, certaines sorties peuvent ne pas être parallélisables ou nécessiter une configuration supplémentaire pour être massivement parallèles. Par exemple, les sorties SQL, SQL DW et PowerBI ne sont pas parallélisables. Les sorties sont toujours fusionnées avant l’envoi vers le récepteur de sortie. Les objets blob, les tables, ADLS, Service Bus et Azure Function sont automatiquement parallélisés. La configuration de PartitionKey pour CosmosDB et Event Hub doit correspondre au champ **PARTITION BY** (généralement PartitionId). Pour Event Hub, assurez-vous également que le nombre de partitions d’entrée est égal au nombre de partitions de sortie pour éviter tout croisement entre les partitions. 
3.  Exécutez votre requête avec **6 unités de streaming** (ce qui est la capacité totale d’un seul nœud de calcul) pour mesurer le débit maximal réalisable et, si vous utilisez **GROUP BY**, pour mesurer le nombre de groupes (cardinalité) que le travail peut gérer. Les symptômes généraux qui indiquent que le travail a atteint les limites des ressources système sont les suivants.
    - La métrique % d’utilisation de SU est supérieure à 80 %. Cela indique une utilisation élevée de la mémoire. Les facteurs qui contribuent à l’augmentation de cette métrique sont décrits [ici](stream-analytics-streaming-unit-consumption.md). 
    -   L’horodatage de sortie est en retard par rapport au temps horloge. Selon la logique de votre requête, l’horodatage de sortie peut présenter un décalage logique par rapport au temps horloge. Toutefois, ils doivent avancer à peu près à la même vitesse. Si l’horodatage de sortie est de plus en plus en retard, cela indique que le système est surchargé. Cela peut être le résultat de la limitation du récepteur de sortie en aval ou d’une utilisation élevée du processeur. Comme nous ne fournissons pas de métrique d’utilisation du processeur à ce stade, il peut être difficile de différencier les deux.
        - Si le problème est dû à une limitation du récepteur, vous devrez peut-être augmenter le nombre de partitions de sortie (et également de partitions d’entrée pour que le travail reste entièrement parallélisable), ou augmenter la quantité de ressources du récepteur (par exemple le nombre d’unités de requête pour CosmosDB).
    - Dans le diagramme de travail, il existe une métrique d’événement de backlog par partition pour chaque entrée. Si la métrique d’événement de backlog continue à augmenter, cela indique également que la ressource système est contrainte (en raison de la limitation du récepteur de sortie ou d’une utilisation élevée du processeur).
4.  Une fois que vous avez déterminé les limites d’un travail avec 6 unités de streaming, vous pouvez extrapoler linéairement la capacité de traitement du travail à mesure que vous ajoutez d’autres unités, en partant du principe que vous n’avez aucune asymétrie des données qui rend une partition « sensible ».
>[!Note]
> Choisissez le nombre approprié d’unités de streaming : comme Stream Analytics crée un nœud de traitement pour chaque ensemble de 6 unités de streaming ajouté, il est préférable de faire du nombre de nœuds un diviseur du nombre de partitions d’entrée pour que les partitions puissent être réparties uniformément entre les nœuds.
> Par exemple, vous avez mesuré que votre travail avec 6 unités de streaming peut atteindre une vitesse de traitement de 4 Mo/s et le nombre de partitions d’entrée est 4. Vous pouvez choisir d’exécuter votre travail avec 12 unités de streaming pour atteindre une vitesse de traitement d’environ 8 Mo/s, ou 24 unités de streaming pour atteindre 16 Mo/s. Vous pouvez alors décider quand augmenter le nombre d’unités de streaming du travail jusqu’à quelle valeur, en fonction de votre vitesse d’entrée.



## <a name="case-2---if-your-query-is-not-embarrassingly-parallel"></a>Cas 2 : Votre requête n’est pas massivement parallèle.
Si votre requête n’est pas massivement parallèle, vous pouvez suivre les étapes ci-dessous.
1.  Démarrez avec une requête sans mot clé **PARTITION BY** tout d’abord afin d’éviter la complexité de partitionnement et exécutez-la avec 6 unités de streaming pour mesurer la charge maximale, comme dans le [cas 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions).
2.  Si vous pouvez obtenir votre charge prévue en termes de débit, vous avez terminé. Vous pouvez également choisir de mesurer le même travail exécuté avec 3 unités de streaming et 1 unité de streaming, pour déterminer le nombre minimal d’unités de streaming qui fonctionne pour votre scénario.
3.  Si vous ne pouvez pas obtenir le débit souhaité, essayez de diviser votre requête en plusieurs étapes, si possible et si elle n’est pas déjà constituée de plusieurs étapes, et allouez jusqu’à 6 unités de streaming pour chaque étape de la requête. Par exemple, si vous avez 3 étapes, allouez 18 unités de streaming dans l’option « Échelle ».
4.  Lors de l’exécution d’un tel travail, Stream Analytics place chaque étape sur son propre nœud avec des ressources de 6 unités de streaming dédiées. 
5.  Si vous n’avez pas encore atteint votre cible de charge, vous pouvez essayer d’utiliser **PARTITION BY** en commençant par les étapes les plus proches de l’entrée. Pour l’opérateur **GROUP BY** qui n’est peut-être pas naturellement configurable en partition, vous pouvez utiliser le modèle d’agrégation global/local pour effectuer une opération **GROUP BY** partitionnée suivie d’une opération **GROUP BY** non partitionnée. Par exemple, vous souhaitez compter le nombre de voitures qui passent par chaque gare de péage toutes les 3 minutes et le volume des données dépasse ce qui peut être géré par 6 unités de streaming.

Requête :

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Dans la requête ci-dessus, vous comptez les voitures par gare de péage par partition, puis additionnez le nombre de toutes les partitions.

Une fois le partitionnement effectué, pour chaque partition de l’étape, allouez jusqu’à 6 unités de streaming, chaque partition avec 6 unités de streaming représentant le maximum, pour pouvoir placer chaque partition sur son propre nœud de traitement.

> [!Note]
> Si votre requête ne peut pas être partitionnée, l’ajout d’unités de streaming supplémentaires dans une requête en plusieurs étapes peut ne pas toujours améliorer le débit. Une façon d’augmenter les performances consiste à réduire le volume sur les étapes initiales à l’aide du modèle d’agrégation global/local, comme décrit ci-dessus à l’étape 5.

## <a name="case-3---you-are-running-lots-of-independent-queries-in-a-job"></a>Cas 3 : Vous exécutez un grand nombre de requêtes indépendantes dans un travail.
Pour certains cas d’usage d’éditeurs de logiciels indépendants, où il est plus rentable de traiter les données de plusieurs locataires dans un même travail, vous risquez de finir par exécuter un certain nombre (par exemple 20) de requêtes indépendantes dans un seul travail en utilisant des entrées et sorties distinctes pour chaque locataire. L’hypothèse est que la charge de chaque sous-requête de ce type est relativement faible. Dans ce cas, vous pouvez suivre les étapes ci-dessous.
1.  Dans ce cas, n’utilisez pas **PARTITION BY** dans la requête
2.  Réduisez le nombre de partitions d’entrée à la valeur la plus faible possible de 2 si vous utilisez Event Hub.
3.  Exécutez la requête avec 6 unités de streaming. Avec la charge attendue pour chaque sous-requête, ajoutez autant de ces sous-requêtes que possible, jusqu’à ce que le travail atteigne les limites des ressources système. Reportez-vous au [cas 1](#case-1--your-query-is-inherently-fully-parallelizable-across-input-partitions) pour connaître les symptômes de cette situation.
4.  Une fois que vous atteignez la limite des sous-requêtes mesurée ci-dessus, commencez à ajouter la sous-requête à un nouveau travail. Le nombre de travaux à exécuter en fonction du nombre de requêtes indépendantes doit être assez linéaire, en partant du principe que vous n’avez aucune asymétrie de charge. Vous pouvez ensuite prévoir combien de travaux avec 6 unités de streaming vous devez exécuter en fonction du nombre de locataires que vous souhaitez traiter.
5.  Lors de l’utilisation de la jointure des données de référence avec les requêtes de ce type, vous devez unir les entrées avant la jointure avec les mêmes données de référence, puis fractionner les événements si nécessaire. Dans le cas contraire, chaque jointure des données de référence conserve une copie des données de référence en mémoire, ce qui risque d’augmenter l’utilisation de la mémoire inutilement.

> [!Note] 
> Combien de locataires à placer dans chaque travail ?
> Ce modèle de requête comporte souvent un grand nombre de sous-requêtes et aboutit à une topologie très volumineuse et complexe. Le contrôleur du travail peut ne pas être en mesure de gérer une topologie si volumineuse. En règle générale, restez en dessous de 40 locataires pour un travail avec 1 unité de streaming, et 60 locataires pour des travaux avec 3 et 6 unités de streaming. Quand vous dépassez la capacité du contrôleur, le travail ne démarre pas correctement.


## <a name="an-example-of-stream-analytics-throughput-at-scale"></a>Exemple de débit Stream Analytics à l’échelle
Pour vous aider à comprendre comment les travaux Stream Analytics sont mis à l’échelle, nous avons effectué une expérience basée sur l’entrée d’un appareil Raspberry Pi. Cette expérience nous permet d’observer l’effet de plusieurs unités SU et partitions sur le débit.

Dans ce scénario, l’appareil envoie des données de capteur (clients) à un concentrateur Event Hub. Stream Analytics traite les données et envoie une alerte ou des statistiques en tant que sortie à un autre concentrateur Event Hub. 

Le client envoie des données de capteur au format JSON. La sortie des données est également au format JSON. Voici à quoi ressemblent les données :

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

La requête suivante permet d’envoyer une alerte quand lorsqu’une lumière est éteinte :

    SELECT AVG(lght), "LightOff" as AlertText
    FROM input TIMESTAMP BY devicetime 
    PARTITION BY PartitionID
    WHERE lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Mesurer le débit

Dans ce contexte, le débit correspond à la quantité de données d’entrée traitées par Stream Analytics au cours d’une durée fixe. (Nous avons mesuré le débit pendant 10 minutes.) Pour obtenir le meilleur débit de traitement pour les données d’entrée, l’entrée de flux de données et la requête ont été partitionnées. Nous avons inclus **COUNT()** dans la requête pour mesurer le nombre d’événements d’entrée traités. Pour vous assurer que le travail n’attende pas simplement les événements d’entrée, chaque partition du concentrateur Event Hub d’entrée a été préchargée avec environ 300 Mo de données d’entrée.

Le tableau suivant montre les résultats que nous avons observés lorsque nous avons augmenté le nombre d’unités SU et le nombre de partitions correspondant dans les concentrateurs d’événements.  

<table border="1">
<tr><th>Partitions d'entrée</th><th>Partitions de sortie</th><th>Unités de diffusion en continu</th><th>Débit soutenu
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06 Mo/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06 Mo/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32 Mo/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67 Mo/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27 Mo/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69 Mo/s</td>
</tr>
</table>

Et le graphique suivant présente une visualisation de la relation entre les unités SU et le débit.

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

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

