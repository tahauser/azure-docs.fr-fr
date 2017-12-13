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
ms.openlocfilehash: 208dfa14d5d18e106d654539cd80bafdeb90cdf8
ms.sourcegitcommit: 80eb8523913fc7c5f876ab9afde506f39d17b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2017
---
# <a name="azure-stream-analytics-event-order-consideration"></a>Considérations relatives à l’ordre des événements Azure Stream Analytics

## <a name="understand-arrival-time-and-application-time"></a>Présentation de l’heure d’arrivée et de l’heure de l’application

Dans un flux de données temporelles d’événements, chaque événement est horodaté. Azure Stream Analytics affecte un horodatage à chaque événement à l’aide de l’heure d’arrivée ou de l’heure de l’application. La colonne "System.Timestamp" indique l’horodatage affecté à l’événement. Une heure d’arrivée est affectée à la source d’entrée quand l’événement atteint la source. L’heure d’arrivée est EventEnqueuedTime pour l’entrée Event Hub et [l’heure de dernière modification de l’objet blob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) pour l’entrée de l’objet blob. L’heure de l’application est affectée quand l’événement est généré et qu’il fait partie de la charge utile. Pour traiter les événements selon l’heure de l’application, utilisez la clause "Timestamp by" dans la requête select. En l’absence de la clause "Timestamp by", les événements sont traités par heure d’arrivée. Vous pouvez accéder à l’heure d’arrivée à l’aide de la propriété EventEnqueuedTime pour le hub d’événements et à l’aide de la propriété BlobLastModified pour l’entrée de l’objet blob. Azure Stream Analytics génère un résultat par ordre d’horodatage et fournit quelques paramètres pour traiter les données en désordre.


## <a name="azure-stream-analytics-handling-of-multiple-streams"></a>Gestion de plusieurs flux de données par Azure Stream Analytics

Un travail Azure Stream Analytics combine dans certains cas les événements de plusieurs chronologies, par exemple :

* Génération d’une sortie à partir de plusieurs partitions. Les requêtes qui n’ont pas de clause « Partition by PartitionId » explicite doivent combiner les événements de toutes les partitions.
* Union de plusieurs sources d’entrée différentes.
* Jointure de sources d’entrée.

Dans les scénarios où plusieurs chronologies sont combinées, Azure Stream Analytics produit un résultat pour un horodatage *t1* uniquement une fois que toutes les sources qui sont combinées sont au moins au moment *t1*.
Par exemple, si la requête lit à partir d’une partition *Event Hub* qui comprend deux partitions, et que l’une des partitions *P1* a des événements jusqu’au moment *t1* et l’autre partition *P2* a des événements jusqu’au moment *t1 + x*, la sortie est générée jusqu’au moment *t1*.
Toutefois, en cas de présence d’une clause *« Partition by PartitionId »* explicite, les deux partitions progressent indépendamment.
Le paramètre Tolérance d’arrivée tardive est utilisé pour gérer l’absence de données dans certaines partitions.

## <a name="configuring-late-arrival-tolerance-and-out-of-order-tolerance"></a>Configuration de la tolérance d’arrivée tardive et de la plage de tolérance pour le désordre
Les flux d’entrée qui ne sont pas dans l’ordre sont :
* Triés (et par conséquent **retardés**).
* Ajustés par le système, en fonction de la stratégie spécifiée par l’utilisateur.

Stream Analytics tolère les événements tardifs et en désordre lors du traitement selon **l’heure de l’application**. Les paramètres suivants sont disponibles dans l’option **Ordre des événements** dans le portail Azure : 

