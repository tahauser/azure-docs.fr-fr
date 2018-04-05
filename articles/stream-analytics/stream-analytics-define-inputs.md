---
title: 'Connexion de données : entrées de flux de données issues d’un flux d’événements | Microsoft Docs'
description: En savoir plus sur la configuration d’une connexion de données à Stream Analytics nommée « entrées ». Les données incluent un flux de données de partir d’événements et également des données de référence.
keywords: flux de données, connexion de données, flux d’événements
services: stream-analytics
documentationcenter: ''
author: SnehaGunda
manager: kfile
ms.assetid: 8155823c-9dd8-4a6b-8393-34452d299b68
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/11/2017
ms.author: sngun
ms.openlocfilehash: 0ddc3187e9fc0664838dd07f781f4d7e2e4a7fe0
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Connexion de données : en savoir plus sur les entrées de flux de données pour Stream Analytics
La connexion de données à un travail Stream Analytics est un flux d’événements provenant d’une source de données, qui est appelée *entrée* du travail. Stream Analytics propose une intégration de pointe aux sources de flux de données Azure, notamment [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) et le [stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/). Ces sources d’entrée peuvent provenir du même abonnement Azure que votre travail d’analyse ou d’un autre abonnement.

## <a name="data-input-types-data-stream-and-reference-data"></a>Types d’entrée de données : flux de données et données de référence.
Lorsque les données sont transmises à une source de données, elles sont utilisées par le travail Stream Analytics et traitées en temps réel. Les entrées sont réparties en deux types : les entrées de flux de données et les entrées de données de référence.

### <a name="data-stream-inputs"></a>Entrées de flux de données
Un flux de données est une séquence illimitée d’événements au fil du temps. Les travaux Stream Analytics doivent contenir au moins une entrée de flux de données. Le stockage d’objets blob, Event Hubs et IoT Hub sont pris en charge en tant que sources d’entrée de flux de données. Les concentrateurs Event Hub permettent de collecter des flux d’événements à partir de plusieurs appareils et services. Il peut s’agir de flux d’activités de réseaux sociaux, d’informations boursières ou de données provenant de capteurs. Les IoT Hubs sont optimisés pour collecter des données provenant d’appareils connectés dans les scénarios Internet des objets (IoT).  Le stockage d’objets blob peut être utilisé comme source d’entrée pour la réception de données en bloc en tant que flux, par exemple des fichiers journaux.  

### <a name="reference-data"></a>Données de référence
Stream Analytics prend également en charge des entrées appelées *données de référence*. Ces données auxiliaires sont statiques ou sont modifiées lentement. Elles sont généralement utilisées pour effectuer une corrélation et des recherches. Par exemple, vous pouvez joindre des données de l’entrée de flux de données à des données de référence, comme vous effectueriez une jointure SQL pour rechercher des valeurs statiques. Le stockage d’objets blob Azure est la seule source d’entrée prise en charge pour les données de référence. Les objets blob de source de données de référence sont limités à une taille de 100 Mo.

Pour découvrir comment créer des entrées de données de référence, voir [Utiliser les données de référence](stream-analytics-use-reference-data.md).  

## <a name="compression"></a>Compression

Azure Stream Analytics prend en charge la compression pour toutes les sources d’entrée de flux de données (Event Hubs, IoT Hub et stockage Blob). Cette fonctionnalité ajoute une option de liste déroulante au panneau **Nouvelle entrée** dans le portail Azure pour vous permettre de choisir de compresser des flux de données. Les types de référence actuellement pris en charge sont Aucun, GZip et Compression Deflate. La prise en charge de la compression n’est pas disponible pour les données de référence.

Vous n’avez pas besoin de spécifier le type de compression avec la sérialisation Avro. Si les données d’entrée Avro sont compressées, elles sont gérées de façon transparente. 

## <a name="create-data-stream-input-from-event-hubs"></a>Créer une entrée de flux de données à partir de concentrateurs Event Hubs

Azure Event Hubs fournit des services de réception d’événements de publication/d’abonnement hautement évolutifs. Un concentrateur Event Hub peut collecter des millions d’événements par seconde afin que vous puissiez traiter et analyser les grandes quantités de données générées par vos appareils et applications connectés. Ensemble, Event Hubs et Stream Analytics vous fournissent une solution complète pour des analyses en temps réel : Event Hubs vous permet d’alimenter Azure en événements en temps réel, et les travaux Stream Analytics peuvent les traiter en temps réel. Par exemple, vous pouvez envoyer à Event Hubs des clics web, des lectures de capteurs ou des événements de journaux en ligne. Vous pouvez alors créer des travaux Stream Analytics pour utiliser Event Hubs en tant que flux de données d’entrée pour le filtrage, l’agrégation et la corrélation en temps réel.

