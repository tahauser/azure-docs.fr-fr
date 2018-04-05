---
title: Présentation de la surveillance des travaux Stream Analytics | Microsoft Docs
description: Présentation de la surveillance des tâches Stream Analytics
keywords: surveillance des requêtes
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 5f5cc00f-4a7b-491e-89e1-dbafea46d399
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: cbfbc653deccbd215a404479100ee5cae860cb3f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Présentation de la surveillance des tâches Stream Analytics et des requêtes

## <a name="introduction-the-monitor-page"></a>Introduction : page de surveillance
Le portail Azure affiche les mesures de performances clés qui peuvent servir à surveiller et résoudre les problèmes affectant les performances de vos requêtes et de vos travaux. Pour voir ces métriques, accédez au travail Stream Analytics dont vous souhaitez consulter les métriques et affichez la section **Surveillance** dans la page Vue d’ensemble.  

![Lien Surveillance](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Une fenêtre s’affiche comme suit :

![Tableau de bord de surveillance des tâches](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Mesures disponibles pour Stream Analytics
| Métrique                 | Définition                               |
| ---------------------- | ---------------------------------------- |
| Utilisation de % d’unités de diffusion       | Utilisation des unités de diffusion affectées à une tâche à partir de l’onglet Mettre à l’échelle de la tâche. Si cet indicateur atteint 80 % ou plus, il est fort probable que le traitement des événements soit retardé ou arrêté. |
| Événements d’entrée           | Quantité de données reçues par le travail Stream Analytics, en nombre d’événements. Cela permet de valider que les événements sont envoyés à la source d’entrée. |
| Événements de sortie          | Quantité de données envoyées par le travail Stream Analytics à la cible de sortie, en nombre d’événements. |
| Événements non ordonnés    | Nombre d’événements reçus dans le désordre qui ont été supprimés ou dont l’horodatage a été réglé, en fonction de la stratégie de classement des événements. Cela peut être affecté par la configuration du paramètre de la plage de tolérance pour les événements en désordre. |
| Erreurs de conversion de données | Nombre d’erreurs de conversion de données générées par une tâche Stream Analytics. |
| Erreurs d’exécution         | Nombre total d’erreurs liées au traitement des requêtes (à l’exception des erreurs détectées lors de l’ingestion d’événements ou de la génération de résultats) |
| Événements d’entrée tardifs      | Nombre d’événements qui arrivent en retard de la source qui ont été supprimés ou dont l’horodatage a été réglé, en fonction de la configuration de la stratégie de classement des événements du paramètre de la plage de tolérance d’arrivée tardive. |
| Requêtes de fonction      | Nombre d’appels à la fonction Azure Machine Learning (le cas échéant). |
| Requêtes de fonction ayant échoué | Nombre d’appels à la fonction Azure Machine Learning ayant échoué (le cas échéant). |
| Événements de fonction        | Nombre d’événements envoyés à la fonction Azure Machine Learning (le cas échéant). |
| Octets des événements d’entrée      | Quantité de données reçues par le travail Stream Analytics, en octets. Cela permet de valider que les événements sont envoyés à la source d’entrée. |


## <a name="customizing-monitoring-in-the-azure-portal"></a>Personnalisation de la surveillance dans le portail Azure
Vous pouvez régler le type de graphique, les mesures affichées et la période dans les paramètres Modifier le graphique. Pour plus d’informations, consultez [Personnalisation de la surveillance](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Graphique représentant le temps de surveillance des requêtes](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Dernière sortie
Un autre point de données intéressant pour surveiller votre travail est l’heure de la dernière sortie, indiquée dans la page Vue d’ensemble.
Cette heure correspond à l’heure d’application (c’est-à-dire l’heure donnée via l’horodatage dans les données d’événement) de la dernière sortie de votre travail.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

