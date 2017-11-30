---
title: Utiliser des applications de Machine Learning Apache Spark sur HDInsight | Documents Microsoft
description: "Instructions détaillées sur la manière de créer des applications de Machine Learning Apache Spark sur des clusters Spark HDInsight à l’aide d’un bloc-notes Jupyter"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f584ca5e-abee-4b7c-ae58-2e45dfc56bf4
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: ec69dfb1a0d43b73efbada654175c54bf315a1e5
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Créer des applications de Machine Learning Apache Spark sur Azure HDInsight

Découvrez comment créer une application de Machine Learning Apache Spark à l’aide d’un cluster Spark sur HDInsight. Cet article montre comment utiliser le bloc-notes Jupyter disponible avec le cluster pour créer et tester l’application. L’application utilise l’exemple de données HVAC.csv, qui est disponible par défaut sur tous les clusters.

**Configuration requise :**

Vous devez disposer des éléments suivants :

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Comprendre le jeu de données
Avant de créer l’application, nous devons comprendre la structure des données pour lesquelles nous la créons, et le type d’analyse que nous allons effectuer sur ces données. 

Dans cet article, nous utilisons l’exemple de fichier de données **HVAC.csv** qui est disponible dans le compte de stockage Azure que vous avez associé au cluster HDInsight. Dans le compte de stockage, le fichier se trouve sous **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Téléchargez et ouvrez le fichier CSV pour obtenir un instantané des données.  

![Capture instantanée des données utilisées pour l’exemple de Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Capture instantanée des données utilisées pour l’exemple de Machine Learning Spark")

Les données indiquent la température cible et la température réelle d’un bâtiment dans lequel un système HVAC est installé. Supposons que la colonne **System** représente l’ID système et que la colonne **SystemAge** représente le nombre d’années écoulées depuis la mise en place du système HVAC dans le bâtiment.

Nous utilisons ces données pour prédire si un bâtiment sera plus chaud ou plus froid en fonction de la température cible, étant donné un ID système et une ancienneté du système.

## <a name="app"></a>Écrire une application de Machine Learning Spark à l’aide de Spark MLlib
Dans cette application, nous utilisons un pipeline Spark ML pour effectuer une classification de documents. Dans le pipeline, nous fractionnons le document en mots, que nous convertissons en vecteur de fonctionnalité numérique avant de créer un modèle de prédiction utilisant les vecteurs et étiquettes de fonctionnalité. Procédez comme suit pour créer l’application.

1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.   
2. Dans le panneau du cluster Spark, cliquez sur **Tableau de bord du cluster**, puis sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.
   
   > [!NOTE]
   > Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 
3. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.
   
    ![Créer un bloc-notes Jupyter pour l’exemple de Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-create-notebook.png "Créer un bloc-notes Jupyter pour l’exemple de Machine Learning Spark")
4. Un nouveau bloc-notes est créé et ouvert sous le nom Untitled.pynb. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial.
   
    ![Fournir un nom de bloc-notes pour l’exemple de Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-notebook-name.png "Fournir un nom de bloc-notes pour l’exemple de Machine Learning Spark")
5. Comme vous avez créé un bloc-notes à l’aide du noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. Vous pouvez commencer par importer les types requis pour ce scénario. Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**. 
   
        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
   
        import os
        import sys
        from pyspark.sql.types import *
   
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
6. À présent, vous devez charger les données (hvac.csv), les analyser et les utiliser pour effectuer l’apprentissage du modèle. Pour ce faire, vous devez définir une fonction qui vérifie si la température réelle du bâtiment est supérieure à la température cible. Si la température actuelle est supérieure, le bâtiment est chaud, ce qui est indiqué par la valeur **1,0**. Si la température actuelle est inférieure, le bâtiment est froid, ce qui est indiqué par la valeur **0,0**. 
   
    Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **Maj+Entrée**.

        # List the structure of data for better understanding. Because the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument

        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        

            textValue = str(values[4]) + " " + str(values[5])

            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


1. Configurez le pipeline Spark ML qui est composé de trois étapes : Tokenizer, HashingTF et LogisticRegression. Pour plus d’informations sur ce qu’est un pipeline et sur son fonctionnement, consultez <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark machine learning pipeline</a>.
   
    Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **Maj+Entrée**.
   
        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
2. Ajustez le pipeline au document d’apprentissage. Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **Maj+Entrée**.
   
        model = pipeline.fit(training)
3. Vérifiez le document d’apprentissage pour contrôler votre progression dans l’application. Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **Maj+Entrée**.
   
        training.show()
   
    Le résultat doit ressembler à ce qui suit :
   
        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+

    Revenez en arrière et vérifiez le résultat par rapport au fichier CSV brut. Par exemple, la première ligne du fichier CSV contient les données suivantes :

    ![Capture instantanée des données de sortie pour l’exemple de Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Capture instantanée des données de sortie pour l’exemple de Machine Learning Spark")

    Notez que la température réelle est inférieure à la température cible, suggérant que le bâtiment est froid. De ce fait, dans le résultat, l’**étiquette** de la première ligne a pour valeur **0,0**,, ce qui signifie que le bâtiment n’est pas chaud.

1. Préparez un jeu de données sur lequel exécuter le modèle d’apprentissage. Pour ce faire, nous allons passer un ID système et une ancienneté du système (indiqués par **SystemInfo** dans le résultat de l’apprentissage). Le modèle prédira si le bâtiment ayant cet ID système et cette ancienneté de système sera plus chaud (indiqué par la valeur 1,0) ou plus froid (indiqué par la valeur 0,0).
   
   Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **Maj+Entrée**.
   
       # SystemInfo here is a combination of system ID followed by system age
       Document = Row("id", "SystemInfo")
       test = sc.parallelize([(1L, "20 25"),
                     (2L, "4 15"),
                     (3L, "16 9"),
                     (4L, "9 22"),
                     (5L, "17 10"),
                     (6L, "7 22")]) \
           .map(lambda x: Document(*x)).toDF() 
2. Pour finir, effectuez des prédictions sur les données de test. Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **Maj+Entrée**.
   
        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row
3. Le résultat doit ressembler à ce qui suit :
   
       Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
       Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
       Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
       Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
       Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
       Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
   
   Dans la première ligne de la prédiction, vous pouvez voir que pour un système HVAC avec l’ID 20 et une ancienneté du système de 25 ans, le bâtiment sera chaud (**prediction=1,0**). La première valeur de DenseVector (0,49999) correspond à la prédiction 0.0, et la seconde valeur (0,5001) correspond à la prédiction 1.0. Dans le résultat, même si la seconde valeur n’est que légèrement supérieure, le modèle indique **prediction=1,0**.
4. Une fois l’exécution de l’application terminée, arrêtez le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette opération permet d’arrêter et de fermer le bloc-notes.

## <a name="anaconda"></a>Utiliser une bibliothèque scikit-learn Anaconda pour le Machine Learning Spark
Les clusters Apache Spark sur HDInsight incluent des bibliothèques Anaconda, notamment la bibliothèque **scikit-learn** pour l’apprentissage automatique. Cette bibliothèque contient également différents jeux de données qui vous permettent de créer des exemples d’application directement à partir d’un bloc-notes Jupyter. Pour obtenir des exemples d’utilisation de la bibliothèque scikit-learn, reportez-vous à [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely) (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
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

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
