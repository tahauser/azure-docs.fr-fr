---
title: Déboguer Azure Stream Analytics avec des destinataires de hub d’événement | Microsoft Docs
description: Meilleures pratiques pour considérer les groupes de consommateurs Event Hubs dans Stream Analytics.
keywords: limite de hub d’événement, groupe de consommateurs
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: ''
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 48fa5d0274549aa35e67526a758eef1f34198a6a
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="debug-azure-stream-analytics-with-event-hub-receivers"></a>Déboguer Azure Stream Analytics avec des destinataires de hub d’événement

Vous pouvez utiliser Azure Event Hubs dans Azure Stream Analytics pour recevoir ou générer des données à partir d’un travail. Une meilleure pratique pour l’utilisation de Event Hubs consiste à utiliser plusieurs groupes de consommateurs pour garantir l’extensibilité du travail. En effet, le nombre de lecteurs dans le travail Stream Analytics pour une entrée spécifique affecte le nombre de lecteurs dans un groupe de consommateurs donné. Le nombre précis de destinataires repose sur les détails d’implémentation interne de la logique de la topologie d’augmentation de la taille des instances. Le nombre de destinataires n’est pas exposé en externe. Le nombre de lecteurs peut changer au démarrage du travail ou pendant les mises à niveau du travail.

> [!NOTE]
> Lorsque le nombre de lecteurs change lors des mises à niveau du travail, des avertissements temporaires sont journalisés dans des journaux d’audit. Les travaux Stream Analytics récupèrent automatiquement de ces problèmes temporaires.

## <a name="number-of-readers-per-partition-exceeds-event-hubs-limit-of-five"></a>Le nombre de lecteurs par partition dépasse la limite de 5 définie dans Event Hubs.

Voici les scénarios dans lesquels le nombre de lecteurs par partition dépasse la limite de 5 dans Event Hubs :

* Plusieurs instructions SELECT : si vous utilisez plusieurs instructions SELECT qui font référence au **même** Event Hub d’entrée, chaque instruction SELECT entraîne la création d’un destinataire.
* UNION : lorsque vous utilisez une opération UNION, il est possible d’avoir plusieurs entrées qui font référence aux **mêmes** Event Hub et groupe de consommateurs.
* SELF JOIN : lorsque vous utilisez cette opération, il est possible de faire référence plusieurs fois au **même** Event Hub.

## <a name="solution"></a>Solution

Les meilleures pratiques suivantes peuvent aider à atténuer les scénarios dans lesquels le nombre de lecteurs par partition dépasse la limite de 5 dans Event Hubs.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Fractionner votre requête en plusieurs étapes à l’aide d’une clause WITH

Cette clause spécifie un jeu de résultats nommé de façon temporaire qui peut être référencé par une clause FROM de la requête. Définissez la clause WITH dans l’étendue d’exécution d’une instruction SELECT unique.

Par exemple, au lieu de cette requête :

```
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Utilisez cette requête :

```
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Vérifiez que les entrées sont liées à différents groupes de consommateurs.

Pour les requêtes dans lesquelles au moins trois entrées sont connectées au même groupe de consommateurs Event Hubs, créez des groupes de consommateurs distincts. Vous devez dans ce cas créer d’autres entrées Stream Analytics.


## <a name="get-help"></a>Obtenir de l’aide
Pour une assistance supplémentaire, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
