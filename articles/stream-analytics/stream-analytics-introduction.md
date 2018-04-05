---
title: Présentation de Stream Analytics | Microsoft Docs
description: Découvrez Stream Analytics, un service géré qui vous permet d’analyser les données de diffusion en continu à partir de l’Internet des objets (IoT) en temps réel.
keywords: analyse en tant que service, services gérés, traitement des données de diffusion en continu, analyse de diffusion en continu, qu’est-ce que Stream Analytics
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/17/2017
ms.author: jeanb
ms.openlocfilehash: 70a4b0b358b79ec892483a9fa5573e4bf2d57534
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2018
---
# <a name="what-is-stream-analytics"></a>Qu’est-ce que Stream Analytics ?

Azure Stream Analytics est un moteur de traitement des événements géré qui configure des calculs analytiques en temps réel sur des données de streaming. Les données peuvent provenir d’appareils, de capteurs, de sites web, de flux issus des réseaux sociaux, d’applications, de systèmes d’infrastructure et bien plus encore. 

Utilisez Stream Analytics pour examiner des volumes importants de flux de données issus d’appareils ou de processus, extraire des informations à partir du flux de données et identifier des modèles, des tendances et des relations. Utilisez ces modèles pour déclencher d’autres processus ou actions, tels que des alertes, des flux de travail Automation, envoyer des informations vers un outil de création de rapports ou bien les stocker pour des recherches ultérieures. 

Voici quelques exemples :

* Analyse et alertes de transactions en actions.
* Détection des fraudes, données, et identifier les protections. 
* Analyse de détecteur et d’actionneur incorporés.
* Analyse de parcours Web.

## <a name="how-does-stream-analytics-work"></a>Comment fonctionne Stream Analytics ?

Le diagramme suivant illustre le pipeline Stream Analytics, en montrant comment les données sont ingérées, analysées, puis envoyées pour la présentation ou l’action. 

![Pipeline Stream Analytics](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

Stream Analytics démarre avec une source de données de diffusion en continu. Les données peuvent être ingérées dans Azure à partir d’un appareil à l’aide d’un concentrateur d’événements Azure ou d’un IoT Hub. Les données peuvent également être extraites d’un magasin de données, tel que le Stockage Blob Azure. 

Pour examiner le flux de données, vous créez un *travail* Stream Analytics indiquant d’où proviennent les données. Le travail spécifie également une *transformation* ; comment rechercher des données, des modèles ou des relations. Pour cette tâche, Stream Analytics prend en charge un langage de requête de type SQL pour filtrer, trier, agréger et joindre les données de diffusion sur une période donnée.

Enfin, le travail spécifie une sortie vers ces données transformées. Vous contrôlez les éléments à effectuer en réponse aux informations que vous avez analysées. Par exemple, en réponse à l’analyse, vous pouvez :

* Envoyer une commande pour modifier les paramètres d’un appareil. 
* Envoyer des données à une file d’attente contrôlée pour d’autres actions basées sur des résultats. 
* Envoyer des données à un tableau de bord Power BI.
* Envoyer des données vers un dispositif de stockage, tel que Data Lake Store, Azure SQL Database ou Stockage Blob Azure.

Vous pouvez ajuster le nombre d’événements traités par seconde pendant l’exécution du travail. Vous pouvez également produire des journaux de diagnostic pour la résolution de problèmes.

## <a name="key-capabilities-and-benefits"></a>Avantages et fonctionnalités clés

Stream Analytics est conçu pour être facile à utiliser, flexible, évolutif selon le volume du travail.

### <a name="connect-inputs-and-outputs"></a>Connecter des entrées et des sorties

Stream Analytics se connecte directement à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) et à [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) pour l’ingestion de flux de données, ainsi qu’au [service de stockage BLOB Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) pour l’ingestion de données d’historique. Associez des données à partir de concentrateurs d’événements avec Stream Analytics à d’autres sources de données et moteurs de traitement. L’entrée de travail peut également inclure des données de référence (données statiques ou à variation lente). Vous pouvez joindre des données de diffusion à ces données de référence pour effectuer des opérations de recherche de la même façon que vous le feriez avec des requêtes de base de données.

Acheminez la sortie du travail Stream Analytics dans plusieurs directions. Écrivez dans un dispositif de stockage, comme Azure Blob, Azure SQL Database, Azure Data Lake Stores, ou Azure Cosmos DB. À partir de là, vous pouvez exécuter des analyses par lots avec Azure HDinsight. Ou bien envoyer la sortie vers un autre service pour une utilisation par un autre processus, tels que des concentrateurs d’événements, Azure Service Bus, des files d’attente, ou bien vers Power BI pour une visualisation.

### <a name="simple-to-use"></a>Simple à utiliser

Pour définir les transformations, vous utilisez un [langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) simple et déclaratif qui vous permet de créer des analyses complexes sans programmation. Le langage de requête considère les données de diffusion comme entrée. Vous pouvez ensuite filtrer et trier les données, agréger des valeurs, effectuer des calculs, joindre des données (au sein d’un flux de données ou à des données de référence) et utiliser des fonctions géospatiales. Vous pouvez modifier les requêtes dans le portail, à l’aide d’IntelliSense et de la vérification de la syntaxe, et tester les requêtes à l’aide d’exemples de données que vous pouvez extraire à partir du flux en direct.

### <a name="extensible-query-language"></a>Langage de requête extensible

Vous pouvez étendre les fonctionnalités du langage de requête en définissant et en appelant des fonctions supplémentaires. Vous pouvez définir des appels de fonction dans le service Azure Machine Learning pour tirer parti des solutions d’Azure Machine Learning. Vous pouvez également intégrer des fonctions JavaScript définies par l’utilisateur afin d’effectuer des calculs complexes dans le cadre d’une requête Stream Analytics.

### <a name="scalable"></a>Évolutif

Stream Analytics peut gérer jusqu’à 1 Go de données entrantes par seconde. L’intégration à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) et [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) permet aux travaux d’ingérer des millions d’événements par seconde. Ces événements peuvent provenir d’appareils connectés, de flux de clics, de fichiers journaux, etc. À l’aide de la fonction de partition des concentrateurs d’événements, vous pouvez partitionner des calculs en étapes logiques, chacune avec la possibilité d’être partitionnées pour augmenter l’évolutivité.

### <a name="low-cost"></a>Faible coût

Comme un service cloud, Stream Analytics est optimisé pour réduire le coût. Payez à l’utilisation en fonction de l’unité de diffusion et de la quantité de données traitées. L’utilisation est définie en fonction du volume d’événements traités et de la quantité de puissance de calcul configurée dans le cluster de travail.

### <a name="reliable"></a>Fiable

En tant que service géré, Stream Analytics permet d’éviter la perte de données et d’assurer la continuité. En cas de panne, le service propose des capacités de récupération intégrées. Doté d’une capacité de conservation de l’état en interne, le service peut fournir des résultats reproductibles en assurant la possibilité d’archiver des événements et de recommencer un traitement ultérieurement, tout en obtenant les mêmes résultats. Cette fonctionnalité vous permet de revenir en arrière et d’examiner les calculs lors de l’analyse des causes premières, l’analyse de scénarios, etc.

## <a name="next-steps"></a>Étapes suivantes

* Commencez par l’[expérimentation d’entrées et de requêtes à partir d’appareils IoT](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Créez une [solution Stream Analytics de bout en bout](stream-analytics-real-time-fraud-detection.md) qui examine les métadonnées téléphoniques pour rechercher des appels frauduleux.
* Trouvez les réponses aux questions que vous vous posez sur Stream Analytics dans le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

