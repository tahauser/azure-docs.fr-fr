---
title: "Planifier la mise à l’échelle de votre environnement Azure Time Series Insights | Microsoft Docs"
description: "Cet article explique comment suivre les meilleures pratiques lors de la planification d’un environnement Azure Time Series Insights, y compris la capacité de stockage, la rétention des données, la capacité d’entrée et la surveillance."
services: time-series-insights
ms.service: time-series-insights
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 5fb158ba162dd199f419f9568de08a7a18c833dd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="plan-your-azure-time-series-insights-environment"></a>Planifier votre environnement Azure Time Series Insights

Cet article explique comment planifier votre environnement Azure Time Series Insights en fonction de votre taux d’entrée attendu et de vos besoins de rétention de données.

## <a name="best-practices"></a>Meilleures pratiques

Pour commencer à utiliser Time Series Insights, il est conseillé de connaître la quantité de données à transmettre à la minute ainsi que la durée de stockage de vos données.  

Pour plus d’informations sur la capacité et la rétention des deux références Time Series Insights, consultez [Tarifs de Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Tenez compte des attributs suivants pour planifier au mieux l’environnement et assurer sa réussite à long terme : 
- Capacité de stockage
- Période de rétention des données
- Capacité d’entrée 

## <a name="understand-storage-capacity"></a>Comprendre la capacité de stockage
Par défaut, Time Series Insights conserve les données en fonction de la quantité de stockage que vous avez provisionnée (unités x quantité de stockage par unité) et de l’entrée.

## <a name="understand-data-retention"></a>Comprendre la rétention des données
Vous pouvez configurer le paramètre de **durée de rétention des données** de votre environnement Time Series Insights, en sélectionnant jusqu'à 400 jours de rétention.  Time Series Insights comprend deux modes : un premier optimisé pour faire en sorte que votre environnement dispose des données les plus récentes (activé par défaut) et un second optimisé pour vérifier que les limites de rétention sont bien respectées (l’entrée est suspendue lorsque la capacité de stockage globale de l’environnement est atteinte).  Vous pouvez ajuster la rétention et basculer entre les deux modes dans la page de configuration de l’environnement dans le portail Azure.

Vous pouvez configurer une durée maximum de conservation des données de 400 jours dans votre environnement Time Series Insights.

## <a name="configure-data-retention"></a>Configurer la rétention de données

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre environnement Time Series Insights.

2. Sur la **page de l’environnement Time Series Insights**, sous le titre **Paramètres**, sélectionnez **Configurer**. 

3. Dans le champ **Durée de rétention des données (en jours)**, saisissez une valeur comprise entre 1 et 400.

   ![Configurer la rétention](media/environment-mitigate-latency/configure-retention.png)

## <a name="understand-ingress-capacity"></a>Comprendre la capacité d’entrée

L’autre point à prendre en compte pour la planification est la capacité d’entrée, qui dépend de l’allocation par minute. 

En termes de limitation, un paquet de données entré avec une taille de paquet de 32 Ko est considéré comme 32 événements, faisant chacun 1 Ko. La taille maximale autorisée de l’événement est 32 Ko ; les paquets de données d’une taille supérieure à 32 Ko sont tronqués.

Le tableau suivant récapitule la capacité d’entrée pour chaque référence SKU :

|SKU  |Nombre d’événements par mois, par unité  |Taille des événements par mois, par unité  |Nombre d’événements par minute, par unité  | Taille par minute, par unité   |
|---------|---------|---------|---------|---------|
|S1     |   30 millions     |  30 Go     |  700    |  700 ko   |
|S2     |   300 millions    |   300 Go   | 7 000   | 7 000 Ko  |

Vous pouvez augmenter la capacité d’une référence SKU S1 ou S2 à 10 unités dans un environnement unique. Vous ne pouvez pas migrer d’un environnement S1 vers un environnement S2, et inversement. 

Pour la capacité d’entrée, vous devez tout d’abord déterminer le total des entrées dont vous avez besoin chaque mois. Ensuite, déterminez vos besoins par minute, car c’est là que la limitation et la latence jouent un rôle.

En cas de pic d’entrée de données d’une durée inférieure à 24 heures, Time Series Insights peut effectuer un « rattrapage » à une vitesse d’entrée égale à 2x les taux indiqués ci-dessus. 

Par exemple, si vous avez une seule référence SKU S1 et des données d’entrée à un taux de 700 événements par minute, et un pic d’une durée inférieure à une heure à un taux de 1400 événements maximum, votre environnement ne devrait observer aucune latence notable. Toutefois, si vous dépassez les 1400 événements par minute pendant plus d’une heure, vous allez probablement observer une latence des données qui sont visualisées et disponibles pour les requêtes dans votre environnement. 

Vous ne savez peut-être pas à l’avance la quantité de données que vous allez transmettre en mode push. Dans ce cas, la télémétrie de données pour [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) et [Azure Event Hubs](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) est disponible dans votre portail Azure. Cette télémétrie peut vous aider à déterminer comment configurer votre environnement. Utilisez la page **Indicateurs de performance** dans le portail Azure de la source d’événements correspondante pour afficher sa télémétrie. Comprendre les indicateurs de performance de votre source d’événement vous permet de planifier et configurer plus efficacement votre environnement Time Series Insights.

## <a name="calculate-ingress-requirements"></a>Calculer les besoins d’entrée

- Vérifiez que votre capacité d’entrée est supérieure à votre taux moyen par minute et que votre environnement est suffisamment grand pour gérer votre entrée attendue qui équivaut à 2x votre capacité pendant moins d’une heure.

- En cas de pics d’entrée d’une durée supérieure à 1 heure, utilisez le taux de pointe comme moyenne, et provisionnez un environnement ayant la capacité de gérer ce taux.
 
## <a name="mitigate-throttling-and-latency"></a>Résoudre la limitation et la latence

Pour plus d’informations sur la manière d’éviter la limitation et la latence, consultez [Résoudre la latence et la limitation](time-series-insights-environment-mitigate-latency.md). 

## <a name="next-steps"></a>Étapes suivantes
- [Comment ajouter une source d’événements Event Hub](time-series-insights-how-to-add-an-event-source-eventhub.md)
- [Comment ajouter une source d’événements IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md)
