---
title: Microsoft Cognitive Toolkit avec Azure HDInsight Spark pour la formation approfondie | Microsoft Docs
description: "Découvrez comment un modèle entraîné de formation approfondie Microsoft Cognitive Toolkit peut être appliqué à un jeu de données avec l’API Python Spark dans un cluster Azure HDInsight Spark."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: ebbbcd7a1d61ba0eac13a184aa48cacb800524e8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Utiliser le modèle de formation approfondie Microsoft Cognitive Toolkit avec un cluster Azure HDInsight Spark

Dans cet article, vous suivez les étapes ci-dessous.

1. Exécuter un script personnalisé pour installer Microsoft Cognitive Toolkit sur un cluster Azure HDInsight Spark.

2. Charger un bloc-notes Jupyter sur le cluster Spark pour voir comment appliquer un modèle entraîné de formation approfondie Microsoft Cognitive Toolkit aux fichiers d’un compte de Stockage Blob Azure avec [l’API Spark Python (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html).

## <a name="prerequisites"></a>Prérequis

* **Un abonnement Azure**. Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Voir [Créez votre compte Azure gratuit](https://azure.microsoft.com/free).

* **Cluster Azure HDInsight Spark**. Pour cet article, créez un cluster Spark 2.0. Pour obtenir des instructions, consultez la page [Créer un cluster Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Comment fonctionne cette solution ?

Cette solution est divisée entre cet article et un bloc-notes Jupyter que vous chargez dans le cadre de ce didacticiel. Dans cet article, vous suivrez les étapes ci-dessous :

* Exécuter une action de script sur un cluster HDInsight Spark pour installer Microsoft Cognitive Toolkit et les packages Python.
* Charger le bloc-notes Jupyter qui exécute la solution sur le cluster HDInsight Spark.

Les étapes restantes suivantes sont traitées dans le bloc-notes Jupyter.

- Charger des exemples d’images dans un RDD (Spark Resilient Distributed Dataset)
   - Charger des modules et prédéfinir des paramètres
   - Télécharger le jeu de données localement sur le cluster Spark
   - Convertir le jeu de données en RDD
- Attribuer un score aux images selon un modèle Cognitive Toolkit entraîné
   - Télécharger le modèle Cognitive Toolkit entraîné vers le cluster Spark
   - Définir les fonctions utilisées par les nœuds de travail
   - Attribuer un score aux images sur les nœuds de travail
   - Évaluer la précision du modèle


## <a name="install-microsoft-cognitive-toolkit"></a>Installer Microsoft Cognitive Toolkit

Vous pouvez installer Microsoft Cognitive Toolkit sur un cluster Spark avec une action de script. L’action de script utilise des scripts personnalisés pour installer sur le cluster des composants qui ne sont pas disponibles par défaut. Vous pouvez utiliser le script personnalisé sur le Portail Azure, avec le Kit de développement logiciel (SDK) .NET HDInsight ou Azure PowerShell. Vous pouvez également utiliser le script pour installer la boîte à outils lors de la création du cluster ou lorsque le cluster est prêt à fonctionner. 

Dans cet article, nous utilisons le portail pour installer la boîte à outils une fois le cluster créé. Pour connaître d’autres façons d’exécuter le script personnalisé, consultez la page [Personnaliser des clusters HDInsight avec une action de script](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Utilisation du portail Azure

Pour connaître les instructions liées à l’utilisation du Portail Azure pour exécuter une action de script, consultez la page [Personnaliser des clusters HDInsight avec une action de script](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Veillez à fournir les entrées suivantes pour installer Microsoft Cognitive Toolkit.

* Attribuez une valeur au nom de l’action de script.

* Dans **URI de script bash**, entrez `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`.

* Veillez à exécuter le script uniquement sur les nœuds principal et worker, et décochez les autres cases.

* Cliquez sur **Créer**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Charger le bloc-notes Jupyter sur le cluster Azure HDInsight Spark

Pour utiliser Microsoft Cognitive Toolkit avec le cluster Azure HDInsight Spark, vous devez charger le bloc-notes Jupyter **CNTK_model_scoring_on_Spark_walkthrough.ipynb** sur le cluster Azure HDInsight Spark. Ce bloc-notes est disponible sur GitHub à l’adresse [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration).

1. Clonez le référentiel GitHub [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Vous trouverez des instructions de clonage à la page [Cloner un référentiel](https://help.github.com/articles/cloning-a-repository/).

2. Sur le Portail Azure, ouvrez le panneau du cluster Spark que vous avez déjà configuré, cliquez sur **Tableau de bord du cluster**, puis sur **Bloc-notes Jupyter**.

    Vous pouvez également lancer le bloc-notes Jupyter en accédant à l’URL `https://<clustername>.azurehdinsight.net/jupyter/`. Remplacez \<clustername> par le nom de votre cluster HDInsight.

3. Dans le bloc-notes Jupyter, cliquez sur **Charger** dans le coin supérieur droit, puis accédez à l’emplacement où vous avez cloné le référentiel GitHub.

    ![Charger le bloc-notes Jupyter sur le cluster Azure HDInsight Spark](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Charger le bloc-notes Jupyter sur le cluster Azure HDInsight Spark")

4. Cliquez à nouveau sur **Charger**.

5. Une fois le bloc-notes chargé, cliquez sur son nom, puis suivez les instructions qui se trouvent dans le bloc-notes même pour charger le jeu de données et suivre le didacticiel.

## <a name="see-also"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetry data analysis using Spark in HDInsight (Analyse des données de télémétrie Application Insight à l’aide de Spark dans HDInsight)](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications
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

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
