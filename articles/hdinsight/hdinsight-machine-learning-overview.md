---
title: "Vue d’ensemble du Machine Learning - Azure HDInsight | Microsoft Docs"
description: "Décrit les options de Machine Learning de HDInsight."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: nitinme
ms.openlocfilehash: ff99a7a60573cad5e6dd30d4ca48903423e9f87f
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="machine-learning-on-hdinsight"></a>Machine Learning sur HDInsight

HDInsight autorise un Machine Learning sur le Big Data, en permettant d’extraire de précieuses informations de grandes quantités (pétaoctets voire exaoctets) de structurées et non structurées, ainsi que de données à déplacement rapide. Il existe plusieurs options de Machine Learning dans HDInsight : SparkML et MLlib, R, Hive et Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML et MLlib

[HDInsight Spark](spark/apache-spark-overview.md) est une offre de [Spark](http://spark.apache.org/) hébergée sur Azure, une infrastructure open source de traitement parallèle des données qui utilise le traitement en mémoire pour améliorer les performances des analyses de Big Data. Le moteur de traitement Spark est élaboré pour permettre des analyses rapides, simples d’utilisation et sophistiquées. De par ses capacités de calcul distribué en mémoire, Spark constitue le choix idéal pour les algorithmes itératifs utilisés dans l’apprentissage automatique et les calculs de graphiques. Il existe deux bibliothèques Machine Learning évolutives, qui offrent des fonctionnalités de modélisation d’algorithme à cet environnement distribué : MLlib et SparkML. MLib contient l’API d’origine qui vient au-dessus des RDD. SparkML est un package plus récent qui fournit une API de niveau supérieur reposant sur des trames de données pour construire des pipelines ML. SparkML ne prend pas en charge toutes les fonctionnalités de MLlib, mais remplace MLlib en tant que bibliothèque de Machine Learning standard de Spark.

La bibliothèque Microsoft Machine Learning pour Apache Spark est [MMLSpark](https://github.com/Azure/mmlspark). Cette bibliothèque est conçue pour améliorer la productivité des chercheurs de données sur Spark, accroître le taux d’expérimentation et pour tirer parti des techniques de Machine Learning de pointe, notamment l’apprentissage profond, sur des jeux de données très volumineux. MMLSpark crée une couche au-dessus des API de bas niveau de SparkML lors de la création de modèles ML évolutifs (comme des chaînes d’indexation), lors du formatage de données dans une disposition compatibles avec les algorithmes de Machine Learning et lors de l’assemblage de vecteurs de caractéristiques. La bibliothque MMLSpark simplifie ces opérations ainsi que d’autres tâches courantes permettant de créer des modèles dans PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) est actuellement le langage de programmation statistique le plus populaire au monde. C’est un outil de visualisation de données open source, dont la communauté compte plus de 2,5 millions d’utilisateurs et augmente régulièrement. Avec sa base d’utilisateurs en plein essor et plus de 8 000 packages créés collectivement, R est un choix probable pour de nombreuses entreprises qui ont besoin du Machine Learning. Vous pouvez créer un cluster HDInsight avec R Server, qui est immédiatement utilisable avec les jeux de données et modèles très volumineux. Cette fonctionnalité fournit aux chercheurs de données et aux statisticiens une interface R familière et évolutive à la demande via HDInsight, sans les tâches fastidieuses de création et de maintenance de cluster.

![Formation à la prédiction avec R Server](./media/hdinsight-machine-learning-overview/r-training.png)

Le nœud de périmètre d’un cluster fournit un lieu d’accueil pratique pour la connexion au cluster et l’exécution de vos scripts R.  Vous pouvez également exécuter des scripts R sur les nœuds du cluster, à l’aide des contextes de calcul Hadoop Map Reduce ou Spark de ScaleR.

Avec R Server sur HDInsight et Spark, vous pouvez paralléliser la formation sur les nœuds d’un cluster à l’aide d’un contexte de calcul Spark. Vous pouvez exécuter des scripts R directement sur le nœud de périphérie, grâce à tous les cœurs disponibles en parallèle, en fonction des besoins. L’autre solution consiste à exécuter votre code à partir du nœud de périphérie pour amorcer le traitement qui est réparti entre tous les nœuds du cluster. R Server sur HDInsight avec Spark permet également de paralléliser les fonctions des packages R open source, si vous le souhaitez.

## <a name="azure-machine-learning-and-hive"></a>Azure Machine Learning et Hive

Azure Machine Learning fournit des outils de modélisation d’analyses prédictives, ainsi qu’un service entièrement géré permettant de déployer vos modèles prédictifs sous la forme de services web prêts à l’emploi. Azure Machine Learning est une solution complète d’analyse prédictive sur le cloud, que vous pouvez utiliser pour créer, tester, mettre en service et gérer rapidement des modèles prédictifs. Effectuez une sélection dans une volumineuse bibliothèque d’algorithmes, utilisez un studio web pour générer des modèles et déployez facilement votre modèle en tant que service web.

![Création d’analyses avancées accessibles à Hadoop avec Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Créez des fonctionnalités pour les données dans un cluster HDInsight Hadoop à l’aide de [requêtes Hive](../machine-learning/team-data-science-process/create-features-hive.md). La *conception de fonctionnalités* tente d’augmenter la puissance prédictive des algorithmes d’apprentissage en créant des fonctionnalités à partir des données brutes qui facilitent le processus d’apprentissage. Vous pouvez exécuter des requêtes HiveQL depuis Azure ML et accéder aux données traitées dans Hive et stockées dans le stockage BLOB, à l’aide du [module Import Data (Importer les données)](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

L’[apprentissage profond](https://www.microsoft.com/en-us/research/group/dltc/) est une branche du Machine Learning qui utilise les réseaux neuronaux profonds, à l’image des processus biologiques du cerveau humain. De nombreux chercheurs considèrent l’apprentissage profond comme une approche prometteuse de l’intelligence artificielle. Les traducteurs de langue parlée, les systèmes de reconnaissance d’image et le raisonnement automatique sont quelques exemples d’apprentissage profond.

Dans le cadre de son travail sur l’apprentissage profond, Microsoft a développé [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/), un outil open source gratuit et facile à utiliser. Cette boîte à outils est largement utilisée par un grand nombre de produits Microsoft, par des entreprises du monde entier qui doivent déployer l’apprentissage profond à grande échelle ainsi que par des étudiants intéressés par les techniques et les algorithmes les plus récents. 

## <a name="see-also"></a>Voir aussi

### <a name="scenarios"></a>Scénarios

* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température des bâtiments à l’aide des données des systèmes HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Génération de recommandations sur des vidéos avec Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Hive et Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Hive et Azure Machine Learning de bout en bout](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Machine Learning avec Spark sur HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Ressources d’apprentissage profond

* [Kit d’apprentissage profond avec Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/25/using-microsofts-deep-learning-toolkit-with-spark-on-azure-hdinsight-clusters/)
* [Classification d’images extrêmement parallèles, à l’aide de Cognitive Toolkit et de TensorFlow sur Spark](https://blogs.technet.microsoft.com/machinelearning/2017/04/12/embarrassingly-parallel-image-classification-using-cognitive-toolkit-tensorflow-on-azure-hdinsight-spark/)
