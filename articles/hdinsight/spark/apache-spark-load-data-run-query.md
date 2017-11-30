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
ms.date: 09/22/2017
ms.author: jgao
ms.openlocfilehash: db8f0056fa3813e95c2c5bea583d7b66ac64260f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>Exécuter des requêtes interactives sur un cluster HDInsight Spark

Dans cet article, vous utilisez un bloc-notes Jupyter pour exécuter des requêtes interactives Spark SQL sur un cluster Spark. Le bloc-notes Jupyter est une application basée sur navigateur qui étend l’expérience interactive de la console au Web. Pour plus d’informations, consultez [le bloc-notes Jupyter](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html).

Pour ce didacticiel, vous utilisez le noyau **PySpark** dans le bloc-notes Jupyter pour exécuter une requête de Spark SQL interactive. Les blocs-notes Jupython des clusters HDInsight prennent également en charge deux autres noyaux - **PySpark3** et **Spark**. Pour plus d’informations sur les noyaux et les avantages liés à l’utilisation de**PySpark**, consultez [Utiliser les noyaux de blocs-notes Jupyter avec les clusters Apache Spark dans HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Composants requis

* **Un cluster Azure HDInsight Spark**. Pour obtenir des instructions, consultez [Créer un cluster Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>Créer un bloc-notes Jupyter pour exécuter des requêtes interactives

Pour exécuter des requêtes, nous utilisons des exemples de données qui sont par défaut disponibles dans le stockage associé au cluster. Toutefois, vous devez d’abord charger ces données dans Spark comme une trame de données. Une fois que vous avez la trame de données, vous pouvez y exécuter des requêtes à l’aide du bloc-notes Jupyter. Dans cet article, vous découvrez comment :

* Enregistrer un jeu d’exemple données comme une trame de données Spark.
* Exécuter des requêtes sur la trame de données.

Allons-y.

1. Ouvrez le [portail Azure](https://portal.azure.com/). Si vous avez choisi d’épingler le cluster au tableau de bord, cliquez sur la mosaïque du cluster dans le tableau de bord pour lancer le panneau du cluster.

    Si vous n’avez pas épinglé le cluster au tableau de bord, dans le volet gauche, cliquez sur **Clusters HDInsight**, puis cliquez sur le cluster que vous avez créé.

3. À partir de **Liens rapides**, cliquez sur **Tableaux de bord de Cluster**, puis sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

   ![Ouvrir le bloc-notes Jupyter pour exécuter une requête interactive Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "Ouvrir un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL")

   > [!NOTE]
   > Vous pouvez également accéder au bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Créez un bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.

   ![Créer un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Créer un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL")

   Un nouveau bloc-notes est créé et ouvert sous le nom Untitled(Untitled.pynb).

4. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial si vous le souhaitez.

    ![Fournir un nom pour le bloc-notes Jupyter à partir duquel exécuter une requête interactive Spark](./media/apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "Fournir un nom pour le bloc-notes Jupyter à partir duquel exécuter une requête interactive Spark")

5. Collez l’exemple de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. Le code importe les types requis pour ce scénario :

        from pyspark.sql import *
        from pyspark.sql.types import *

    Comme vous avez créé un bloc-notes à l’aide du noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code.

    ![État de la requête interactive Spark SQL](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "État de la requête interactive Spark SQL")

    À chaque exécution d’une requête interactive dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

6. Avant de charger les données dans un cluster Spark, jetons un coup œil à son instantané. Les exemples de données utilisés dans ce didacticiel sont disponibles dans un fichier CSV sur tous les clusters HDInsight Spark à **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Les données capturent les variations de température du bâtiment. Voici les premières lignes de ces données.

    ![Instantané des données pour les requêtes Spark SQL interactives](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "instantané des données pour les requêtes Spark SQL interactives")

6. Créez une trame de données et une table temporaire (**hvac**) en exécutant le code suivant. Pour ce didacticiel, nous ne créons pas toutes les colonnes disponibles dans le fichier CSV. 

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

7. Une fois la table créée, utilisez le code suivant pour exécuter une requête interactive sur les données.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Étant donné que vous utilisez un noyau PySpark, vous pouvez maintenant exécuter directement une requête SQL interactive sur la table temporaire **hvac** que vous avez créée à l’aide de la méthode magique `%%sql`. Pour plus d’informations sur la méthode magique `%%sql` et d’autres méthodes magiques disponibles avec le noyau PySpark, consultez [Noyaux disponibles sur les blocs-notes Jupyter avec clusters Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

   La sortie sous forme de tableau suivante s’affiche par défaut.

     ![Sortie sous forme de tableau d’un résultat de requête interactive Spark](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Sortie sous forme de tableau d’un résultat de requête interactive Spark")

9. Vous pouvez également voir les résultats dans d’autres visualisations. Pour afficher un graphique en aires pour le même résultat, sélectionnez **Area** (Aires), puis définissez d’autres valeurs comme indiqué.

    ![Résultat de requête interactive Spark sous forme graphique](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Résultat de requête interactive Spark sous forme graphique")

10. Dans le menu **File** (Fichier) du bloc-notes, cliquez sur **Save and Checkpoint** (Enregistrer et point de contrôle). 

11. Si vous démarrez le [didacticiel suivant](apache-spark-use-bi-tools.md) maintenant, laissez le bloc-notes ouvert. Dans le cas contraire, arrêtez le bloc-notes pour libérer les ressources de cluster : à partir du menu **File** (Fichier) du bloc-notes, cliquez sur **Close and Halt** (Fermer et arrêter).

## <a name="next-step"></a>Étape suivante

Dans cet article, vous avez appris comment exécuter des requêtes interactives dans Spark à l’aide du bloc-notes Jupyter. Passer à l’article suivant pour découvrir comment les données que vous avez enregistrées dans Spark peuvent être placées dans un outil analytique de BI tel que Power BI et Tableau. 

> [!div class="nextstepaction"]
>[Spark BI utilisant des outils de visualisation de données avec Azure HDInsight](apache-spark-use-bi-tools.md)