![Traitement des événements Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

**Tolérance d’arrivée tardive**
* Ce paramètre est applicable uniquement lors d’un traitement par heure de l’application, sinon il est ignoré.
* Il s’agit de la différence maximale entre l’heure d’arrivée et l’heure de l’application. Si l’heure de l’application est antérieure à (Heure d’arrivée - Plage d’arrivée tardive), elle est définie sur (Heure d’arrivée - Plage d’arrivée tardive)
* Lorsque plusieurs partitions du même flux d’entrée ou de plusieurs flux d’entrée sont combinées, la tolérance d’arrivée tardive correspond au délai d’attente maximal des nouvelles données pour chaque partition. 

En bref, la plage d’arrivée tardive correspond au délai maximal entre la génération de l’événement et la réception de l’événement au niveau de la source d’entrée.
Un ajustement en fonction de la tolérance d’arrivée tardive est effectué en premier, puis un ajustement en fonction des événements en désordre. La colonne **System.Timestamp** indique l’horodatage final affecté à l’événement.

**Tolérance pour les événements en désordre**
* Les événements qui arrivent dans le désordre, mais dans la « plage de tolérance pour les événements en désordre » définie sont **réorganisés par horodatage**. 
* Les événements arrivant en dehors de la plage de tolérance sont **supprimés ou ajustés**.
    * **Ajustés** : les événements sont ajustés de façon à ce qu’ils semblent arrivés à l’heure la plus récente acceptable. 
    * **Supprimés** : ignorés.

Pour réorganiser les événements reçus dans la « plage de tolérance pour les événements en désordre », la sortie de la requête est **retardée par la plage de tolérance pour les événements en désordre**.

**Exemple**

* Tolérance d’arrivée tardive = 10 minutes<br/>
* Tolérance pour les événements en désordre = 3 minutes<br/>
* Traitement selon l’heure de l’application<br/>
* Événements :
   * Événement 1 _Heure de l’application_ = 00:00:00, _Heure d’arrivée_ = 00:10:01, _System.Timestamp_ = 00:00:01, ajusté car (_Heure d’arrivée_ - _Heure de l’application_) est supérieur à la tolérance d’arrivée tardive.
   * Événement 2 _Heure de l’application_ = 00:00:01, _Heure d’arrivée_ = 00:10:01, _System.Timestamp_ = 00:00:01, non ajusté car l’heure de l’application est comprise dans la plage d’arrivée tardive.
   * Événement 3 _Heure de l’application_ = 00:10:00, _Heure d’arrivée_ = 00:10:02, _System.Timestamp_ = 00:10:00, non ajusté car l’heure de l’application est comprise dans la plage d’arrivée tardive.
   * Événement 4 _Heure de l’application_ = 00:09:00, _Heure d’arrivée_ = 00:10:03, _System.Timestamp_ = 00:09:00, accepté avec l’horodatage d’origine car l’heure de l’application est comprise dans la tolérance pour les événements en désordre.
   * Événement 5 _Heure de l’application_ = 00:06:00, _Heure d’arrivée_ = 00:10:04, _System.Timestamp_ = 00:07:00, ajusté car l’heure de l’application est ultérieure à la tolérance pour les événements en désordre.

## <a name="practical-considerations"></a>Considérations d’ordre pratique
Comme mentionné ci-dessus, la *tolérance d’arrivée tardive* est la différence maximale entre l’heure de l’application et l’heure d’arrivée.
De plus, lors du traitement par heure d’application, les événements qui ont lieu après la *tolérance d’arrivée tardive* configurée sont ajustés avant que le paramètre de *tolérance pour le désordre* soit appliqué. Ainsi, le désordre réel est la valeur minimale parmi la tolérance d’arrivée tardive et la plage de tolérance pour le désordre.

Les événements en désordre dans un flux peuvent avoir pour cause :
* Des variations d’horloges entre les expéditeurs
* Une latence variable entre l’expéditeur et la source de l’événement d’entrée

Une arrivée tardive peut avoir les causes suivantes :
* Les expéditeurs regroupent les événements par lot et les envoient pour un intervalle ultérieur, après l’intervalle
* Latence entre l’envoi de l’événement par l’expéditeur et sa réception à la source d’entrée

Lors de la configuration de la *tolérance d’arrivée tardive* et de la *plage de tolérance pour le désordre* pour un travail spécifique, l’exactitude, les exigences de latence et les facteurs ci-dessus doivent être pris en compte.

Voici quelques exemples :

### <a name="example-1"></a>Exemple 1 : 
La requête a une clause « Partition by PartitionId » et, dans une même partition, les événements sont envoyés à l’aide de méthodes d’envoi synchrone. Les méthodes d’envoi synchrone sont bloquées jusqu’à ce que les événements soient envoyés.
Dans ce cas, le désordre est égal à zéro, car les événements sont envoyés dans l’ordre avec confirmation explicite avant d’envoyer l’événement suivant. L’arrivée tardive est le délai maximal entre la génération de l’événement et l’envoi de l’événement + la latence maximale entre l’expéditeur et la source d’entrée.

### <a name="example-2"></a>Exemple 2 :
La requête a une clause « Partition by PartitionId » et, dans une même partition, les événements sont envoyés à l’aide d’une méthode d’envoi asynchrone. Les méthodes d’envoi asynchrone peuvent démarrer plusieurs envois en même temps, ce qui peut entraîner la présence d’événements en désordre.
Dans ce cas, le désordre et l’arrivée tardive sont au moins le délai maximal entre la génération de l’événement et l’envoi de l’événement + la latence maximale entre l’expéditeur et la source d’entrée.

### <a name="example-3"></a>Exemple 3 :
La requête n’a pas de clause « Partition by PartitionId » et il existe au moins deux partitions.
La configuration est identique à celle de l’Exemple 2. Toutefois, l’absence de données dans l’une des partitions peut retarder la sortie d’une fenêtre de « tolérance d’arrivée tardive » supplémentaire.

## <a name="to-summarize"></a>Pour résumer
* La fenêtre de tolérance d’arrivée tardive et de désordre doit être configurée en fonction de l’exactitude et des exigences de latence, et elles doivent aussi tenir compte de la façon dont les événements sont envoyés.
* Nous vous recommandons de faire en sorte que la tolérance de désordre soit inférieure à la tolérance d’arrivée tardive.
* Lors de la combinaison de plusieurs chronologies, l’absence de données dans l’une des sources ou partitions peut retarder la sortie d’une fenêtre de « tolérance d’arrivée tardive » supplémentaire.

## <a name="get-help"></a>Obtenir de l’aide
Pour une assistance supplémentaire, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
