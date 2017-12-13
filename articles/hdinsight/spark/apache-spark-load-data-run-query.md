---
title: "Exécuter des requêtes interactives sur un cluster Azure HDInsight Spark | Microsoft Docs"
description: "Démarrage rapide Spark HDInsight pour créer un cluster Apache Spark dans HDInsight."
keywords: "démarrage rapide spark,spark interactif,requête interactive,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 78ab44a7afa6523e1e9e4082b3f45b1a28affe77
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>Exécuter des requêtes interactives sur des clusters Spark dans HDInsight

Découvrez comment utiliser le bloc-notes Jupyter pour exécuter des requêtes interactives Spark SQL sur un cluster Spark. 

Le [bloc-notes Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) est une application basée sur navigateur qui étend l’expérience interactive de la console au web. Spark sur HDInsight inclut également [Bloc-notes Zeppelin](apache-spark-zeppelin-notebook.md). Bloc-notes Jupyter est utilisé dans ce didacticiel.

Les blocs-notes Jupyter des clusters HDInsight prennent également en charge trois noyaux : **PySpark**, **PySpark3**, et **Spark**. Le noyau **PySpark** est utilisé dans ce didacticiel. Pour plus d’informations sur les noyaux et les avantages liés à l’utilisation de**PySpark**, consultez [Utiliser les noyaux de blocs-notes Jupyter avec les clusters Apache Spark dans HDInsight](apache-spark-jupyter-notebook-kernels.md). Pour utiliser Blocs-notes Zeppelin, consultez [Utiliser Blocs-notes Zeppelin avec un cluster Apache Spark sur HDInsight](./apache-spark-zeppelin-notebook.md).

Dans ce didacticiel, vous interrogez des données dans un fichier CSV. Vous devez d’abord charger ces données dans Spark comme une trame de données. Vous pouvez ensuite exécuter des requêtes sur la trame de données à l’aide du bloc-notes Jupyter. 

## <a name="prerequisites"></a>Composants requis

* **Un cluster Azure HDInsight Spark**. Pour obtenir des instructions, consultez [Créer un cluster Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Un bloc-notes Jupyter à l’aide de PySpark**. Pour obtenir des instructions, consultez [Créer un bloc-notes Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

## <a name="create-a-dataframe-from-a-csv-file"></a>Créer une trame de données à partir d’un fichier CSV

Avec un SQLContext, les applications peuvent créer des trames de données à partir d’un RDD existant, d’une table Hive ou de sources de données. 

**Pour créer une trame de données à partir d’un fichier CSV**

1. Si vous n’en avez pas, créez un bloc-notes Jupyter à l’aide de PySpark. Pour obtenir des instructions, consultez [Créer un bloc-notes Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Collez l’exemple de code suivant dans une cellule vide du bloc-notes, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. Le code importe les types requis pour ce scénario :

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    Lorsque vous utilisez le noyau PySpark pour créer un bloc-notes, les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. Vous n’avez pas besoin de créer explicitement de contextes.

    Lorsque vous exécutez une requête interactive dans Jupyter, la fenêtre du navigateur web ou la légende d’onglet affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

    ![État de la requête interactive Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "État de la requête interactive Spark SQL")

3. Exécutez le code suivant pour créer une trame de données et une table temporaire (**hvac**) en exécutant le code suivant : le code n’extrait pas toutes les colonnes disponibles dans le fichier CSV. 

    ```PySpark
    # Create an RDD from sample data
    hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
    # Create a schema for our data
    Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')
    
    # Parse the data and create a schema
    hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
    hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
    
    # Infer the schema and create a table       
    hvacTable = sqlContext.createDataFrame(hvac)
    hvacTable.registerTempTable('hvactemptable')
    dfw = DataFrameWriter(hvacTable)
    dfw.saveAsTable('hvac')
    ```
    La capture d’écran suivante montre un instantané du fichier HVAC.csv. Le fichier CSV est fourni avec tous les clusters HDInsight Spark. Les données capturent les variations de température du bâtiment.

    ![Instantané des données pour les requêtes Spark SQL interactives](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "instantané des données pour les requêtes Spark SQL interactives")

## <a name="run-queries-on-the-dataframe"></a>Exécuter des requêtes sur la trame de données

Une fois la table créée, vous pouvez exécuter une requête interactive sur les données.

**Pour exécuter une requête**

1. Exécutez le code suivant dans une cellule vide du bloc-notes :

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Étant donné que le noyau PySpark est utilisé dans le bloc-notes, vous pouvez maintenant exécuter directement une requête SQL interactive sur la table temporaire **hvac** que vous avez créée à l’aide de la méthode magique `%%sql`. Pour plus d’informations sur la méthode magique `%%sql` et d’autres méthodes magiques disponibles avec le noyau PySpark, consultez [Noyaux disponibles sur les blocs-notes Jupyter avec clusters Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   La sortie sous forme de tableau suivante s’affiche par défaut.

     ![Sortie sous forme de tableau d’un résultat de requête interactive Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Sortie sous forme de tableau d’un résultat de requête interactive Spark")

3. Vous pouvez également voir les résultats dans d’autres visualisations. Pour afficher un graphique en aires pour le même résultat, sélectionnez **Area** (Aires), puis définissez d’autres valeurs comme indiqué.

    ![Résultat de requête interactive Spark sous forme graphique](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Résultat de requête interactive Spark sous forme graphique")

10. Dans le menu **File** (Fichier) du bloc-notes, cliquez sur **Save and Checkpoint** (Enregistrer et point de contrôle). 

11. Si vous démarrez le [didacticiel suivant](apache-spark-use-bi-tools.md) maintenant, laissez le bloc-notes ouvert. Dans le cas contraire, arrêtez le bloc-notes pour libérer les ressources de cluster : à partir du menu **File** (Fichier) du bloc-notes, cliquez sur **Close and Halt** (Fermer et arrêter).

## <a name="next-step"></a>Étape suivante

Dans cet article, vous avez appris comment exécuter des requêtes interactives dans Spark à l’aide du bloc-notes Jupyter. Passer à l’article suivant pour découvrir comment les données que vous avez enregistrées dans Spark peuvent être placées dans un outil analytique de BI tel que Power BI et Tableau. 

> [!div class="nextstepaction"]
>[Spark BI utilisant des outils de visualisation de données avec Azure HDInsight](apache-spark-use-bi-tools.md)




