---
title: Débogage piloté par les données d’Azure Stream Analytics à l’aide du diagramme de travail | Microsoft Docs
description: Résolvez les problèmes liés à votre travail Stream Analytics en utilisant le diagramme de travail et les mesures.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 05/01/2017
ms.author: jeanb
ms.openlocfilehash: 65eeeee7daa22b94074f55defdfd1219049774c9
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Débogage piloté par les données à l’aide du diagramme de travail

Le diagramme de travail dans le panneau **Surveillance** sur le portail Azure peut vous aider à visualiser votre pipeline de travail. Il montre les entrées, les sorties et les étapes de requête. Vous pouvez utiliser le diagramme de travail pour examiner les mesures pour chaque étape, afin d’isoler plus rapidement la source d’un problème lors de la résolution de problèmes.

## <a name="using-the-job-diagram"></a>Utilisation du diagramme de travail

Dans le portail Azure, à partir d’un travail Stream Analytics, sous **SUPPORT + DÉPANNAGE**, sélectionnez **Diagramme de travail** :

![Diagramme de travail avec des mesures - emplacement](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Sélectionnez chaque étape de requête pour afficher la section correspondante dans un volet d’édition de requête. Un graphique de mesures pour l’étape s’affiche dans un volet inférieur de la page.

![Diagramme de travail avec des mesures - travail de base](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Pour afficher les partitions de l’entrée Azure Event Hubs, sélectionnez **...**. Un menu contextuel s’affiche. Vous pouvez également voir la fusion d’entrée.

![Diagramme de travail avec des mesures - étendre une partition](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Pour afficher le graphique de mesures pour une seule partition, sélectionnez le nœud de la partition. Les mesures sont affichées en bas de la page.

![Diagramme de travail avec des mesures - mesures supplémentaires](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Pour afficher le graphique de mesures pour une fusion, sélectionnez le nœud de fusion. Le graphique suivant montre qu’aucun événement n’a été supprimé ou ajusté.

![Diagramme de travail avec des mesures - grille](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Pour afficher les informations relatives à la valeur et à l’heure de la mesure, pointez sur le graphique.

![Diagramme de travail avec des mesures - pointer](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Résoudre les problèmes à l’aide de mesures

La mesure **QueryLastProcessedTime** indique quand une étape spécifique a reçu des données. En examinant la topologie, vous pouvez respecter une séquence inverse depuis le processeur de sortie afin d’identifier l’étape qui ne reçoit pas les données. Si une étape ne reçoit pas de données, passez à l’étape de requête qui précède. Vérifiez si l’étape précédente de la requête dispose d’une fenêtre de temps et si suffisamment de temps s’est écoulé pour afficher les données. (Notez que les fenêtres de temps sont alignées sur l’heure.)
 
Si l’étape de requête précédente est un processeur d’entrée, utilisez les mesures d’entrée afin de répondre plus facilement aux questions ciblées. Elles peuvent vous aider à déterminer si un travail récupère des données à partir de ses sources d’entrée. Si la requête est partitionnée, examinez chaque partition.
 
### <a name="how-much-data-is-being-read"></a>Quelle est la quantité de données lue ?

*   **InputEventsSourcesTotal** correspond au nombre d’unités de données lues. Par exemple, le nombre d’objets blob.
*   **InputEventsTotal** correspond au nombre d’événements lus. Cette mesure est disponible par partition.
*   **InputEventsInBytesTotal** correspond au nombre d’octets lus.
*   **InputEventsLastArrivalTime** est mis à jour avec chaque durée de file d’attente des événements reçus.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>La chronologie progresse-t-elle ? Si des événements réels sont lus, il est possible qu’aucune ponctuation ne soit émise.

*   **InputEventsLastPunctuationTime** indique qu’une ponctuation a été émise afin de maintenir la progression chronologique. Si la ponctuation n’est pas émise, le flux de données peut être bloqué.
 
### <a name="are-there-any-errors-in-the-input"></a>La sortie contient-elle des erreurs ?

*   **InputEventsEventDataNullTotal** correspond au nombre d’événements présentant des données nulles.
*   **InputEventsSerializerErrorsTotal** correspond au nombre d’événements qui n’ont pas pu être désérialisés correctement.
*   **InputEventsDegradedTotal** correspond au nombre d’événements présentant un problème autre que la désérialisation.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Les événements sont-ils supprimés ou modifiés ?

*   **InputEventsEarlyTotal** correspond au nombre d’événements présentant un horodatage d’application antérieur à la limite supérieure.
*   **InputEventsLateTotal** correspond au nombre d’événements présentant un horodatage d’application postérieur à la limite supérieure.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** fournit le nombre d’événements abandonnés avant l’heure de début du travail.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Sommes-nous en retard en matière de lecture des données ?

*   **InputEventsSourcesBackloggedTotal** indique le nombre de messages supplémentaires devant être lus pour les entrées Event Hub et IoT Hub.


## <a name="get-help"></a>Obtenir de l’aide
Pour une assistance supplémentaire, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
