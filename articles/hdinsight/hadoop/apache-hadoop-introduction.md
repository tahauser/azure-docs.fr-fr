---
title: "Qu’est-ce que HDInsight et la pile de technologies Hadoop et Spark ? - Azure | Documents Microsoft"
description: "Présentation de HDInsight et de la pile de technologies et des composants Hadoop et Spark, notamment Kafka, Hive, Storm, et HBase pour l’analyse de données volumineuses (Big Data)."
keywords: "azure hadoop, hadoop azure, intro hadoop, introduction hadoop, pile de technologies hadoop, intro à hadoop, introduction à hadoop, qu’est-ce qu’un cluster hadoop, qu’est-ce un cluster hadoop, pourquoi hadoop est utilisé"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/13/2017
ms.author: cgronlun
ms.openlocfilehash: 369d4444e52083c689441548dcfab70fe49ab346
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="introduction-to-azure-hdinsight-and-the-hadoop-and-spark-technology-stack"></a>Présentation d’Azure HDInsight et de la pile de technologies Hadoop et Spark
Cet article vous fournit une présentation d’Azure HDInsight. Azure HDInsight est un service d’analyse entièrement géré, complet et open source pour les entreprises. Vous pouvez utiliser les infrastructures open source telles que Hadoop, Spark, Hive, LLAP, Kafka, Storm, R et bien plus encore. 

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

