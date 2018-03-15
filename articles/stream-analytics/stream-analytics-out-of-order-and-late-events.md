---
title: "Traitement de l’ordre et du retard des événements avec Azure Stream Analytics | Microsoft Docs"
description: "Découvrez le fonctionnement de Stream Analytics avec des événements en retard ou en désordre dans les flux de données."
keywords: "Événements en désordre, en retard"
documentationcenter: 
services: stream-analytics
author: jseb225
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb
ms.openlocfilehash: 6478d577c52ffa23c3149c8213f182eaa1e466bd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="azure-stream-analytics-event-order-considerations"></a>Considérations relatives à l’ordre des événements Azure Stream Analytics

## <a name="arrival-time-and-application-time"></a>Heure d’arrivée et heure de l’application

Dans un flux de données temporel d’événements, chaque événement est horodaté. Azure Stream Analytics affecte un horodatage à chaque événement à l’aide de l’heure d’arrivée ou de l’heure de l’application. La colonne **System.Timestamp** indique l’horodatage affecté à l’événement. 

Une heure d’arrivée est affectée à la source d’entrée quand l’événement atteint la source. Vous pouvez accéder à l’heure d’arrivée à l’aide de la propriété **EventEnqueuedTime** pour l’entrée du hub d’événements et de la propriété [BlobProperties.LastModified](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) pour l’entrée d’objet blob. 

L’heure de l’application est affectée quand l’événement est généré et qu’il fait partie de la charge utile. Pour traiter les événements selon l’heure de l’application, utilisez la clause **Timestamp by** dans la requête select. En l’absence de la clause **Timestamp by**, les événements sont traités par heure d’arrivée. 

Azure Stream Analytics génère un résultat selon l’ordre de l’horodatage et fournit des paramètres pour traiter les données en désordre.


## <a name="handling-of-multiple-streams"></a>Gestion de plusieurs flux

Une tâche Azure Stream Analytics combine les événements de plusieurs chronologies dans les cas suivants :

* Génération d’une sortie à partir de plusieurs partitions. Les requêtes qui n’ont pas de clause **Partition by PartitionId** explicite doivent combiner les événements de toutes les partitions.
* Union de plusieurs sources d’entrée différentes.
* Jointure de sources d’entrée.

Dans les scénarios où plusieurs chronologies sont combinées, Azure Stream Analytics produit un résultat pour un horodatage *t1* une fois seulement que toutes les sources combinées arrivent au moins au moment *t1*. Par exemple, supposons que la requête lit une partition de hub d’événements qui a deux partitions. Une des partitions, *P1*, a des événements jusqu’au moment *t1*. L’autre partition, *P2*, a des événements jusqu’au moment *t1 + x*. Dans ce cas, la sortie est produite jusqu’au moment *t1*. Toutefois, s’il y a une clause **Partition by PartitionId** explicite, les deux partitions progressent indépendamment.

Le paramètre de tolérance d’arrivée tardive est utilisé pour gérer l’absence de données dans certaines partitions.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Configuration de la tolérance d’arrivée tardive et de la tolérance de désordre
Les flux d’entrée qui ne sont pas dans l’ordre sont :
* Triés (et par conséquent retardés)
* Ajustés par le système, en fonction de la stratégie spécifiée par l’utilisateur

Stream Analytics tolère les événements tardifs et en désordre en cas de traitement selon l’heure de l’application. Les paramètres suivants sont disponibles dans l’option **Ordre des événements** dans le portail Azure : 

