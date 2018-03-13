---
title: "Vue d’ensemble d’Azure Event Hubs Capture | Microsoft Docs"
description: "Capturer les données de télémétrie avec Event Hubs Capture"
services: event-hubs
documentationcenter: 
author: sethmanheim
manager: timlt
editor: 
ms.assetid: e53cdeea-8a6a-474e-9f96-59d43c0e8562
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: sethm;darosa
ms.openlocfilehash: fbd4aef62891341ad3760b74cd8aaee7abf7b827
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="azure-event-hubs-capture"></a>Azure Event Hubs Capture

Azure Event Hubs Capture vous permet de fournir automatiquement les données de streaming dans Event Hubs vers un compte [Stockage Blob Azure](https://azure.microsoft.com/services/storage/blobs/) ou [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) de votre choix avec une souplesse accrue permettant de spécifier un intervalle personnalisé de temps ou de taille. La configuration de l’outil Capture est rapide : il n’existe aucun coût d’administration pour son exécution et il s’adapte automatiquement à vos [unités de débit](event-hubs-features.md#capacity) Event Hubs. Event Hubs Capture représente le moyen le plus simple de charger les données pour la diffusion en continu dans Azure et vous permet de vous concentrer sur le traitement des données plutôt que sur la capture de données.

Event Hubs Capture vous permet de traiter des pipelines basés sur des lots et en temps réel sur le même flux. Cela vous permet de créer des solutions capables d’évoluer avec vos besoins au fil du temps. Que vous créiez des systèmes basés sur des lots dès aujourd’hui en pensant au traitement en temps réel à l’avenir, ou que vous souhaitiez ajouter un chemin à froid efficace vers une solution existante en temps réel, Event Hubs Capture facilite la tâche avec les données diffusées en continu.

## <a name="how-event-hubs-capture-works"></a>Fonctionnement d’Azure Event Hubs Capture

Event Hubs est une mémoire tampon durable de rétention temporelle pour l’entrée de télémétrie, similaire à un journal distribué. La clé de la mise à l’échelle dans Event Hubs est le [modèle de consommateur partitionné](event-hubs-features.md#partitions). Chaque partition est un segment de données indépendant, et est utilisée de manière indépendante. Au fil du temps ces données vieillissent, en fonction de la période de rétention configurable. Par conséquent, un hub d’événements donné n’est jamais « saturé ».

Event Hubs Capture vous permet de spécifier votre propre compte Stockage Blob Azure ou Azure Data Lake Store, ainsi qu’un conteneur qui est utilisé pour stocker les données capturées. Ces comptes peuvent se trouver dans la même région que votre hub d’événements ou dans une autre région, ce qui ajoute à la flexibilité de la fonctionnalité Event Hubs Capture.

Les données capturées sont écrites au format [Apache Avro][Apache Avro] : un format compact, rapide et binaire qui fournit des structures de données riches avec un schéma inclus. Ce format est largement utilisé dans l’écosystème Hadoop, Stream Analytics et Azure Data Factory. Vous trouverez plus d’informations sur l’utilisation d’Avro plus loin dans cet article.

### <a name="capture-windowing"></a>Fenêtrage de Capture

Event Hubs Capture vous permet de configurer une fenêtre de temps pour le contrôle de la capture. Cette fenêtre présente une taille et une configuration temporelle minimales avec une « stratégie de premier gagnant », ce qui signifie que le premier déclencheur rencontré entraîne une opération de capture. Si vous avez une fenêtre de capture de quinze minutes et de 100 Mo, et si vous envoyez 1 Mo par seconde, la fenêtre de taille se déclenche avant la fenêtre temporelle. Chaque partition capture indépendamment et écrit un objet blob de bloc complet au moment de la capture, nommé d’après l’heure à laquelle l’intervalle de capture a été rencontré. La convention d’affectation de noms de stockage est la suivante :

```
{Namespace}/{EventHub}/{PartitionId}/{Year}/{Month}/{Day}/{Hour}/{Minute}/{Second}
```

Notez que les valeurs de date contiennent des zéros. Par exemple :

```
https://mystorageaccount.blob.core.windows.net/mycontainer/mynamespace/myeventhub/0/2017/12/08/03/03/17.avro
```

### <a name="scaling-to-throughput-units"></a>Mise à l’échelle des unités de débit

Le trafic Event Hubs est contrôlé par les [unités de débit](event-hubs-features.md#capacity). Une unité de débit autorise 1 Mo/s ou 1 000 événements par seconde d’entrée et 2 000 événements par seconde de sortie. Les concentrateurs d’événements Standard peuvent être configurés avec 1 à 20 unités de débit, et vous pouvez en acheter d’autres en soumettant une [demande de support][support request] d’augmentation de quota. L’utilisation au-delà des unités de débit que vous avez achetées est limitée. Event Hubs Capture copie les données directement depuis le stockage Event Hubs interne, en contournant les quotas de sortie des unités de débit et en enregistrant votre sortie pour d’autres lecteurs de traitement tels que Stream Analytics ou Spark.

Une fois configuré, Event Hubs Capture s’exécute automatiquement lorsque vous envoyez votre premier événement et continue de s’exécuter. Pour que votre traitement en aval sache plus facilement que le processus fonctionne, les Event Hubs écrivent des fichiers vides en l’absence de données. Ce processus fournit une cadence prévisible et un marqueur qui peuvent alimenter vos processeurs de traitement par lots.

## <a name="setting-up-event-hubs-capture"></a>Configuration de l’outil Event Hubs Capture

Vous pouvez configurer la fonctionnalité Capture lors de la création du concentrateur d’événements, à l’aide du [portail Azure](https://portal.azure.com) ou des modèles Azure Resource Manager. Pour plus d’informations, consultez les articles suivants :

- [Activer Event Hubs Capture à l’aide du portail Azure](event-hubs-capture-enable-through-portal.md)
- [Créer un espace de noms Event Hubs avec un concentrateur d’événements et activer Capture à l’aide d’un modèle Azure Resource Manager](event-hubs-resource-manager-namespace-event-hub-enable-capture.md)

## <a name="exploring-the-captured-files-and-working-with-avro"></a>Exploration des fichiers capturés et utilisation d’Avro

Event Hubs Capture crée des fichiers au format Avro, tel que spécifié dans la fenêtre de temps configurée. Vous pouvez afficher ces fichiers à l’aide de n’importe quel outil tel que [l’Explorateur de stockage Azure][Azure Storage Explorer]. Vous pouvez télécharger les fichiers localement pour les utiliser.

Les fichiers générés par Event Hubs Capture présentent le schéma Avro suivant :

![][3]

Un moyen facile d’explorer les fichiers Avro consiste à utiliser la boîte à [outils Avro][Avro Tools] d’Apache. Après avoir téléchargé cette boîte, vous pouvez voir le schéma d’un fichier Avro spécifique en exécutant la commande suivante :

```
java -jar avro-tools-1.8.2.jar getschema <name of capture file>
```

Cette commande renvoie

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

Vous pouvez également utiliser les outils Avro pour convertir le fichier au format JSON et effectuer d’autres traitements.

Pour effectuer un traitement plus avancé, téléchargez et installez Avro pour la plateforme de votre choix. Au moment de la rédaction de cet article, les implémentations sont disponibles pour C, C++, C\#, Java, NodeJS, Perl, PHP, Python et Ruby.

Apache Avro propose des guides de mise en route complets pour [Java][Java] et [Python][Python]. Vous pouvez également lire l’article [Prise en main d’Event Hubs Capture](event-hubs-capture-python.md).

## <a name="how-event-hubs-capture-is-charged"></a>Chargement d’Azure Event Hubs Capture

Event Hubs Capture est mesuré de la même façon que les unités de débit, au tarif horaire. La facturation est directement proportionnelle au nombre d’unités de débit achetées pour l’espace de noms. En même temps que les unités de débit augmentent et diminuent, Event Hubs Capture augmente et diminue pour fournir des performances adaptées. Les compteurs se produisent en même temps. Pour plus d’informations sur les prix appliqués, consultez [Tarification d’Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/). 

## <a name="next-steps"></a>Étapes suivantes

Event Hubs Capture est la solution la plus simple pour charger des données dans Azure. À l’aide d’Azure Data Lake, d’Azure Data Factory et d’Azure HDInsight, vous pouvez effectuer un traitement par lots, ainsi que d’autres analyses en utilisant des outils et des plateformes de votre choix, à l’échelle requise.

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Bien démarrer avec l’envoi et la réception d’événements](event-hubs-dotnet-framework-getstarted-send.md)
* [Vue d’ensemble des hubs d’événements][Event Hubs overview]

[Apache Avro]: http://avro.apache.org/
[support request]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[Azure Storage Explorer]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-capture-overview/event-hubs-capture3.png
[Avro Tools]: http://www-us.apache.org/dist/avro/avro-1.8.2/java/avro-tools-1.8.2.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
