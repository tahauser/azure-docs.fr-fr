---
title: Visualisation et dépannage de travaux Stream Analytics | Microsoft Docs
description: Découvrez comment visualiser un pipeline de tâches Stream Analytics pour le dépannage en libre-service à l’aide de la fonctionnalité de diagramme de diagnostic.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: eae43a6a444514855229af760de6aa1cbec7840a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualisation et dépannage de travaux Stream Analytics
Dans Stream Analytics, comme dans d’autres technologies de cloud, la résolution des problèmes implique parfois d’étudier la raison pour laquelle une tâche ne produit pas la sortie attendue (ou même aucune sortie). Dans cet esprit, Stream Analytics permet de visualiser une tâche de streaming. Il est également utile en tant qu’outil de modélisation pour aider les personnes ayant besoin de documenter tout leur travail.

Dans le volet de visualisation, les entrées sont visibles, ainsi que la requête en cours d’exécution et toutes les sorties configurées. Les problèmes liés à la connectivité ou à la configuration peuvent alors être plus évidents, et il peut également être utile d’afficher une représentation visuelle de votre configuration.

## <a name="using-the-diagnosis-diagram-tool"></a>Utilisation de l’outil de diagramme de diagnostic
Pour accéder à ce visualiseur, cliquez simplement sur le bouton « Diagramme de diagnostic » dans le panneau « Paramètres » de la tâche Stream Analytics.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Chaque entrée et sortie ont un code de couleur pour indiquer l’état actuel de ce composant, comme indiqué ci-dessous.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Lorsque l’utilisateur souhaite consulter les étapes intermédiaires de sa requête pour comprendre les modèles de flux de données au sein d’une tâche, l’outil de visualisation fournit une vue de la répartition des étapes de la requête et de la séquence de flux. Cliquez sur chaque étape de requête pour afficher la section correspondante dans un volet d’édition de requête, comme illustré. 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

