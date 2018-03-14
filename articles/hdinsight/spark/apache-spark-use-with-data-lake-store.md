---
title: "Utiliser Apache Spark pour analyser les données Azure Data Lake Store | Microsoft Docs"
description: "Exécuter des travaux Spark pour analyser des données stockées dans Azure Data Lake Store"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f174323-c17b-428c-903d-04f0e272784c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 6e44ffc26bbb618e0ff14d412ed77e4827232b42
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>Utiliser le cluster HDInsight Spark pour analyser les données dans Data Lake Store

Dans ce didacticiel, vous utilisez le bloc-notes Jupyter disponible avec les clusters HDInsight Spark pour exécuter un travail qui lit les données à partir d’un compte Data Lake Store.

## <a name="prerequisites"></a>Prérequis

* Compte Azure Data Lake Store. Suivez les instructions de [Prise en main d’Azure Data Lake Store avec le portail Azure](../../data-lake-store/data-lake-store-get-started-portal.md).

* Cluster Azure HDInsight Spark avec Data Lake Store comme système de stockage. Suivez les instructions énoncées dans la section [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    
## <a name="prepare-the-data"></a>Préparation des données

> [!NOTE]
> Vous n’avez pas besoin de suivre cette étape si vous avez créé le cluster HDInsight avec Data Lake Store comme stockage par défaut. Le processus de création de cluster ajoute quelques exemples de données dans le compte Data Lake Store spécifié durant la création du cluster. Passez à la section [Utilisation du cluster Azure HDInsight Spark avec Data Lake Store](#use-an-hdinsight-spark-cluster-with-data-lake-store).
>
>

Si vous avez créé un cluster HDInsight avec Data Lake Store en tant que stockage supplémentaire et Azure Storage Blob comme stockage par défaut, vous devez dans un premier temps copier quelques exemples de données sur le compte Data Lake Store. Vous pouvez utiliser les exemples de données de l’instance Azure Storage Blob associée au cluster HDInsight. Vous pouvez utiliser [l’outil ADLCopy](http://aka.ms/downloadadlcopy) pour cette opération. Téléchargez et installez l’outil à l’aide du lien.

1. Ouvrez une invite de commandes et accédez au répertoire où vous avez installé AdlCopy, généralement `%HOMEPATH%\Documents\adlcopy`.

2. Exécutez la commande suivante pour copier un objet blob spécifique du conteneur source vers Data Lake Store :

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Copiez le fichier d’exemple de données **HVAC.csv** de l’emplacement **/HdiSamples/HdiSamples/SensorSampleData/hvac/** sur le compte Azure Data Lake Store. L’extrait de code doit ressembler à ceci :

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > Assurez-vous que la casse des noms de fichiers et du chemin est correcte.
   >
   >
3. Vous êtes invité à entrer les informations d’identification de l’abonnement Azure sous lequel vous disposez d’un compte Data Lake Store. Le résultat ressemble à ce qui suit :

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Le fichier de données (**HVAC.csv**) sera copié dans un dossier **/hvac** du compte Data Lake Store.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>Utiliser un cluster HDInsight Spark avec Data Lake Store

1. Dans le tableau d’accueil du [portail Azure](https://portal.azure.com/), cliquez sur la vignette de votre cluster Spark (si vous l’avez épinglé au tableau d’accueil). Vous pouvez également accéder à votre cluster sous **Parcourir tout** > **Clusters HDInsight**.

2. Dans le panneau du cluster Spark, cliquez sur **Liens rapides**, puis dans le panneau **Tableau de bord du cluster**, cliquez sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

   > [!NOTE]
   > Vous pouvez également atteindre le bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Créer un nouveau bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.

    ![Créer un bloc-notes Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Créer un bloc-notes Jupyter")

4. Comme vous avez créé un bloc-notes à l’aide du noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. Vous pouvez commencer par importer les types requis pour ce scénario. Pour cela, collez l’extrait de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.

        from pyspark.sql.types import *

    À chaque exécution d’une tâche dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.

     ![État d’une tâche de bloc-notes Jupyter](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "État d’une tâche de bloc-notes Jupyter")

5. Chargez des exemples de données dans une table temporaire à l’aide du fichier **HVAC.csv** que vous avez copié dans le compte Data Lake Store. Vous pouvez accéder aux données du compte Data Lake Store à l’aide du modèle d’URL suivant.

    * Si vous disposez de Data Lake Store en tant que stockage par défaut, le fichier HVAC.csv sera sur un chemin d’accès similaire à l’URL suivante :

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        Vous pouvez également utiliser un format abrégé comme les éléments suivants :

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Si vous disposez de Data Lake Store en tant que stockage supplémentaire, le fichier HVAC.csv sera à l’emplacement où vous l’avez copié, comme :

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Dans une cellule vide, collez l’exemple de code suivant, remplacez **MYDATALAKESTORE** par votre nom de compte Data Lake Store, puis appuyez sur **Maj+ Entrée**. Cet exemple de code enregistre les données dans une table temporaire appelée **hvac**.

            # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
            hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            # Create the schema
            hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

            # Parse the data in hvacText
            hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

            # Create a data frame
            hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

            # Register the data fram as a table to run queries against
            hvacdf.registerTempTable("hvac")

6. Étant donné que vous utilisez un noyau PySpark, vous pouvez maintenant exécuter directement une requête SQL sur la table temporaire **hvac** que vous venez de créer à l’aide de la méthode magique `%%sql`. Pour plus d’informations sur la méthode magique `%%sql` , ainsi que les autres méthodes magiques disponibles avec le noyau PySpark, consultez [Noyaux disponibles sur les blocs-notes Jupyter avec clusters Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Une fois le travail terminé, le résultat tabulaire suivant s’affiche par défaut.

      ![Table de sortie des résultats de la requête](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Table de sortie des résultats de la requête")

     Vous pouvez également voir les résultats dans d’autres visualisations. Par exemple, un graphique en aires pour le même résultat se présenterait comme suit.

     ![Graphique en aires des résultats de la requête](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "Graphique en aires des résultats de la requête")

8. Une fois l’exécution de l’application terminée, arrêtez le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cette opération permet d’arrêter et de fermer le bloc-notes.


## <a name="next-steps"></a>étapes suivantes

* [Créer une application Scala autonome à exécuter sur le cluster Apache Spark](apache-spark-create-standalone-application.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ afin de créer des applications Spark pour un cluster Linux HDInsight Spark](apache-spark-intellij-tool-plugin.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse afin de créer des applications Spark pour un cluster Linux HDInsight Spark](apache-spark-eclipse-tool-plugin.md)
