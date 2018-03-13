---
title: "Haute disponibilité avec Apache Kafka - Azure HDInsight | Microsoft Docs"
description: "Découvrez comment garantir une haute disponibilité avec Apache Kafka sur Azure HDInsight. Apprenez à rééquilibrer les réplicas de partition dans Kafka afin qu’ils soient répartis sur différents domaines d’erreur dans la région Azure qui contient HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: larryfr
ms.openlocfilehash: 9abd2c970a0429b908bd5650b0002c002bcdea83
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Haute disponibilité de vos données avec Apache Kafka sur HDInsight

Découvrez comment configurer des réplicas de partition pour les rubriques Kafka afin de tirer parti de la configuration de rack matériel sous-jacent. Cette configuration garantit la disponibilité des données stockées dans Kafka Apache sur HDInsight.

## <a name="fault-and-update-domains-with-kafka"></a>Domaines d’erreur et de mise à jour avec Kafka

Un domaine d’erreur est un regroupement logique de matériel sous-jacent dans un datacenter Azure. Chaque domaine d’erreur partage une source d’alimentation et un commutateur réseau communs. Les ordinateurs virtuels et les disques gérés mettant en œuvre les nœuds au sein d’un cluster HDInsight sont répartis dans ces domaines d’erreur. Cette architecture limite l’impact potentiel des défaillances de matériel physique.

Chaque région Azure possède un certain nombre de domaines d’erreur. Pour obtenir la liste des domaines et le nombre de domaines d’erreur qu’ils contiennent, consultez la documentation [Groupes à haute disponibilité](../../virtual-machines/windows/regions-and-availability.md#availability-sets).

> [!IMPORTANT]
> Kafka n’est pas informé des domaines d’erreur. Lorsque vous créez une rubrique dans Kafka, ce dernier peut stocker tous les réplicas de partition dans le même domaine d’erreur. Pour résoudre ce problème, HDInsight fournit [l’outil de rééquilibrage de partitions Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Quand rééquilibrer les réplicas de partition

Pour garantir la haute disponibilité de vos données Kafka, vous devez rééquilibrer les réplicas de partition de votre rubrique aux heures suivantes :

* Lorsqu’une rubrique ou une partition est créée

* Lorsque vous mettez à l’échelle un cluster

## <a name="replication-factor"></a>Facteur de réplication

> [!IMPORTANT]
> Il est recommandé d’utiliser une région Azure qui contient les trois domaines d’erreur, et un facteur de réplication de 3.

Si vous devez utiliser une région qui contient uniquement deux domaines d’erreur, utilisez un facteur de réplication de 4 afin de répartir uniformément les réplicas sur les domaines d’erreur.

Pour obtenir un exemple de création de rubriques et de paramétrage du facteur de réplication, consultez le document [Démarrer avec Kafka sur HDInsight](apache-kafka-get-started.md).

## <a name="how-to-rebalance-partition-replicas"></a>Comment rééquilibrer les réplicas de partition

Utilisez [l’outil rééquilibrage de partition Kafka](https://github.com/hdinsight/hdinsight-kafka-tools) pour rééquilibrer les rubriques sélectionnées. Cet outil doit être exécuté à partir d’une session SSH pour le nœud principal de votre cluster Kafka.

Pour plus d’informations sur la connexion à HDInsight avec SSH, consultez le document [Utilisation de SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Étapes suivantes

* [Évolutivité de Kafka sur HDInsight](apache-kafka-scalability.md)
* [Mise en miroir avec Kafka sur HDInsight](apache-kafka-mirroring.md)