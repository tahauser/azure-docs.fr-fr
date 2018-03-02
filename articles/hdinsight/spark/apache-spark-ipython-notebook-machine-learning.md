---
title: Utiliser des applications de Machine Learning Apache Spark sur HDInsight | Documents Microsoft
description: "Instructions détaillées sur la manière de créer des applications de Machine Learning Apache Spark sur des clusters Spark HDInsight à l’aide d’un bloc-notes Jupyter"
services: hdinsight
documentationcenter: 
author: mumian
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
ms.date: 01/23/2018
ms.author: jgao
ms.openlocfilehash: 2f7dcb9bea05a79a6647b549896c8107f9e830af
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="build-apache-spark-machine-learning-applications-on-azure-hdinsight"></a>Créer des applications de Machine Learning Apache Spark sur Azure HDInsight

Découvrez comment créer une application de Machine Learning Apache Spark à l’aide d’un cluster Spark sur HDInsight. Cet article montre comment utiliser le bloc-notes Jupyter disponible avec le cluster pour créer et tester l’application. L’application utilise l’exemple de données HVAC.csv, qui est disponible par défaut sur tous les clusters.

[MLib](https://spark.apache.org/docs/1.1.0/mllib-guide.html) est une bibliothèque de Machine Learning de Spark constituée d’utilitaires et d’algorithmes d’apprentissage courants, notamment la classification, la régression, le clustering, le filtrage collaboratif, la réduction de dimensionnalité, ainsi que les primitives d’optimisation sous-jacentes.

## <a name="prerequisites"></a>Configuration requise :

Vous devez disposer de l’élément suivant :

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md). 

## <a name="data"></a>Comprendre le jeu de données

Les données suivantes indiquent la température cible et la température réelle de bâtiments dans lesquels un système HVAC est installé. La colonne **System** représente l’ID système et la colonne **SystemAge** représente le nombre d’années écoulées depuis la mise en place du système HVAC dans le bâtiment. Vous utilisez les données de ce didacticiel pour prédire si un bâtiment sera plus chaud ou plus froid en fonction de la température cible, selon un ID système et l’ancienneté du système.

![Capture instantanée des données utilisées pour l’exemple de Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Capture instantanée des données utilisées pour l’exemple de Machine Learning Spark")

Le fichier de données, **HVAC.csv**, se trouve sous **\HdiSamples\HdiSamples\SensorSampleData\hvac** pour tous les clusters HDInsight.

## <a name="app"></a>Écrire une application de Machine Learning Spark à l’aide de Spark MLlib
Dans cette application, vous utilisez un [pipeline ML](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) Spark pour effectuer une classification de documents. Les pipelines ML fournissent un ensemble d’API de haut niveau basées sur des trames de données qui permettent aux utilisateurs de créer et d’ajuster des pipelines d’apprentissage automatique pratique. Dans le pipeline, vous fractionnez le document en mots, convertissez ces mots en un vecteur de fonctionnalité numérique avant de créer un modèle de prédiction utilisant les étiquettes et vecteurs de fonctionnalité. Procédez comme suit pour créer l’application.

1. Créez un bloc-notes Jupyter à l’aide du noyau PySpark. Pour obtenir des instructions, consultez [Créer un bloc-notes Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).
2. Importez les types requis pour ce scénario. Collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**. 

    ```PySpark
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
    ```
3. Chargez les données (hvac.csv), analysez-les et utilisez-les pour effectuer l’apprentissage du modèle. 

    ```PySpark
    # Define a type called LabelDocument
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
    ```

    Dans l’extrait de code, vous définissez une fonction qui compare la température réelle avec la température cible. Si la température actuelle est supérieure, le bâtiment est chaud, ce qui est indiqué par la valeur **1,0**. Sinon, le bâtiment est froid, ce qui est indiqué par la valeur **0.0**. 

4. Configurez le pipeline Spark ML qui est composé de trois étapes : Tokenizer, HashingTF et LogisticRegression. 

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Pour plus d’informations sur ce qu’est un pipeline et sur son fonctionnement, consultez <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">Spark machine learning pipeline</a>.

5. Ajustez le pipeline au document d’apprentissage.
   
    ```PySpark
    model = pipeline.fit(training)
    ```

6. Vérifiez le document d’apprentissage pour contrôler votre progression dans l’application.
   
    ```PySpark
    training.show()
    ```
   
    Le résultat doit ressembler à ce qui suit :

    ```
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
    ```

    Comparaison de la sortie et du fichier CSV brut. Par exemple, la première ligne du fichier CSV contient les données suivantes :

    ![Capture instantanée des données de sortie pour l’exemple de Machine Learning Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Capture instantanée des données de sortie pour l’exemple de Machine Learning Spark")

    Notez que la température réelle est inférieure à la température cible, suggérant que le bâtiment est froid. De ce fait, dans le résultat, l’**étiquette** de la première ligne a pour valeur **0,0**,, ce qui signifie que le bâtiment n’est pas chaud.

7. Préparez un jeu de données sur lequel exécuter le modèle d’apprentissage. Pour ce faire, vous transmettez un ID système et l’ancienneté du système (indiqués par **SystemInfo** dans le résultat de l’apprentissage). Le modèle prédira si le bâtiment ayant cet ID système et cette ancienneté de système sera plus chaud (indiqué par la valeur 1,0) ou plus froid (indiqué par la valeur 0,0).
   
    ```PySpark   
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF() 
    ```
8. Pour finir, effectuez des prédictions sur les données de test. 
   
    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Le résultat doit ressembler à ce qui suit :

    ```   
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```
   
   Dans la première ligne de la prédiction, vous pouvez voir que pour un système HVAC avec l’ID 20 et une ancienneté de 25 ans, le bâtiment est chaud (**prediction=1.0**). La première valeur de DenseVector (0,49999) correspond à la prédiction 0.0, et la seconde valeur (0,5001) correspond à la prédiction 1.0. Dans le résultat, même si la seconde valeur n’est que légèrement supérieure, le modèle indique **prediction=1,0**.
10. Arrêtez le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette opération permet d’arrêter et de fermer le bloc-notes.

## <a name="anaconda"></a>Utiliser une bibliothèque scikit-learn Anaconda pour le Machine Learning Spark
Les clusters Apache Spark sur HDInsight incluent des bibliothèques Anaconda, notamment la bibliothèque **scikit-learn** pour l’apprentissage automatique. Cette bibliothèque contient également différents jeux de données qui vous permettent de créer des exemples d’application directement à partir d’un bloc-notes Jupyter. Pour obtenir des exemples d’utilisation de la bibliothèque scikit-learn, reportez-vous à [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
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

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]:../hadoop/apache-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]:../hadoop/apache-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
