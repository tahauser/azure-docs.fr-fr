---
title: Architecture Hadoop - Azure HDInsight | Microsoft Docs
description: "Décrit le stockage et le traitement Hadoop sur les clusters HDInsight."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/19/2018
ms.author: ashishth
ms.openlocfilehash: 49277871026e79b871b0216c05e051a1c93336b3
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="hadoop-architecture-in-hdinsight"></a>Architecture Hadoop dans HDInsight

Hadoop inclut deux composants essentiels, à savoir High Density File System (HDFS) qui fournit une capacité de stockage, et Yet Another Resource Negotiator (YARN) qui fournit une capacité de traitement. Ces capacités permettent à un cluster d’exécuter les programmes MapReduce pour procéder au traitement des données souhaité.

> [!NOTE]
> Un système HDFS n’est généralement pas déployé au sein du cluster HDInsight pour fournir le stockage. Une couche d’interface compatible HDFS est utilisée à la place par les composants Hadoop. La capacité de stockage réelle est fournie par le stockage Azure ou Azure Data Lake Store. Pour Hadoop, les tâches MapReduce exécutées sur le cluster HDInsight sont traitées comme si un système HDFS était présent. Elles ne nécessitent par conséquent aucune modification pour prendre en charge leurs besoins en stockage. Dans Hadoop sur HDInsight, le stockage est externalisé, mais le traitement YARN reste un composant principal. Pour plus d’informations, consultez [Présentation d’Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Cet article présente YARN et décrit comment il coordonne l’exécution des applications sur HDInsight.

## <a name="yarn-basics"></a>Concepts de base de YARN 

YARN gouverne et orchestre le traitement des données dans Hadoop. YARN inclut deux services principaux qui s’exécutent en tant que processus sur des nœuds du cluster : 

* ResourceManager 
* NodeManager

ResourceManager accorde des ressources de calcul de cluster aux applications telles que les tâches MapReduce. ResourceManager accorde ces ressources en tant que conteneurs, où chaque conteneur est composé d’une allocation de cœurs de processeur et de mémoire RAM. Si vous avez combiné toutes les ressources disponibles dans un cluster, puis les avez distribuées en blocs avec une quantité donnée de cœurs et de mémoire, chaque bloc de ressources est un conteneur. Étant donné que chaque nœud du cluster a une capacité pour un certain nombre de conteneurs, le nombre de conteneurs disponibles du cluster est limité. L’allocation de ressources dans un conteneur est configurable. 

Quand une application MapReduce s’exécute sur un cluster, ResourceManager fournit à l’application les conteneurs dans lesquels elle doit s’exécuter. ResourceManager effectue le suivi de l’état de l’exécution des applications, de la capacité disponible sur le cluster et des applications au fur et à mesure qu’elles se terminent et libèrent leurs ressources. 

ResourceManager exécute également un processus de serveur web qui fournit une interface utilisateur web que vous pouvez utiliser pour effectuer le suivi de l’état des applications. 

Quand un utilisateur soumet une application MapReduce à exécuter sur le cluster, l’application est soumise à ResourceManager. À son tour, ResourceManager alloue un conteneur sur les nœuds NodeManager disponibles. Ces nœuds représentent l’emplacement où l’application s’exécute réellement. Le premier conteneur alloué exécute une application spéciale, appelée ApplicationMaster. Celle-ci se charge de l’acquisition des ressources, sous la forme de conteneurs suivants, nécessaires pour exécuter l’application soumise. ApplicationMaster examine les étapes de l’application (l’étape de mappage et l’étape de réduction), ainsi que les facteurs permettant de déterminer la quantité de données à traiter. ApplicationMaster demande ensuite (*négocie*) les ressources auprès de ResourceManager pour le compte de l’application. ResourceManager accorde à son tour des ressources à partir des NodeManagers dans le cluster à ApplicationMaster qui peut les utiliser pour exécuter l’application. 

Les NodeManagers exécutent les tâches qui forment l’application, puis informent ApplicationMaster de leur progression et de leur l’état. ApplicationMaster informe à son tour ResourceManager de l’état de l’application. ResourceManager retourne les résultats au client.

## <a name="yarn-on-hdinsight"></a>YARN sur HDInsight

Tous les types de clusters HDInsight déploient YARN. ResourceManager est déployé pour une haute disponibilité avec une instance principale et secondaire, qui s’exécutent respectivement sur les premier et deuxième nœuds de tête au sein du cluster. Seule une instance de ResourceManager est active à la fois. Les instances de NodeManager s’exécutent sur les nœuds de travail disponibles dans le cluster.

![YARN sur HDInsight](./media/hdinsight-hadoop-architecture/yarn-on-hdinsight.png)

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de MapReduce dans Hadoop sur HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Présentation d’Azure HDInsight](hadoop/apache-hadoop-introduction.md)
