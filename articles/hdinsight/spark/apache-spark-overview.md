---
title: "Présentation de Spark sur Azure HDInsight | Microsoft Docs"
description: "Cet article fournit une présentation Spark sur HDInsight et des différents scénarios dans lesquels vous pouvez utiliser le cluster Spark sur HDInsight."
keywords: "présentation d’apache spark,cluster spark,présentation de spark,spark sur hdinsight"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/13/2017
ms.author: nitinme
ms.openlocfilehash: d2b2e33de25fc5b76b1df4a42fd25e4e0b374439
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="introduction-to-spark-on-hdinsight"></a>Présentation de Spark sur HDInsight

Cet article vous fournit une présentation de Spark sur HDInsight. <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> est une infrastructure de traitement parallèle open source qui prend en charge le traitement en mémoire pour améliorer les performances des applications d’analyse de données volumineuses. Le cluster Spark sur HDInsight est compatible avec le stockage Azure (WASB) ainsi que Azure Data Lake Store. Par conséquent, vos données existantes stockées dans Azure peuvent facilement être traitées via un cluster Spark.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Lorsque vous créez un cluster Spark sur HDInsight, vous créez des ressources de calcul Azure dans lesquelles Spark est installé et configuré. La création d’un cluster Spark dans HDInsight ne prend que 10 minutes environ. Les données à traiter sont stockées dans Azure Storage ou Azure Data Lake Store. Consultez [Utiliser Azure Storage avec HDInsight](../hdinsight-hadoop-use-blob-storage.md).