L’horodatage par défaut des événements provenant d’Event Hubs dans Stream Analytics est l’horodatage de l’arrivée de l’événement dans le concentrateur Event Hub, `EventEnqueuedUtcTime`. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

### <a name="consumer-groups"></a>Groupes de consommateurs
Vous devez configurer chaque entrée Event Hub de Stream Analytics afin qu’elle dispose de son propre groupe de consommateurs. Lorsqu’un travail contient une jointure réflexive ou s’il comporte plusieurs entrées, une entrée peut être lue par plusieurs lecteurs en aval. Cette situation a une incidence sur le nombre de lecteurs dans un groupe de consommateurs unique. Pour éviter de dépasser la limite des Event Hubs de cinq lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque travail Stream Analytics. Il existe également une limite de 20 groupes de consommateurs par concentrateur Event Hub. Pour plus d’informations, consultez l’article [Guide de programmation de concentrateurs d’événements](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-an-event-hub-as-a-data-stream-input"></a>Configurer un concentrateur Event Hub comme entrée de flux de données
Le tableau suivant décrit chaque propriété du panneau **Nouvelle entrée** du portail Azure lorsque vous configurez un concentrateur Event Hub en tant qu’entrée.

| Propriété | Description |
| --- | --- |
| **Alias d’entrée** |Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée. |
| **Espace de noms Service Bus** |Espace de noms Azure Service Bus, qui est un conteneur pour un ensemble d’entités de messagerie. Lorsque vous créez un concentrateur Event Hub, vous créez également un espace de noms Service Bus. |
| **Nom du hub d’événements** |Nom du concentrateur Event Hub à utiliser comme entrée. |
| **Nom de la stratégie du hub d’événements** |Stratégie d’accès partagé qui fournit l’accès au concentrateur Event Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| **Groupe de consommateurs du hub d’événements** (facultatif) |Groupe de consommateurs à utiliser pour ingérer les données du concentrateur Event Hub. Si aucun groupe de consommateurs n’est spécifié, le travail Stream Analytics utilise le groupe de consommateurs par défaut. Nous vous recommandons d’utiliser un groupe de consommateurs différent pour chaque travail Stream Analytics. |
| **Format de sérialisation de l’événement** |Format de sérialisation (JSON, CSV ou Avro) du flux de données entrant. |
| **Encodage** | Pour le moment, UTF-8 est le seul format d’encodage pris en charge. |
| **Compression** (facultatif) | Le type de compression (Aucun, GZip ou Deflate) du flux de données entrant. |

Si vos données proviennent d’un concentrateur Event Hub, vous avez accès aux champs de métadonnées suivants dans votre requête Stream Analytics :

| Propriété | Description |
| --- | --- |
| **EventProcessedUtcTime** |Date et heure du traitement de l’événement par Stream Analytics |
| **EventEnqueuedUtcTime** |Date et heure de la réception de l’événement par le hub d’événements. |
| **PartitionId** |ID de partition de base zéro de l'adaptateur d'entrée. |

Par exemple, en utilisant ces champs, vous pouvez écrire une requête semblable à l’exemple suivant :

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

> [!NOTE]
> Lorsque vous utilisez le concentrateur d’événements comme un point de terminaison pour les itinéraires IoT Hub, vous pouvez accéder à la métadonnée IoT Hub en utilisant la [fonction GetMetadataPropertyValue](https://msdn.microsoft.com/en-us/library/azure/mt793845.aspx).
> 

## <a name="create-data-stream-input-from-iot-hub"></a>Créer une entrée de flux de données à partir de IoT Hub
Azure Iot Hub est un service de réception d’événements de publication/d’abonnement hautement évolutif optimisé pour les scénarios IoT.

L’horodatage par défaut des événements provenant d’un concentrateur IoT Hub dans Stream Analytics est l’horodatage de l’arrivée de l’événement dans le concentrateur IoT Hub, `EventEnqueuedUtcTime`. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

> [!NOTE]
> Seuls les messages envoyés avec une propriété `DeviceClient` peuvent être traités.
> 
> 

### <a name="consumer-groups"></a>Groupes de consommateurs
Vous devez configurer chaque entrée IoT Hub de Stream Analytics afin qu’elle dispose de son propre groupe de consommateurs. Lorsqu’un travail contient une jointure réflexive ou s’il comporte plusieurs entrées, une entrée peut être lue par plusieurs lecteurs en aval. Cette situation a une incidence sur le nombre de lecteurs dans un groupe de consommateurs unique. Pour éviter de dépasser la limite Azure IoT Hub de cinq lecteurs par groupe de consommateurs par partition, il est recommandé de désigner un groupe de consommateurs pour chaque travail Stream Analytics.

### <a name="configure-an-iot-hub-as-a-data-stream-input"></a>Configurer un concentrateur IoT Hub comme entrée de flux de données
Le tableau suivant décrit chaque propriété du panneau **Nouvelle entrée** du portail Azure lorsque vous configurez un concentrateur IoT Hub en tant qu’entrée.

| Propriété | Description |
| --- | --- |
| **Alias d’entrée** |Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée.|
| **IoT hub** |Nom du concentrateur IoT Hub à utiliser comme entrée. |
| **Point de terminaison** |Point de terminaison du concentrateur IoT Hub.|
| **Nom de la stratégie d’accès partagé** |Stratégie d’accès partagé qui fournit l’accès au concentrateur IoT Hub. Chaque stratégie d’accès partagé a un nom, les autorisations que vous définissez ainsi que des clés d’accès. |
| **Clé de la stratégie d’accès partagé** |Clé d’accès partagé utilisée pour autoriser l’accès au concentrateur IoT Hub. |
| **Groupe de consommateurs** (facultatif) |Groupe de consommateurs à utiliser pour ingérer les données du concentrateur IoT Hub. Si aucun groupe de consommateurs n’est spécifié, un travail Stream Analytics utilise le groupe de consommateurs par défaut. Nous vous recommandons d’utiliser un groupe de consommateurs différent pour chaque travail Stream Analytics. |
| **Format de sérialisation de l’événement** |Format de sérialisation (JSON, CSV ou Avro) du flux de données entrant. |
| **Encodage** |Pour le moment, UTF-8 est le seul format d’encodage pris en charge. |
| **Compression** (facultatif) | Le type de compression (Aucun, GZip ou Deflate) du flux de données entrant. |

Si vos données proviennent d’un concentrateur IoT Hub, vous avez accès aux champs de métadonnées suivants dans votre requête Stream Analytics :

| Propriété | Description |
| --- | --- |
| **EventProcessedUtcTime** |Date et heure du traitement de l'événement. |
| **EventEnqueuedUtcTime** |Date et heure de la réception de l’événement par le concentrateur IoT Hub. |
| **PartitionId** |ID de partition de base zéro de l'adaptateur d'entrée. |
| **IoTHub.MessageId** | ID utilisé pour corréler une communication bidirectionnelle dans IoT Hub. |
| **IoTHub.CorrelationId** |ID utilisé dans les réponses de message et les commentaires dans IoT Hub. |
| **IoTHub.ConnectionDeviceId** |ID d’authentification utilisé pour envoyer ce message. Cette valeur est marquée sur les messages liés aux services par le concentrateur IoT Hub. |
| **IoTHub.ConnectionDeviceGenerationId** |ID de génération de l’appareil authentifié qui a été utilisé pour envoyer ce message. Cette valeur est marquée sur les messages liés aux services par le concentrateur IoT Hub. |
| **IoTHub.EnqueuedTime** |Heure de réception du message par le concentrateur IoT Hub. |
| **IoTHub.StreamId** |Propriété d’événement personnalisée, ajoutée par l’appareil de l’expéditeur. |


## <a name="create-data-stream-input-from-blob-storage"></a>Créer une entrée de flux de données à partir du stockage d’objets blob
Quand il est nécessaire de stocker de grandes quantités de données non structurées dans le cloud, le stockage Blob Azure offre une solution peu coûteuse et évolutive. Les données du stockage d’objets blob sont généralement considérées comme des données au repos. Toutefois, elles peuvent être traitées comme un flux de données par Stream Analytics. Un scénario classique pour les entrées de stockage d’objets blob avec Stream Analytics correspond au traitement des journaux. Dans ce scénario, des données de télémétrie ont été capturées à partir d’un système, et doivent être analysées et traitées pour extraire des données significatives.

L’horodatage par défaut des événements de stockage d’objets blob dans Stream Analytics est l’horodatage de la dernière modification de l’objet blob, soit `BlobLastModifiedUtcTime`. Pour traiter les données en tant que flux à l’aide d’un horodatage dans la charge utile d’événement, vous devez utiliser le mot-clé [TIMESTAMP BY](https://msdn.microsoft.com/library/azure/dn834998.aspx).

Les entrées au format CSV *nécessitent* une ligne d’en-tête pour définir les champs du jeu de données. Par ailleurs, tous les champs de ligne d’en-tête doivent être uniques.

> [!NOTE]
> Stream Analytics ne prend pas en charge l’ajout de contenu à un objet blob existant. Stream Analytics n’affiche chaque fichier qu’une seule fois, et toutes les modifications qui sont apportées à celui-ci, une fois que le travail a lu les données, ne sont pas traitées. Une meilleure pratique consiste à télécharger toutes les données d’un objet blob en une seule fois, puis d’ajouter les événements récents supplémentaires dans un fichier blob nouveau et différent.
> 

### <a name="configure-blob-storage-as-a-data-stream-input"></a>Configurer un stockage d’objets blob en tant qu’entrée de flux de données

Le tableau suivant décrit chaque propriété du panneau **Nouvelle entrée** du portail Azure lorsque vous configurez le stockage d’objets blob en tant qu’entrée.

| Propriété | Description |
| --- | --- |
| **Alias d’entrée** | Nom convivial que vous utilisez dans la requête du travail pour faire référence à cette entrée. |
| **Compte de stockage** | Nom du compte de stockage dans lequel se trouvent les fichiers d’objets blob. |
| **Clé du compte de stockage** | Clé secrète associée au compte de stockage. |
| **Conteneur** | Conteneur pour les entrées d’objets blob. Les conteneurs fournissent un regroupement logique des objets blob stockés dans le service d’objets blob Microsoft Azure. Lorsque vous chargez un objet blob dans le service de stockage Blob Azure, vous devez spécifier un conteneur pour cet objet blob. |
| **Modèle de chemin d’accès** (facultatif) | Chemin d’accès au fichier utilisé pour localiser les objets blob dans le conteneur spécifié. Dans le chemin d’accès, vous pouvez spécifier une ou plusieurs instances de l’une des trois variables suivantes : `{date}`, `{time}` ou `{partition}`<br/><br/>Exemple 1 : `cluster1/logs/{date}/{time}/{partition}`<br/><br/>Exemple 2 : `cluster1/logs/{date}`<br/><br/>Le caractère `*` n’est pas une valeur autorisée pour le préfixe du chemin d’accès. Seuls les <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caractères d’objet blob Azure</a> valides sont autorisés. |
| **Format de date** (facultatif) | Format de date suivant lequel les fichiers sont organisés si vous utilisez la variable de date dans le chemin d’accès. Exemple : `YYYY/MM/DD` |
| **Format d’heure** (facultatif) |  Format d’heure suivant lequel les fichiers sont organisés si vous utilisez la variable d’heure dans le chemin d’accès. Actuellement, la seule valeur prise en charge est `HH`. |
| **Format de sérialisation de l’événement** | Format de sérialisation (JSON, CSV ou Avro) des flux de données entrants. |
| **Encodage** | Pour CSV et JSON, UTF-8 est le seul format d’encodage actuellement pris en charge. |
| **Compression** (facultatif) | Le type de compression (Aucun, GZip ou Deflate) du flux de données entrant. |

Si vos données proviennent d’une source de stockage d’objets blob, vous avez accès aux champs de métadonnées suivants dans votre requête Stream Analytics :

| Propriété | Description |
| --- | --- |
| **BlobName** |Nom de l’objet blob d’entrée d’où provient l’événement. |
| **EventProcessedUtcTime** |Date et heure du traitement de l’événement par Stream Analytics |
| **BlobLastModifiedUtcTime** |Date et heure de la dernière modification apportée à l’objet blob. |
| **PartitionId** |ID de partition de base zéro de l'adaptateur d'entrée. |

Par exemple, en utilisant ces champs, vous pouvez écrire une requête semblable à l’exemple suivant :

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
Vous avez appris à connaître les options de connexion de données dans Azure pour vos travaux Stream Analytics. Pour en savoir plus sur Stream Analytics, consultez :

* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
