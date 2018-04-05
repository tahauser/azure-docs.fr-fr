---
title: Déboguer les requêtes Azure Stream Analytics à l’aide de SELECT INTO | Microsoft Docs
description: Exemple de requête intermédiaire des données utilisant des instructions SELECT INTO dans Stream Analytics
keywords: ''
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 9952e2cf-b335-4a5c-8f45-8d3e1eda2e20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: f232ba9804886f416ac0941ddc7e602782b6ebe2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="debug-queries-by-using-select-into-statements"></a>Déboguer des requêtes à l’aide des instructions SELECT INTO

Lors du traitement de données en temps réel, savoir à quoi ressemblent les données au milieu de la requête peut être utile. Étant donné que les entrées ou les étapes d’un travail Azure Stream Analytics peuvent être lues plusieurs fois, vous pouvez écrire des instructions SELECT INTO supplémentaires. Cela génère des données intermédiaires dans le stockage et vous permet d’inspecter l’exactitude des données, tout comme le font les *variables espionnes* lorsque vous déboguez un programme.

## <a name="use-select-into-to-check-the-data-stream"></a>Utiliser SELECT INTO pour vérifier le flux de données

L’exemple de requête suivant dans un travail Azure Stream Analytics a un flux d’entrée, deux entrées de données de référence et une sortie pour Stockage Table Azure. La requête joint les données du Event Hub et deux objets blob de référence pour obtenir les informations de nom et de catégorie :

![Exemple de requête SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Notez que le travail est en cours d’exécution, mais aucun événement n’est produit dans la sortie. Dans la vignette **Surveillance**, illustrée ici, vous pouvez voir que l’entrée génère des données, mais vous ne savez pas quelle étape de **JOIN** a causé la suppression de tous les événements.

![Vignette Surveillance](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
Dans ce cas, vous pouvez ajouter quelques instructions SELECT INTO supplémentaires pour « journaliser » les résultats JOIN intermédiaires et les données lues à partir de l’entrée.

Dans cet exemple, nous avons ajouté deux nouvelles « sorties temporaires ». Il peut s’agir du récepteur que vous voulez. Ici, nous utilisons Stockage Azure comme exemple :

![Ajout d’instructions SELECT INTO supplémentaires](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Vous pouvez ensuite réécrire la requête comme suit :

![Requête SELECT INTO réécrite](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

À présent, relancez le travail et laissez-le s’exécuter pendant quelques minutes. Ensuite, interrogez temp1 et temp2 avec Visual Studio Cloud Explorer pour produire les tables suivantes :

**table temp1**
![table temp1 SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**table temp2**
![table temp2 SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Comme vous pouvez le voir, temp1 et temp2 présentent tous deux des données, et la colonne de nom est remplie correctement dans temp2. Toutefois, étant donné qu’il n’y a toujours aucune donnée dans la sortie, quelque chose ne va pas :

![Table output1 SELECT INTO sans aucune donnée](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

En échantillonnant les données, vous pouvez être presque certain que le problème concerne le deuxième JOIN. Vous pouvez télécharger les données de référence à partir de l’objet blob et jeter un coup de œil :

![Table de référence SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Comme vous pouvez le voir, le format du GUID dans ces données de référence est différent du format de la colonne [from] dans temp2. C’est pourquoi les données ne sont pas arrivées dans output1 comme prévu.

Vous pouvez corriger le format de données, les télécharger pour référencer des objets blob, puis réessayer :

![Table temp SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Cette fois, les données dans la sortie sont mises en forme et remplies comme prévu.

![Table finale SELECT INTO](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Obtenir de l'aide

Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