![Traitement des événements Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

### <a name="late-arrival-tolerance"></a>Tolérance d’arrivée tardive
La tolérance d’arrivée tardive est applicable uniquement en cas de traitement par heure de l’application. Sinon, le paramètre est ignoré.

La tolérance d’arrivée tardive est la différence maximale entre l’heure d’arrivée et l’heure de l’application. Si un événement arrive après la tolérance d’arrivée tardive (par exemple, heure de l’application *app_t* < heure d’arrivée *arr_t* - tolérance de la stratégie d’arrivée tardive *late_t*), l’événement est ajusté au maximum de la tolérance d’arrivée tardive (*arr_t* - *late_t*). La fenêtre d’arrivée tardive correspond au délai maximal entre la génération de l’événement et sa réception à la source d’entrée. 

Quand plusieurs partitions du même flux d’entrée ou de plusieurs flux d’entrée sont combinées, la tolérance d’arrivée tardive correspond au délai maximal d’attente de nouvelles données pour chaque partition. 

L’ajustement en fonction de la tolérance d’arrivée tardive a lieu en premier. L’ajustement en fonction de la tolérance de désordre a lieu ensuite. La colonne **System.Timestamp** indique l’horodatage final affecté à l’événement.

### <a name="out-of-order-tolerance"></a>Tolérance de désordre
Les événements qui arrivent dans le désordre, mais dans la fenêtre de tolérance de désordre définie sont réorganisés selon l’horodatage. Les événements qui arrivent après la fenêtre de tolérance sont :
* **Ajustés** : les événements sont ajustés de façon à ce qu’ils semblent arrivés à l’heure la plus récente acceptable. 
* **Supprimés** : ignorés.

Quand Stream Analytics réorganise les événements reçus dans la fenêtre de tolérance de désordre, la sortie de la requête est retardée par la fenêtre de tolérance de désordre.

### <a name="early-events"></a>Événements précoces
Lors du traitement selon l’heure de l’application, les événements dont l’heure de l’application est en avance de plus de 5 minutes sur leur heure d’arrivée sont supprimés ou ajustés en fonction de l’option de configuration sélectionnée.

### <a name="example"></a>Exemple

* Tolérance d’arrivée tardive = 10 minutes<br/>
* Tolérance de désordre = 3 minutes<br/>
* Traitement selon l’heure de l’application<br/>
* Événements :
   1. **Heure de l’application** = 00:00:00, **Heure d’arrivée** = 00:10:01, **System.Timestamp** = 00:00:01, ajusté car (**Heure d’arrivée - Heure de l’application**) est supérieur à la tolérance d’arrivée tardive.
   2. **Heure de l’application** = 00:00:01, **Heure d’arrivée** = 00:10:01, **System.Timestamp** = 00:00:01, non ajusté parce que l’heure de l’application est comprise dans la fenêtre d’arrivée tardive.
   3. **Heure de l’application** = 00:10:00, **Heure d’arrivée** = 00:10:02, **System.Timestamp** = 00:10:00, non ajusté parce que l’heure de l’application est comprise dans la fenêtre d’arrivée tardive.
   4. **Heure de l’application** = 00:09:00, **Heure d’arrivée** = 00:10:03, **System.Timestamp** = 00:09:00, accepté avec l’horodatage d’origine parce que l’heure de l’application est comprise dans la tolérance de désordre.
   5. **Heure de l’application** = 00:06:00, **Heure d’arrivée** = 00:10:04, **System.Timestamp** = 00:07:00, ajusté parce que l’heure de l’application est antérieure à la tolérance de désordre.

### <a name="practical-considerations"></a>Considérations d’ordre pratique
Comme mentionné précédemment, la tolérance d’arrivée tardive est la différence maximale entre l’heure de l’application et l’heure d’arrivée. En cas de traitement par heure d’application, les événements qui ont lieu après la tolérance d’arrivée tardive configurée sont ajustés avant l’application du paramètre de tolérance de désordre. De cette façon, le désordre réel est la valeur minimale de la tolérance d’arrivée tardive et de la tolérance de désordre.

Les raisons expliquant des événements en désordre dans un flux sont notamment les suivantes :
* Des variations d’horloges entre les expéditeurs.
* Une latence variable entre l’expéditeur et la source de l’événement d’entrée.

Les raisons expliquant une arrivée tardive sont notamment les suivantes :
* Les expéditeurs regroupent les événements par lot et les envoient pour un intervalle ultérieur, après l’intervalle.
* Il y a une latence entre l’envoi de l’événement par l’expéditeur et sa réception à la source d’entrée.

Quand vous configurez la tolérance d’arrivée tardive et la tolérance de désordre pour une tâche spécifique, l’exactitude, les exigences de latence et les facteurs précédents doivent être pris en compte.

Voici quelques exemples.

#### <a name="example-1"></a>Exemple 1 
La requête a une clause **Partition by PartitionId**. Dans une partition unique, les événements sont envoyés via les méthodes d’envoi synchrone. Les méthodes d’envoi synchrone sont bloquées jusqu’à ce que les événements soient envoyés.

Dans ce cas, le désordre est égal à zéro, car les événements sont envoyés dans l’ordre avec confirmation explicite avant l’envoi de l’événement suivant. L’arrivée tardive est le délai maximal entre la génération de l’événement et l’envoi de l’événement, plus la latence maximale entre l’expéditeur et la source d’entrée.

#### <a name="example-2"></a>Exemple 2
La requête a une clause **Partition by PartitionId**. Dans une partition unique, les événements sont envoyés via les méthodes d’envoi asynchrone. Les méthodes d’envoi asynchrone peuvent lancer plusieurs envois en même temps, ce qui peut entraîner des événements en désordre.

Dans ce cas, le désordre et l’arrivée tardive sont au moins le délai maximal entre la génération de l’événement et l’envoi de l’événement, plus la latence maximale entre l’expéditeur et la source d’entrée.

#### <a name="example-3"></a>Exemple 3
La requête n’a pas de clause **Partition by PartitionId** et il y a au moins deux partitions.

La configuration est identique à celle de l’exemple 2. Toutefois, l’absence de données dans l’une des partitions peut retarder la sortie d’une fenêtre de tolérance d’arrivée tardive supplémentaire.

## <a name="handling-event-producers-with-differing-timelines"></a>Gestion des producteurs d’événements avec différentes chronologies
Un flux d’événements d’entrée contient souvent des événements issus de plusieurs producteurs d’événements, comme des appareils individuels. Ces événements peuvent arriver dans le désordre pour les raisons déjà mentionnées. Dans ces scénarios, bien que le désordre entre producteurs d’événements puisse être important, le désordre entre événements d’un même producteur est faible (voire inexistant).

Azure Stream Analytics fournit des mécanismes généraux pour gérer les événements en désordre. Ces mécanismes permettent de gérer les retards (en attentant que les événements retardataires atteignent le système), de supprimer ou d’ajuster des événements ou les deux.

Pourtant, dans de nombreux scénarios, la requête souhaitée traite séparément les événements de différents producteurs d’événements. Par exemple, les événements peuvent être regroupés par fenêtre, par appareil. Dans ce cas, il est inutile de retarder la sortie d’un producteur d’événements pour attendre que les autres producteurs d’événements rattrapent leur retard. En d’autres termes, il n’est pas nécessaire de traiter la variation de délai entre producteurs. Vous pouvez l’ignorer.

Bien entendu, cela signifie que les événements de sortie eux-mêmes sont en désordre par rapport à leur horodatage. Le consommateur en aval doit être en mesure de gérer ce comportement. Toutefois, chaque événement dans la sortie est correct.

Azure Stream Analytics implémente cette fonctionnalité à l’aide de la clause [TIMESTAMP BY OVER](https://msdn.microsoft.com/library/azure/mt573293.aspx).

## <a name="summary"></a>Résumé
* Configurez la tolérance d’arrivée tardive et la fenêtre de désordre en fonction de vos exigences d’exactitude et de latence. Tenez compte également de la façon dont les événements sont envoyés.
* De préférence, la tolérance de désordre doit être inférieure à la tolérance d’arrivée tardive.
* En cas de combinaison de plusieurs chronologies, l’absence de données dans l’une des sources ou partitions peut retarder la sortie d’une fenêtre de tolérance d’arrivée tardive supplémentaire.

## <a name="get-help"></a>Obtenir de l’aide
Pour une assistance supplémentaire, essayez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