[Apache Hadoop](http://hadoop.apache.org/) était l’infrastructure open source d’origine de traitement et d’analyse distribués des jeux de données volumineuses sur des clusters. La pile de technologies Hadoop inclut des logiciels et utilitaires liés, notamment Apache Hive, HBase, Spark, Kafka et bien d’autres encore. 

Pour voir les piles de composants de technologie Hadoop disponibles sur HDInsight, consultez [Composants et versions disponibles avec HDInsight][component-versioning]. Pour plus d’informations sur Hadoop dans HDInsight, consultez la rubrique [Page de fonctionnalités Azure pour HDInsight](https://azure.microsoft.com/services/hdinsight/).

[Apache Spark](http://spark.apache.org) est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analyse de données volumineuses. Pour en savoir plus sur Spark dans HDInsight, consultez la [Présentation de Spark sur Azure HDInsight](../spark/apache-spark-overview.md). 

<a href="https://ms.portal.azure.com/#create/Microsoft.HDInsightCluster" target="_blank"><img src="./media/apache-hadoop-introduction/deploy-to-azure.png" alt="Deploy an Azure HDInsight cluster"></a>

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Qu’est-ce que HDInsight et la pile de technologies Hadoop ? 
Azure HDInsight est une distribution par cloud des composants Hadoop à partir de [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/). Azure HDInsight rend facile, rapide et économique le traitement de volumes importants de données. Vous pouvez utiliser les infrastructures open source les plus populaires, telles que Hadoop, Spark, Hive, LLAP, Kafka, Storm, R et bien plus encore. Avec ces infrastructures, vous pouvez activer un large éventail de scénarios, tels que l’extraction, la transformation et le chargement (ETL) ; l’entreposage de données ; l’apprentissage automatique ; et IoT.

## <a name="what-is-big-data"></a>Que sont les données volumineuses ?

Les données volumineuses sont collectées dans des volumes toujours plus importants, à des vitesses élevées et pour une variété de formats plus grande qu’auparavant. Elles peuvent être historiques (c’est-à-dire stockées) ou en temps réel (c’est-à-dire diffusées à partir de la source). Consultez [Scénarios d’utilisation d’ HDInsight](#scenarios-for-using-hdinsight) pour en savoir plus sur les cas d’usage courants pour les Big Data.

## <a name="why-should-i-use-hdinsight"></a>Pourquoi dois-je utiliser HDInsight ?

Cette section répertorie les fonctionnalités d’Azure HDInsight.


|Fonctionnalité  |Description  |
|---------|---------|
|Cloud natif     |     Azure HDInsight vous permet de créer des clusters optimisés pour [Hadoop](apache-hadoop-linux-tutorial-get-started.md),  [Spark](../spark/apache-spark-jupyter-spark-sql.md),  [Interactive query (LLAP)](../interactive-query/apache-interactive-query-get-started.md),  [Kafka](../kafka/apache-kafka-get-started.md),  [Storm](../storm/apache-storm-tutorial-get-started-linux.md),  [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md), et  [R Server](../r-server/r-server-get-started.md) sur Azure. HDInsight fournit également un contrat SLA de bout en bout sur toutes vos charges de travail de production.  |
|Économique et évolutif     | HDInsight vous permet de monter ou de descendre en [puissance](../hdinsight-administer-use-portal-linux.md)  les charges de travail. Vous pouvez réduire les coûts en [créant des clusters à la demande](../hdinsight-hadoop-create-linux-clusters-adf.md) et payer uniquement ce que vous utilisez. Vous pouvez également créer des pipelines de données pour faire fonctionner vos travaux. Le stockage et le calcul découplés améliorent les performances et la flexibilité. |
|Sécurité et conformité    | HDInsight vous permet de protéger les ressources de données de votre entreprise à l’aide du [réseau virtuel Azure](../hdinsight-extend-hadoop-virtual-network.md), du [chiffrement](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)et de l’intégration avec [Azure Active Directory](../domain-joined/apache-domain-joined-introduction.md). HDInsight répond également aux [normes de conformité](https://azure.microsoft.com/overview/trusted-cloud) du gouvernement et du secteur les plus populaires.        |
|Surveillance    | Azure HDInsight s’intègre à [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) pour fournir une interface unique permettant de gérer l’ensemble des clusters.        |
|Disponibilité générale | HDInsight est disponible dans plus de 25  [régions](https://azure.microsoft.com/regions/services/) , soit plus que tout autre offre d’analytique Big Data. Azure HDInsight est également disponible dans Azure Government, en Chine, et en Allemagne, ce qui vous permet de répondre aux besoins de votre entreprise dans les principaux domaines souverains. |  
|Productivité     |  Azure HDInsight vous permet d’utiliser des outils de productivité enrichis pour Hadoop et Spark avec les environnements de développement de votre choix. Ces environnements de développement incluent [Visual Studio](apache-hadoop-visual-studio-tools-get-started.md), [Eclipse](../spark/apache-spark-eclipse-tool-plugin.md), et [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md) pour la prise en charge de Scala, Python, R, Java et .NET. Les scientifiques des données peuvent également collaborer à l’aide de blocs-notes populaires, tels que [Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) et [Zeppelin](../spark/apache-spark-zeppelin-notebook.md).    |
|Extensibilité     |  Vous pouvez étendre les clusters HDInsight avec des composants installés (Hue, Presto, etc.) à l’aide d’[actions de script](../hdinsight-hadoop-customize-cluster-linux.md), par l’[ajout de nœuds de périphérie](../hdinsight-apps-use-edge-node.md), ou l’[intégration à d’autres applications certifiées Big data](../hdinsight-apps-install-applications.md). HDInsight permet une intégration transparente aux solutions Big Data les plus populaires à l’aide d’un déploiement [en un clic](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Scénarios d’utilisation de HDInsight

Azure HDInsight peut être utilisé pour divers scénarios lors d’un traitement de données Big data. Il peut s’agir de données historiques (déjà collectées et stockées) ou de données en temps réel (transmises en continu directement à partir de la source). Les scénarios pour le traitement de ces données peuvent être classés dans les catégories suivantes : 

### <a name="batch-processing-etl"></a>Traitement par lots (ETL)

Extraction, transformation et chargement (ETL) est un processus au cours duquel les données structurées ou non sont extraites à partir de sources de données hétérogènes. Elles sont ensuite converties dans un format structuré et chargées dans un magasin de données. Les données converties peuvent être utilisées pour la science des données ou l’entreposage de données.

### <a name="internet-of-things-iot"></a>Internet des objets (IoT)

Vous pouvez utiliser HDInsight pour traiter des données de diffusion en continu reçues en temps réel depuis divers appareils. Pour plus d’informations, [lire ce billet de blog Azure annonçant la version préliminaire publique de Apache Kafka sur HDInsight avec Azure Managed Disks](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![Architecture HDInsight : Internet des objets](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png) 

### <a name="data-science"></a>Science des données

Vous pouvez utiliser HDInsight pour créer des applications qui extraient des informations critiques à partir des données. Vous pouvez également utiliser Azure Machine Learning pour prédire les tendances futures de votre activité. Pour plus d’informations, [consultez ce témoignage client](https://customers.microsoft.com/story/pros).

![Architecture de HDInsight : science des données](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png)

### <a name="data-warehousing"></a>Entrepôt de données

HDInsight permet d’exécuter des requêtes interactives sur des pétaoctets de données structurées ou non dans n’importe quel format. Vous pouvez également créer des modèles en les connectant à des outils BI. Pour plus d’informations, [consultez ce témoignage client](https://customers.microsoft.com/story/milliman). 

![Architecture de HDInsight : entrepôt de données](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png)

### <a name="hybrid"></a>Hybride

HDInsight permet d’étendre votre infrastructure Big Data locale existante sur Azure pour exploiter les fonctionnalités d’analyse avancée du cloud.

![Architecture HDInsight : hybride](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png)

## <a name="cluster-types-in-hdinsight"></a>Types de cluster dans HDInsight
HDInsight comprend des types de cluster spécifiques et des fonctionnalités de personnalisation de cluster, comme l’ajout de composants, d’utilitaires et de langages.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark, Kafka, Interactive Query, HBase, personnalisés et autres types de cluster
HDInsight offre les types de clusters suivants :

* **[Apache Hadoop](https://wiki.apache.org/hadoop)** : infrastructure qui utilise [HDFS](#hdfs), la gestion de ressources [YARN](#yarn) et un modèle de programmation [MapReduce](#mapreduce) simple pour traiter et analyser les lots de données en parallèle.

* **[Apache Spark](http://spark.apache.org/)** : infrastructure de traitement parallèle qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analyse de données volumineuses. Spark fonctionne pour SQL, la diffusion de données et l’apprentissage automatique. Consultez [Qu’est-ce qu’Apache Spark dans HDInsight ?](../spark/apache-spark-overview.md)

* **[Apache HBase](http://hbase.apache.org/)** : base de données NoSQL basée sur Hadoop qui fournit un accès aléatoire et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées (potentiellement, des milliards de lignes multipliées par des millions de colonnes). Consultez [Qu’est-ce que HBase sur HDInsight ?](../hbase/apache-hbase-overview.md)

* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)** : serveur pour l’hébergement et la gestion de processus R distribués en parallèle. Ce serveur offre aux experts en science des données, aux statisticiens et aux programmeurs R un accès à la demande à des méthodes d’analyse évolutives et distribuées sur HDInsight. Consultez l’article [Vue d’ensemble : R Server sur HDInsight](../r-server/r-server-overview.md).

* **[Apache Storm](https://storm.incubator.apache.org/)** : système de calcul distribué et en temps réel permettant le traitement rapide de vastes flux de données. Storm est fourni en tant que cluster géré dans HDInsight. Consultez la rubrique [Analyse de données de capteur en temps réel au moyen de Storm et de Hadoop](../storm/apache-storm-sensor-data-analysis.md).

* **[Version préliminaire d’Apache Interactive Query (également appelé Live Long and Process)](https://cwiki.apache.org/confluence/display/Hive/LLAP)** : mise en cache en mémoire autorisant des requêtes Hive interactives et plus rapides. Consultez l’article [Utilisation d’Interactive Query dans HDInsight](../interactive-query/apache-interactive-query-get-started.md).

* **[Apache Kafka](https://kafka.apache.org/)** : plateforme open source utilisée pour créer des applications et des pipelines de données de diffusion en continu. Kafka fournit également une fonctionnalité de file d’attente de messages qui vous permet de publier des flux de données et de vous abonner à ces derniers. Consultez l’article [Présentation d’Apache Kafka sur HDInsight](../kafka/apache-kafka-introduction.md).

## <a name="open-source-components-in-hdinsight"></a>Composants open source dans HDInsight

Azure HDInsight permet de créer des clusters avec des infrastructures open source, telles que Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase et R. Ces clusters sont fournis par défaut avec d’autres composants open source inclus sur le cluster, tels que [Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](http://avro.apache.org/docs/current/spec.html), [Hive](http://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Mahout](https://mahout.apache.org/), [MapReduce](http://wiki.apache.org/hadoop/MapReduce), [YARN](http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Phoenix](http://phoenix.apache.org/), [Pig](http://pig.apache.org/), [Sqoop](http://sqoop.apache.org/), [Tez](http://tez.apache.org/), [Oozie](http://oozie.apache.org/), [ZooKeeper](http://zookeeper.apache.org/).  


## <a name="programming-languages-in-hdinsight"></a>Langages de programmation dans HDInsight
Les clusters HDInsight (dont Spark, HBase, Kafka, Hadoop et d’autres) prennent en charge de nombreux langages de programmation. Certains langages de programmation ne sont pas installés par défaut. Pour les bibliothèques, modules ou packages non installés par défaut, [utilisez une action de script pour installer le composant](../hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Prise en charge des langages de programmation par défaut
Par défaut, HDInsight prend en charge :

* Java
* Python

Vous pouvez installer des langages supplémentaires à l’aide d’[actions de script](../hdinsight-hadoop-script-actions-linux.md).

### <a name="java-virtual-machine-jvm-languages"></a>Langages de machines virtuelles Java (JVM)
De nombreux langages autres que Java peuvent s’exécuter sur une machine virtuelle Java (JVM). Toutefois, si vous exécutez certains de ces langages, vous devrez peut-être installer des composants supplémentaires sur le cluster.

Les langages JVM suivants sont pris en charge sur les clusters HDInsight :

* Clojure
* Jython (Python pour Java)
* Scala

### <a name="hadoop-specific-languages"></a>Langages spécifiques à Hadoop
Les clusters HDInsight prennent en charge les langages suivants, spécifiques à la pile de technologies Hadoop :

* Pig Latin pour les travaux Pig
* HiveQL pour les travaux Hive et SparkSQL

## <a name="business-intelligence-on-hdinsight"></a>Le décisionnel sur HDInsight
Les outils décisionnels courants permettent de récupérer des données intégrées à HDInsight, de les analyser et de générer des rapports à leur sujet via le complément Power Query ou le Pilote ODBC Microsoft Hive :

* [Apache Spark BI utilisant des outils de visualisation de données avec Azure HDInsight](../spark/apache-spark-use-bi-tools.md)

* [Visualiser des données Hive à l’aide de Microsoft Power BI dans Azure HDInsight](apache-hadoop-connect-hive-power-bi.md) 

* [Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Connecter Excel à Hadoop avec Power Query](apache-hadoop-connect-excel-power-query.md) : découvrez comment connecter Excel au compte de stockage Azure stockant les données à partir de votre cluster HDInsight à l’aide de Microsoft Power Query pour Excel. Station de travail Windows requise. 

* [Connexion d’Excel à Hadoop à l’aide du pilote ODBC Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md) : apprenez à importer des données à partir de HDInsight avec le pilote ODBC Microsoft Hive. Station de travail Windows requise. 

* [Plateforme cloud de Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): découvrez Power BI pour Office 365, téléchargez la version d’évaluation de SQL Server, et configurez SharePoint Server 2013 et SQL Server BI.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Étapes suivantes

* [Prise en main de Hadoop dans HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Prise en main de Spark dans HDInsight](../spark/apache-spark-jupyter-spark-sql.md)
* [Prise en main de Kafka sur HDInsight](../kafka/apache-kafka-get-started.md)
* [Prise en main de Storm sur HDInsight](../storm/apache-storm-tutorial-get-started-linux.md)
* [Prise en main de HBase sur HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
* [Prise en main de Interactive Query (LLAP) sur HDInsight](../interactive-query/apache-interactive-query-get-started.md)
* [Prise en main de R Server sur HDInsight](../r-server/r-server-get-started.md)
* [Gérer des clusters HDInsight](../hdinsight-administer-use-portal-linux.md)
* [Sécuriser vos clusters HDInsight](../domain-joined/apache-domain-joined-introduction.md)
* [Surveiller des clusters HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md)


[component-versioning]: ../hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/
