---
title: Introduction aux fonctions de fenêtrage de Stream Analytics | Microsoft Docs
description: Découvrez les trois fonctions de fenêtrage de Stream Analytics (bascule, récurrente, glissante).
keywords: fenêtre bascule, fenêtre glissante, fenêtre récurrente
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: 0d8d8717-5d23-43f0-b475-af078ab4627d
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: 3757834ff816fffc4571aeef8c164c2230c0f7c2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Introduction aux fonctions de fenêtrage de Stream Analytics
Dans de nombreux scénarios de diffusion en continu en temps réel, il est nécessaire de n’effectuer des opérations que sur les données contenues dans des fenêtres temporelles. La prise en charge native des fonctions de fenêtrage est une fonctionnalité essentielle d’Azure Stream Analytics qui déplace le curseur de productivité des développeurs dans la création de tâches complexes de traitement de flux. Stream Analytics permet aux développeurs d’utiliser des fenêtres [**bascules**](https://msdn.microsoft.com/library/dn835055.aspx), [**récurrentes**](https://msdn.microsoft.com/library/dn835041.aspx) et [**glissantes**](https://msdn.microsoft.com/library/dn835051.aspx) pour effectuer des opérations temporelles sur des données de diffusion en continu. Il est important de noter que tous les opérations de [fenêtrage](https://msdn.microsoft.com/library/dn835019.aspx) renvoient les résultats à la **fin** de la fenêtre. La sortie de la fenêtre sera un événement unique qui dépendra de la fonction d’agrégation utilisée. L’événement aura l’horodatage de la fin de la fenêtre ; toutes les fonctions de fenêtrage sont définies avec une longueur fixe. Enfin, il est important de noter que toutes les fonctions de fenêtrage doivent être utilisées dans une clause [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx).

![Concepts des fonctions de fenêtrage de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Fenêtre bascule
Les fonctions de fenêtre bascule sont utilisées pour segmenter un flux de données en segments temporels distincts et exécuter une fonction sur ces segments, comme dans l’exemple ci-dessous. Les principales caractéristiques d’une fenêtre bascule sont les suivantes : elles se répètent, elles ne se chevauchent pas et un événement ne peut pas appartenir à plusieurs fenêtres bascules.

![Introduction aux fonctions de fenêtre bascule de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Fenêtre récurrente
Les fonctions de fenêtre récurrente font des bonds d’une durée fixe dans le temps. Il peut être facile de les considérer comme des fenêtres bascules pouvant se chevaucher : par conséquent, les événements peuvent appartenir au jeu de résultats de plusieurs fenêtres récurrentes. Pour créer une fenêtre récurrente identique à une fenêtre bascule, il suffit de spécifier une taille de bond égale à la taille de la fenêtre. 

![Introduction aux fonctions de fenêtre récurrente de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Fenêtre glissante
Les fonctions de fenêtre glissante, à la différence des fenêtres bascules ou récurrentes, ne génèrent une sortie **que** lorsqu’un événement se produit. Chaque fenêtre a au moins un événement et avance en permanence d’un € (epsilon). Comme pour les fenêtres récurrentes, les événements peuvent appartenir à plusieurs fenêtres glissantes.

![Introduction aux fonctions de fenêtre glissante de Stream Analytics](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Obtenir de l’aide sur les fonctions de fenêtrage
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