![Spark : une infrastructure unifiée](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="spark-vs-traditional-mapreduce"></a>Spark et MapReduce traditionnel

Qu’est-ce qui rend Spark rapide ? Quelles sont les différences de l’architecture Apache Spark par rapport à MapReduce traditionnel, qui lui permettent d’offrir de meilleures performances pour le partage des données ?

![MapReduce traditionnel et Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

Spark fournit des primitives pour le calcul de cluster en mémoire. Un travail Spark peut charger et mettre en cache des données en mémoire et les interroger à plusieurs reprises, beaucoup plus rapidement que les systèmes basés sur un disque. Spark s’intègre également dans le langage de programmation Scala pour vous permettre de manipuler des ensembles de données distribuées tels que des collections locales. Il n’est pas nécessaire de tout structurer comme des opérations de réduction et de mappage.

Dans Spark, le partage de données entre les opérations est plus rapide étant donné que les données sont en mémoire. En revanche, Hadoop partage des données via HDFS, rendant leur traitement plus long.

## <a name="what-is-apache-spark-on-azure-hdinsight"></a>Qu’est-ce qu’Apache Spark sur Azure HDInsight ?
Les clusters Spark sur HDInsight proposent un service Spark entièrement géré. Les avantages de la création d’un cluster Spark sur HDInsight sont répertoriés ici.

| Fonctionnalité | Description |
| --- | --- |
| Facilité de création des clusters Spark |Vous pouvez créer un cluster Spark sur HDInsight en quelques minutes en utilisant le portail Azure, Azure PowerShell ou le Kit de développement logiciel (SDK) .NET HDInsight. Consultez [Prise en main des clusters Spark dans HDInsight](apache-spark-jupyter-spark-sql.md) |
| Simplicité d'utilisation |Un cluster Spark sur HDInsight inclut des blocs-notes Jupyter et Zeppelin. Vous pouvez utiliser les blocs-notes pour le traitement interactif et la visualisation des données.|
| API REST |Les clusters Spark sur HDInsight comprennent [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), un serveur de travaux Spark basé sur une API REST, qui permet de soumettre et de surveiller à distance des travaux. |
| Prise en charge d’Azure Data Lake Store | Un cluster Spark sur HDInsight peut être configuré pour utiliser Azure Data Lake Store en tant que stockage supplémentaire, mais aussi en tant que stockage principal (uniquement avec les clusters HDInsight 3.5). Pour plus d’informations sur Data Lake Store, consultez [Vue d’ensemble d’Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md). |
| Intégration aux services Azure |Un cluster Spark sur HDInsight est fourni avec un connecteur à Azure Event Hubs. Les clients peuvent créer des applications de diffusion en continu en utilisant Event Hubs, en plus de [Kafka](http://kafka.apache.org/), qui est déjà disponible dans Spark. |
| Prise en charge du serveur R | Vous pouvez configurer un serveur R sur un cluster Spark HDInsight pour exécuter des calculs R distribués à la vitesse d’un cluster Spark. Pour plus d’informations, consultez la section [Prise en main de R Server sur HDInsight](../r-server/r-server-get-started.md). |
| Intégration à des environnements de développement intégrés tiers | HDInsight fournit des plug-ins pour IDE tels qu’IntelliJ IDEA et Eclipse que vous pouvez utiliser pour créer et soumettre des applications sur un cluster Spark HDInsight. Pour plus d’informations, consultez [Utiliser le kit de ressources Azure pour IntelliJ IDEA](apache-spark-intellij-tool-plugin.md) et [Utiliser le kit de ressources Azure pour Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Requêtes simultanées |Les clusters Spark sur HDInsight prennent en charge les requêtes simultanées. Ainsi, plusieurs requêtes d’un même utilisateur ou plusieurs requêtes de différents utilisateurs et applications peuvent partager les mêmes ressources de cluster. |
| Mise en cache sur des disques SSD |Vous pouvez choisir de mettre en cache des données en mémoire ou dans les disques SSD attachés aux nœuds de cluster. La mise en cache en mémoire fournit les meilleures performances de requêtes, mais peut s’avérer coûteuse. La mise en cache sur des disques SSD constitue une très bonne option pour améliorer les performances des requêtes sans nécessité de créer un cluster d’une taille requise pour tenir l’ensemble du jeu de données en mémoire. |
| Intégration aux outils décisionnels |Les clusters Spark sur HDInsight fournissent des connecteurs pour certains outils décisionnels, notamment [Power BI](http://www.powerbi.com/) et [Tableau](http://www.tableau.com/products/desktop), pour l’analyse des données. |
| Bibliothèques Anaconda préchargées |Les clusters Spark sur HDInsight sont fournis avec des bibliothèques Anaconda préinstallées. [Anaconda](http://docs.continuum.io/anaconda/) fournit près de 200 bibliothèques pour l’apprentissage automatique, l’analyse des données, la visualisation, etc. |
| Extensibilité |Bien que vous puissiez spécifier le nombre de nœuds dans votre cluster lors de sa création, vous pourriez souhaiter augmenter ou réduire la taille du cluster pour l’adapter à votre charge de travail. Tous les clusters HDInsight vous permettent de modifier le nombre de nœuds du cluster. En outre, les clusters Spark peuvent être supprimés sans perte de données puisque toutes les données sont stockées dans Azure Storage ou Data Lake Store. |
| Support technique 24 heures sur 24, 7 jours sur 7 |Les clusters Spark sur HDInsight sont fournis avec un support technique à l’échelle de l’entreprise assuré 24 heures sur 24, 7 jours sur 7 et un contrat de niveau de service de 99,9 % de disponibilité. |

## <a name="spark-cluster-architecture"></a>Architecture d’un cluster Spark

Voici l’architecture d’un cluster Spark et son mode de fonctionnement :

![Architecture d’un cluster Spark](./media/apache-spark-overview/spark-architecture.png)

Le nœud principal est le maître Spark qui gère le nombre d’applications, les applications sont mappées au pilote Spark. Chaque application est gérée de différentes manières par le maître Spark. Spark peut être déployé sur Mesos, YARN ou le gestionnaire de cluster Spark, qui alloue des ressources de nœud Worker à une application. Dans HDInsight, Spark s’exécute à l’aide du gestionnaire de cluster YARN. Les ressources dans le cluster sont gérées par le maître Spark dans HDInsight. Cela signifie que le maître Spark a connaissance de l’état d’occupation ou de disponibilité des ressources, comme la mémoire, sur le nœud Worker.

Le pilote exécute la fonction principale de l’utilisateur et exécute les différentes opérations parallèles sur les nœuds Worker. Ensuite, le pilote collecte les résultats des opérations. Les nœuds Worker lisent et écrivent des données depuis/sur le système de fichiers distribués Hadoop (HDFS). Les nœuds Worker mettent également en cache les données transformées en mémoire comme les jeux de données résilients distribués (RDD).

Une fois l’application créée dans le maître Spark, les ressources sont allouées aux applications par le maître Spark, créant une exécution nommée pilote Spark. Le pilote Spark crée également le SparkContext et démarre également la création des RDD. Les métadonnées des RDD sont stockées sur le pilote Spark.

Le pilote Spark se connecte au maître Spark et est responsable de la conversion d’une application à un graphe orienté (DAG) des tâches individuelles qui sont exécutées dans un processus d’exécution sur les nœuds Worker. Chaque application obtient ses propres processus d’exécution qui restent pour la durée de toute l’application et exécutent des tâches sur plusieurs threads.

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Quels sont les cas d’utilisation de Spark sur HDInsight ?
Les clusters Spark sur HDInsight autorisent les principaux scénarios suivants :

### <a name="interactive-data-analysis-and-bi"></a>Analyse des données interactive et Power BI
[Consulter un didacticiel](apache-spark-use-bi-tools.md)

Apache Spark sur HDInsight stocke les données dans Azure Storage ou Azure Data Lake Store. Des experts et des décideurs clés peuvent analyser les données, générer les rapports correspondants et utiliser Microsoft Power BI pour créer des rapports interactifs à partir des données analysées. Les analystes peuvent démarrer à partir des données non structurées/semi-structurées dans le stockage du cluster, définir un schéma pour les données à l’aide des blocs-notes, puis générer des modèles de données à l’aide de Microsoft Power BI. Les clusters Spark sur HDInsight prennent également en charge plusieurs outils décisionnels tiers comme Tableau, grâce auxquels ils constituent une plateforme idéale pour les analystes de données, les experts et les décideurs clés.

### <a name="spark-machine-learning"></a>Spark Machine Learning
[Consulter un didacticiel : prédiction des températures d’un bâtiment en utilisant des données HVAC](apache-spark-ipython-notebook-machine-learning.md)

[Consulter un didacticiel : prédiction des résultats d’inspections alimentaires](apache-spark-machine-learning-mllib-ipython.md)

Apache Spark est fourni avec [MLlib](http://spark.apache.org/mllib/), bibliothèque d’apprentissage automatique basée sur Spark que vous pouvez utiliser à partir d’un cluster Spark sur HDInsight. Un cluster Spark sur HDInsight inclut également Anaconda, une distribution de Python avec une variété de packages pour l’apprentissage automatique. Ajoutez à cela la prise en charge intégrée des blocs-notes Jupyter et Zeppelin, et vous disposez d’un environnement haut de gamme pour la création d’applications d’apprentissage automatique.

### <a name="spark-streaming-and-real-time-data-analysis"></a>Analyse des données de diffusion en continu et en temps réel Spark

Les clusters Spark sur HDInsight offrent une prise en charge améliorée de la création de solutions d’analyse en temps réel. Si Spark possède déjà des connecteurs pour la réception des données provenant de nombreuses sources telles que les sockets Kafka, Flume, Twitter, ZeroMQ ou TCP, Spark dans HDInsight ajoute la prise en charge de première classe de la réception des données provenant d’Azure Event Hubs. La fonctionnalité Event Hubs est le service de file d’attente le plus largement utilisé sur Azure. Grâce à leur prise en charge immédiate des concentrateurs d’événements, les clusters Spark sur HDInsight constituent une plateforme idéale pour créer un pipeline d’analyse en temps réel.

## <a name="next-steps"></a>Quels sont les composants inclus dans un cluster Spark ?
Les clusters Spark sur HDInsight incluent les composants suivants qui sont disponibles dans les clusters par défaut.

* [Spark Core](https://spark.apache.org/docs/1.5.1/). Comprend Spark Core, Spark SQL, les API de diffusion en continu Spark, GraphX et MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Bloc-notes Jupyter](https://jupyter.org)
* [Bloc-notes Zeppelin](http://zeppelin-project.org/)

Les clusters Spark sur HDInsight fournissent également un [pilote ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) pour la connectivité aux clusters Spark sur HDInsight à partir d’outils décisionnels comme Microsoft Power BI et Tableau.

## <a name="where-do-i-start"></a>Par où commencer ?
Commencez par créer un cluster Spark sur HDInsight. Consultez [Démarrage rapide : créer un cluster Spark sur HDInsight Linux et exécuter une requête interactive à l’aide de Jupyter](apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Étapes suivantes
### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisation du plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
* [Problèmes connus d’Apache Spark sur Azure HDInsight](apache-spark-known-issues.md).
