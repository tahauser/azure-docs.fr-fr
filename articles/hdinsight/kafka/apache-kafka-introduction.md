---
title: "Présentation d’Apache Kafka sur HDInsight - Azure | Microsoft Docs"
description: "Découvrez Apache Kafka sur HDInsight : Présentation, fonctionnalités et exemples et informations de prise en main."
services: hdinsight
documentationcenter: 
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
ms.date: 11/07/2017
ms.author: larryfr
ms.openlocfilehash: 09a661b2a100245dd424e24d8a8ddef56c573b02
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="introducing-apache-kafka-on-hdinsight"></a>Présentation d’Apache Kafka sur HDInsight

[Apache Kafka](https://kafka.apache.org) est une plateforme de diffusion en continu distribuée open source qui permet de générer des pipelines de données et des applications de diffusion en continu en temps réel. Kafka fournit également des fonctionnalités de courtier de messages semblables à une file d’attente, où vous pouvez publier et vous abonner aux flux de données nommés. Kafka sur HDInsight vous offre un service géré, hautement évolutif et hautement disponible dans le cloud Microsoft Azure.

## <a name="why-use-kafka-on-hdinsight"></a>Pourquoi utiliser Kafka sur HDInsight ?

Le logiciel Kafka sur HDInsight offre les fonctionnalités suivantes :

* Contrat de niveau de service (SLA) : [Informations de contrat SLA pour HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Modèle de messagerie de publication/abonnement : Kafka fournit une API de producteur pour la publication des enregistrements dans un sujet Kafka. L’API de consommateur est utilisée lors de l’abonnement à un sujet.

* Traitement des flux : Kafka est souvent utilisé avec Apache Storm ou Spark pour le traitement des flux en temps réel. Kafka 0.10.0.0 (HDInsight version 3.5 et 3.6) a introduit une API de diffusion en continu qui vous permet de créer des solutions de diffusion en continu sans Storm ni Spark.

* Évolution horizontale : Kafka partitionne les flux de données entre les nœuds du cluster HDInsight. Les processus consommateur peuvent être associés à des partitions individuelles pour fournir un équilibrage de charge lors de l’utilisation des enregistrements.

* Livraison chronologique : dans chaque partition, les enregistrements sont stockés dans le flux de données dans l’ordre de réception. En associant un processus consommateur par partition, vous pouvez garantir que les enregistrements sont traités dans l’ordre.

* Tolérance de pannes : les partitions peuvent être répliquées entre les nœuds pour fournir une tolérance de pannes.

* Intégration à Azure Managed Disks : Managed Disks disques offre une mise à l’échelle et un débit supérieurs pour les disques utilisés par les machines virtuelles du cluster HDInsight.

    Les disques gérés sont activés par défaut pour Kafka sur HDInsight. Le nombre de disques utilisés par nœud peut être configuré lors de la création de HDInsight. Pour plus d’informations sur les disques gérés, consultez [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md).

    Pour plus d’informations sur la configuration des disques gérés avec Kafka sur HDInsight, consultez [Increase scalability of Kafka on HDInsight](apache-kafka-scalability.md) (Augmenter l’évolutivité de Kafka sur HDInsight).

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
> Kafka n’a pas connaissance du matériel sous-jacent (rack) dans le centre de données Azure. Pour vous assurer que les partitions sont correctement équilibrées sur le matériel sous-jacent, consultez [Configurer la haute disponibilité des données (Kafka)](apache-kafka-high-availability.md).

## <a name="next-steps"></a>Étapes suivantes

Cliquez sur les liens suivants pour apprendre à utiliser Apache Kafka sur HDInsight :

* [Prise en main de Kafka sur HDInsight](apache-kafka-get-started.md)

* [Utilisation de MirrorMaker pour créer un réplica de Kafka sur HDInsight](apache-kafka-mirroring.md)

* [Use Apache Storm with Kafka on HDInsight](../hdinsight-apache-storm-with-kafka.md) (Utilisation d’Apache Storm avec Kafka sur HDInsight)

* [Use Apache Spark with Kafka on HDInsight](../hdinsight-apache-spark-with-kafka.md) (Utilisation d’Apache Spark avec Kafka sur HDInsight)

* [Connect to Kafka through an Azure Virtual Network](apache-kafka-connect-vpn-gateway.md) (Se connecter à Kafka via un réseau virtuel Azure)