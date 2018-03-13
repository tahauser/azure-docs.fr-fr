---
title: Utiliser Interactive Query avec Azure HDInsight | Microsoft Docs
description: "Découvrez comment utiliser Interactive Query (Hive LLAP) avec HDInsight."
keywords: 
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0957643c-4936-48a3-84a3-5dc83db4ab1a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: 1e094c801ffc9bc51609d119b0e6ad80ff17a091
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/23/2018
---
# <a name="use-interactive-query-with-hdinsight"></a>Utiliser Interactive Query avec HDInsight
Le cluster Interactive Query (également appelé Hive LLAP ou [Live Long and Process](https://cwiki.apache.org/confluence/display/Hive/LLAP)) est un [type de cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types) Azure HDInsight. Interactive Query prend en charge la mise en mémoire cache, ce qui accélère les requêtes Hive et les rend beaucoup plus interactives.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Les clusters Interactive Query sont différents des clusters Hadoop. Ils contiennent uniquement le service Hive. 

> [!NOTE]
> Vous pouvez accéder au service Hive dans le cluster Interactive Query uniquement par le biais de l’affichage Ambari Hive, de Beeline et du pilote ODBC Microsoft Hive. Vous ne pouvez pas y accéder à travers la console Hive, Templeton, l’outil en ligne de commande Azure (Azure CLI) ni Azure PowerShell. 
> 
> 

## <a name="create-an-interactive-query-cluster"></a>Créer un cluster Interactive Query
Pour obtenir des informations sur la création d’un cluster HDInsight, consultez [Créer des clusters Hadoop dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Choisissez le type de cluster Interactive Query.

## <a name="execute-hive-queries-from-interactive-query"></a>Exécuter des requêtes Hive à partir du cluster Interactive Query
Pour exécuter des requêtes Hive, vous disposez des options suivantes :

* Utiliser Power BI

    Consultez [Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) Consultez [Visualiser des données volumineuses (« Big Data ») avec Power BI dans Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
 
* Utilisez Zeppelin

    Consultez [Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).

* Utiliser Visual Studio

    Consultez [Se connecter à Azure HDInsight et exécuter des requêtes Hive avec Data Lake Tools pour Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).

* Utiliser Visual Studio Code

    Consultez [Utiliser Visual Studio Code pour Hive, LLAP ou pySpark](../hdinsight-for-vscode.md).
* Exécuter Hive à l’aide de l’affichage Ambari Hive.
  
    Consultez [Utiliser la vue Hive avec Hadoop dans Azure HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Exécuter Hive à l’aide de Beeline.
  
    Consultez [Utiliser Hive avec Hadoop dans HDInsight via Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Vous pouvez utiliser Beeline à partir du nœud principal ou d’un nœud de périphérie vide. L’utilisation de Beeline à partir d’un nœud de périphérie vide est recommandée. Pour plus d’informations sur la création d’un cluster HDInsight avec un nœud de périphérie vide, consultez [Utiliser des nœuds de périphérie vides dans HDInsight](../hdinsight-apps-use-edge-node.md).
* Exécuter Hive à l’aide d’ODBC Hive.
  
    Consultez [Connexion d’Excel à Hadoop à l’aide du pilote ODBC Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

Pour rechercher la chaîne de connexion Java Database Connectivity (JDBC) :

1. Connectez-vous à Ambari à travers l’URL suivante : https://\<nom du cluster\>.AzureHDInsight.net.
2. Dans le menu de gauche, sélectionnez **Hive**.
3. Pour copier l’URL, sélectionnez l’icône du Presse-papiers :
   
   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [créer des clusters Interactive Query dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Découvrez comment [visualiser des Big Data à l’aide de Power BI dans Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Découvrez comment [utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight](../hdinsight-connect-hive-zeppelin.md).
* Découvrez comment [exécuter des requêtes Hive à l’aide de Data Lake Tools pour Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-hive-queries).
* Découvrez comment [utiliser les outils HDInsight pour Visual Studio Code](../hdinsight-for-vscode.md).
* Découvrez comment [utiliser la vue Hive avec Hadoop dans HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Découvrez comment [utiliser Beeline pour envoyer des requêtes Hive dans HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md).
* Découvrez comment [connecter Excel à Hadoop avec le pilote ODBC Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

