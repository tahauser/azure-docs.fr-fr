---
title: Présentation d’Apache Kafka sur HDInsight - Azure | Microsoft Docs
description: 'Découvrez Apache Kafka sur HDInsight : Présentation, fonctionnalités et exemples et informations de prise en main.'
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: f284b6e3-5f3b-4a50-b455-917e77588069
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 4a4f2c6734de211cd20ee4b9f6815bdefefb25bc
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="introducing-apache-kafka-on-hdinsight"></a>Présentation d’Apache Kafka sur HDInsight

[Apache Kafka](https://kafka.apache.org) est une plateforme de diffusion en continu distribuée open source qui permet de générer des pipelines de données et des applications de diffusion en continu en temps réel. Kafka fournit également des fonctionnalités de courtier de messages semblables à une file d’attente, où vous pouvez publier et vous abonner aux flux de données nommés. Kafka sur HDInsight vous offre un service géré, hautement évolutif et hautement disponible dans le cloud Microsoft Azure.

## <a name="why-use-kafka-on-hdinsight"></a>Pourquoi utiliser Kafka sur HDInsight ?

Le logiciel Kafka sur HDInsight offre les fonctionnalités suivantes :

* __Contrat de niveau de service (SLA) de 99,9 % sur le temps d’activité de Kafka__ : pour plus d’information, consultez le document [Informations du SLA pour HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* __Tolérance aux pannes__ : Kafka a été conçu avec une seule vue dimensionnelle d’un rack qui fonctionne correctement sur certains environnements. Toutefois, sur des environnements tels qu’Azure, un rack est réparti en deux dimensions : les domaines de mise à jour et les domaines d’erreur. Microsoft fournit des outils permettant de rééquilibrer des partitions et réplicas Kafka entre les UD et les FD. 

    Pour plus d’informations, consultez [Haute disponibilité avec Kafka dans HDInsight](apache-kafka-high-availability.md).

* **Intégration à Azure Managed Disks** : Managed Disks offre une mise à l’échelle et un débit supérieurs pour les disques utilisés par Kafka sur HDInsight, jusqu’à 16 To par nœud dans le cluster.

    Pour plus d’informations sur la configuration des disques gérés avec Kafka sur HDInsight, consultez [Increase scalability of Kafka on HDInsight](apache-kafka-scalability.md) (Augmenter l’évolutivité de Kafka sur HDInsight).

    Pour plus d’informations sur les disques gérés, consultez [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

* **Alertes, surveillance et maintenance prédictive** : Azure Log Analytics peut servir à surveiller Kafka sur HDInsight. Log Analytics montre les informations au niveau de la machine virtuelle, telles que les métriques des disques et des cartes réseau, et les métriques JMX depuis Kafka.

    Pour plus d’informations, consultez [Analyser les journaux pour Kafka sur HDInsight](apache-kafka-log-analytics-operations-management.md).

* **Réplication des données de Kafka** : Kafka fournit l’utilitaire MirrorMaker, qui réplique les données entre les clusters Kafka.

    Pour plus d’informations sur l’utilisation de MirrorMaker, consultez [Répliquer des rubriques Kafka avec Kafka sur HDInsight](apache-kafka-mirroring.md).

* **Mise à l’échelle du cluster**: HDInsight vous permet de modifier le nombre de nœuds de travail (qui hébergent le répartiteur-Kafka) après la création du cluster. Augmentez la taille des instances d’un cluster lorsque la charge de travail augmente, ou diminuez-les pour réduire les coûts. La mise à l’échelle peut être effectuée depuis le portail Azure, Azure PowerShell et d’autres interfaces de gestion Azure. Pour Kafka, vous devez rééquilibrer les réplicas de partition après les opérations de mise à l’échelle. Le rééquilibrage des partitions permet à Kafka de tirer parti du nouveau nombre de nœuds de travail.

    Pour plus d’informations, consultez [Haute disponibilité avec Kafka dans HDInsight](apache-kafka-high-availability.md).

* **Modèle de messagerie de publication/abonnement** : Kafka fournit une API de producteur pour la publication des enregistrements dans un sujet Kafka. L’API de consommateur est utilisée lors de l’abonnement à un sujet.

    Pour plus d’informations, consultez [Démarrer avec Kafka sur HDInsight](apache-kafka-get-started.md).

* **Traitement des flux** : Kafka est souvent utilisé avec Apache Storm ou Spark pour le traitement des flux en temps réel. Kafka 0.10.0.0 (HDInsight version 3.5 et 3.6) a introduit une API de diffusion en continu qui vous permet de créer des solutions de diffusion en continu sans Storm ni Spark.

    Pour plus d’informations, consultez [Démarrer avec Kafka sur HDInsight](apache-kafka-get-started.md).

* **Évolution horizontale** : Kafka partitionne les flux de données entre les nœuds du cluster HDInsight. Les processus consommateur peuvent être associés à des partitions individuelles pour fournir un équilibrage de charge lors de l’utilisation des enregistrements.

    Pour plus d’informations, consultez [Démarrer avec Kafka sur HDInsight](apache-kafka-get-started.md).

* **Livraison chronologique** : dans chaque partition, les enregistrements sont stockés dans le flux de données dans l’ordre de réception. En associant un processus consommateur par partition, vous pouvez garantir que les enregistrements sont traités dans l’ordre.

    Pour plus d’informations, consultez [Démarrer avec Kafka sur HDInsight](apache-kafka-get-started.md).

## <a name="use-cases"></a>Cas d'utilisation

* **Messagerie** : dans la mesure où Kafka prend en charge le modèle de messagerie publication/abonnement, il est souvent utilisé comme courtier de messages.

* **Suivi des activités** : étant donné que Kafka fournit la journalisation dans l’ordre des enregistrements, il peut être utilisé pour effectuer le suivi et recréer des activités. Par exemple, les actions de l’utilisateur sur un site web ou dans une application.

* **Agrégation** : avec le traitement de flux de données, vous pouvez agréger des informations à partir de différents flux afin de combiner et de centraliser les informations dans des données opérationnelles.

* **Transformation** : avec le traitement de flux de données, vous pouvez combiner et enrichir les données à partir de plusieurs rubriques d’entrée dans une ou plusieurs rubriques de sortie.

## <a name="architecture"></a>Architecture

![Configuration de clusters Kafka](./media/apache-kafka-introduction/kafka-cluster.png)

Ce diagramme illustre une configuration Kafka typique qui utilise des groupes de consommateurs, un partitionnement et une réplication afin d’offrir une lecture parallèle des événements avec une tolérance de panne. Apache ZooKeeper est conçu pour les transactions simultanées, résilientes et à faible latence, car il gère l’état du cluster Kafka. Kafka stocke les enregistrements dans des *rubriques*. Les enregistrements sont produits par des *producteurs*, et utilisés par des *consommateurs*. Les producteurs récupèrent des enregistrements à partir des *répartiteurs* Kafka. Chaque nœud Worker dans votre cluster HDInsight est un répartiteur Kafka. Une partition est créée pour chaque consommateur, permettant ainsi le traitement parallèle des données de diffusion en continu. La réplication est employée pour répartir les partitions sur les nœuds, offrant ainsi une protection contre les pannes de nœud (broker). Une partition indiquée par un *(L)* est le leader pour la partition donné. Le trafic de producteur est acheminé vers le leader de chaque nœud, en utilisant l’état géré par ZooKeeper.

Chaque répartiteur Kafka utilise des disques gérés Azure. Le nombre de disques est défini par l’utilisateur et peut apporter un stockage allant jusqu’à 16 To par répartiteur.

> [!IMPORTANT]
> Kafka n’a pas connaissance du matériel sous-jacent (rack) dans le centre de données Azure. Pour vous assurer que les partitions sont correctement équilibrées sur le matériel sous-jacent, consultez le document [Configurer la haute disponibilité des données (Kafka)](apache-kafka-high-availability.md).

## <a name="next-steps"></a>Étapes suivantes

Cliquez sur les liens suivants pour apprendre à utiliser Apache Kafka sur HDInsight :

* [Prise en main de Kafka sur HDInsight](apache-kafka-get-started.md)

* [Utilisation de MirrorMaker pour créer un réplica de Kafka sur HDInsight](apache-kafka-mirroring.md)

* [Use Apache Storm with Kafka on HDInsight](../hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)

* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)

* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Se connecter à Kafka via un réseau virtuel Azure)