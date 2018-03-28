---
title: Exemple de Machine Learning avec Spark MLlib sur HDInsight - Azure | Microsoft Docs
description: Découvrez comment utiliser Spark MLlib pour créer une application de Machine Learning qui analyse un jeu de données à l’aide d’une classification de régression logistique.
keywords: machine learning spark, exemple de machine learning spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c0fd4baa-946d-4e03-ad2c-a03491bd90c8
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2018
ms.author: jgao
ms.openlocfilehash: ec9852cb47ab57736edadecf38173c314195f324
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Utiliser Spark MLlib pour créer une application de Machine Learning et analyser un jeu de données

Découvrez comment utiliser Spark [MLlib](https://spark.apache.org/mllib/) pour créer une application de Machine Learning afin d’effectuer une analyse prédictive simple sur un jeu de données ouvert. À partir des bibliothèques de Machine Learning intégrées de Spark, cet exemple utilise une *classification* de régression logistique. 

> [!TIP]
> Ce exemple est également disponible en tant que bloc-notes Jupyter sur un cluster Spark (Linux) que vous créez dans HDInsight. L’interface du bloc-notes vous permet d’exécuter des extraits de code Python à partir du bloc-notes lui-même. Pour suivre le didacticiel à partir d’un bloc-notes, créez un cluster Spark et lancez un bloc-notes Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`). Ensuite, exécutez le bloc-notes, **Machine Learning Spark : analyse prédictive des données d’inspections alimentaires à l’aide de MLLib** figurant dans le dossier **Python**.
>
>

MLLib est une bibliothèque principale Spark qui fournit de nombreux utilitaires pratiques pour l’exécution de tâches de Machine Learning. Certains de ces utilitaires conviennent pour les tâches suivantes :

* classification ;
* Régression
* Clustering
* Modélisation de rubrique
* Décomposition de valeur singulière (SVD) et analyse des composants principaux (PCA)
* Hypothèse de test et de calcul des exemples de statistiques

## <a name="understand-classification-and-logistic-regression"></a>Comprendre la classification et la régression logistique
Une *classification*, tâche de Machine Learning très courante, est le processus de tri de données d’entrée par catégories. L’algorithme de classification doit déterminer comment attribuer les « étiquettes » aux données d’entrée que vous fournissez. Par exemple, vous pouvez utiliser un algorithme Machine Learning qui accepte les informations de stock en tant qu’entrée et divise le stock en deux catégories : ce qu’il faut vendre et ce qu’il faut conserver.

La régression logistique correspond à l’algorithme que vous utilisez pour la classification. L’API de régression logistique de Spark est utile pour la *classification binaire*ou pour classer les données d’entrée dans un des deux groupes. Pour plus d’informations sur la régression logistique, consultez [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

En résumé, le processus de régression logistique génère une *fonction logistique* qui peut être utilisée pour prédire la probabilité qu’un vecteur d’entrée appartienne à un groupe ou à l’autre.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Exemple d’analyse prédictive sur des données d’inspection alimentaire
Dans cet exemple, vous utilisez Spark pour effectuer une analyse prédictive de données d’inspection alimentaire (**Food_Inspections1.csv**) qui ont été acquises via le [portail de données de la ville de Chicago](https://data.cityofchicago.org/). Ce jeu de données contient des informations relatives aux inspections d’établissements alimentaires effectuées à Chicago, notamment des informations sur chaque établissement, les violations éventuelles relevées et les résultats des inspections. Le fichier de données CSV est déjà disponible dans le compte de stockage associé au cluster dans **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Dans la procédure ci-dessous, vous développez un modèle pour voir ce qui est nécessaire à la réussite ou à l’échec d’une inspection de produits alimentaires.

## <a name="create-a-spark-mllib-machine-learning-app"></a>Créer une application de Machine Learning MLlib Spark

1. Créez un bloc-notes Jupyter à l’aide du noyau PySpark. Pour obtenir des instructions, consultez [Créer un bloc-notes Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importez les types requis pour cette application. Copiez et collez le code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```
    Grâce au noyau PySpark, il est inutile de créer des contextes explicitement. Les contextes Spark et Hive sont automatiquement créés pour vous lorsque vous exécutez la première cellule de code. 

## <a name="construct-the-input-dataframe"></a>Construire une trame de données d’entrée

Étant donné que les données brutes sont au format CSV, vous pouvez utiliser le contexte Spark pour extraire chaque fichier en mémoire sous forme de texte non structuré. Ensuite, utilisez la bibliothèque CSV de Python pour analyser chaque ligne de données.

1. Exécutez les lignes suivantes pour créer un jeu de données distribué résilient (RDD) par l’importation et l’analyse des données d’entrée.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value
    
    inspections = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Exécutez le code suivant pour récupérer une ligne du RDD, afin de pouvoir observer le schéma de données :

    ```PySpark
    inspections.take(1)
    ```

    La sortie est la suivante :

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    La sortie donne une idée du schéma du fichier d’entrée. Elle inclut notamment, pour chaque établissement, le nom, le type, l’adresse, les données des inspections et l’emplacement. 

3. Exécutez le code suivant pour créer une trame de données (*df*) et une table temporaire (*CountResults*) avec quelques colonnes utiles pour l’analyse prédictive. `sqlContext` permet d’exécuter des transformations sur des données structurées. 

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])
    
    df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Les quatre colonnes intéressantes de la trame de données sont : **id**, **nom**, **résultats** et **violations**.

4. Exécutez le code suivant pour obtenir un petit échantillon de données :

    ```PySpark
    df.show(5)
    ```

    La sortie est la suivante :

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Comprendre les données

Commençons par nous faire une idée de ce que contient le jeu de données. 

1. Exécutez le code suivant pour afficher les valeurs distinctes dans la colonne **résultats** :

    ```PySpark
    df.select('results').distinct().show()
    ```

    La sortie est la suivante :

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Exécutez le code suivant pour visualiser la distribution de ces résultats :

    ```PySpark
    %%sql -o countResultsdf
    SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results
    ```

    La méthode `%%sql` suivie par `-o countResultsdf` garantit que le résultat de la requête est conservé localement sur le serveur Jupyter (généralement le nœud principal du cluster). Le résultat est conservé sous la forme d’une trame de données [Pandas](http://pandas.pydata.org/) avec le nom spécifié **countResultsdf**. Pour plus d’informations sur la méthode magique `%%sql` et d’autres méthodes magiques disponibles avec le noyau PySpark, consultez [Noyaux disponibles sur les blocs-notes Jupyter avec clusters Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    La sortie est la suivante :

    ![Résultat de la requête SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Résultat de la requête SQL")


3. Vous pouvez également utiliser [Matplotlib](https://en.wikipedia.org/wiki/Matplotlib), une bibliothèque permettant de construire une visualisation des données, pour créer un tracé. Étant donné que le tracé doit être créé à partir du tableau de données **countResultsdf** conservé localement, l’extrait de code doit commencer par la commande magique `%%local`. Cela garantit l’exécution locale du code sur le serveur Jupyter.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    La sortie est la suivante :

    ![Sortie de l’application de Machine Learning Spark : graphique en secteurs avec cinq résultats d’inspection distincts](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-1.png "Sortie du résultat de l’application de Machine Learning Spark")

    Une inspection peut présenter 5 résultats distincts :

    - Entreprise introuvable
    - Échec
    - Réussite
    - Réussite avec conditions
    - Cessation d’activités

    Pour prédire un résultat d’inspection de produits alimentaires, vous devez développer un modèle basé sur les violations. Étant donné que la régression logistique est une méthode de classification binaire, il est judicieux de regrouper les données de résultat en deux catégories : **Échec** et **Réussite** :

    - Réussite
        - Réussite
        - Réussite avec conditions
    - Échec
        - Échec
    - Abandonner
        - Entreprise introuvable
        - Cessation d’activités

    Les données avec les autres résultats (« Entreprise introuvable » ou « Faillite ») sont inutiles et représentent de toute façon un très faible pourcentage.

4. Exécutez le code suivant pour convertir la trame de données existante (`df`) en une nouvelle trame de données dans laquelle chaque inspection est représentée par une paire étiquette-violations. Dans ce cas, une étiquette de `0.0` représente un échec, une étiquette de `1.0` représente un succès et une étiquette de `-1.0` représente d’autres résultats. 

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Exécutez le code suivant pour afficher une ligne de données étiquetées :

    ```PySpark
    labeledData.take(1)
    ```

    La sortie est la suivante :

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Créer un modèle de régression logistique à partir de la trame de données d’entrée

La dernière tâche consiste à convertir les données étiquetées dans un format qui peut être analysé par régression logistique. L’entrée dans un algorithme de régression logistique doit être un jeu de *paires de vecteurs étiquette-fonctionnalité*, où le « vecteur fonctionnalité » est un vecteur de nombres représentant le point d’entrée. Vous devez donc convertir la colonne « violations », qui est semi-structurée et contient un grand nombre de commentaires sous forme de texte libre, en un tableau de nombres réels qu’une machine peut facilement comprendre.

Une approche Machine Learning standard pour le traitement du langage naturel consiste à assigner un « index » à chaque mot distinct et à transmettre un vecteur à l’algorithme de Machine Learning, de manière à ce que la valeur de chaque index contienne la fréquence relative de ce mot dans la chaîne de texte.

MLLib permet d’effectuer cette opération en toute simplicité. Tout d’abord, il convertit en jetons chaque chaîne de violations afin d’obtenir les mots de celle-ci. Ensuite, il utilise un `HashingTF` pour convertir chaque ensemble de jetons en un vecteur fonctionnalité qui peut ensuite être transmis à l’algorithme de régression logistique pour construire un modèle. Vous exécutez toutes ces étapes successivement à l’aide d’un « pipeline ».

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Évaluer le modèle à l’aide d’un autre jeu de données

Vous pouvez utiliser le modèle que vous avez créé précédemment pour *prédire* les résultats des nouvelles inspections, en fonction des violations qui ont été observées. Vous avez formé ce modèle sur le jeu de données **Food_Inspections1.csv**. Vous pouvez utiliser un deuxième jeu de données, **Food_Inspections2.csv**, pour *évaluer* la puissance de ce modèle sur les nouvelles données. Ce deuxième jeu de données (**Food_Inspections2.csv**) se trouve dans le conteneur de stockage par défaut associé au cluster.

1. Exécutez le code suivant pour créer une nouvelle trame de données, **predictionsDf**, qui contient la prédiction générée par le modèle. Il crée également une table temporaire, **Predictions**, basée sur la tramedonnées.

    ```PySpark
    testData = sc.textFile('wasb:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Un résultat similaire à ce qui suit s’affiche normalement :

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Observez une des prédictions. Exécutez cet extrait de code :

    ```PySpark
    predictionsDf.take(1)
    ```

   Une prédiction pour la première entrée figure dans le jeu de données de test.
1. La méthode `model.transform()` applique la même transformation à toutes les nouvelles données possédant le même schéma afin d’obtenir une prédiction permettant de classer les données. Vous pouvez générer des statistiques simples pour avoir une idée de la précision des prédictions :

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    La sortie a l'aspect suivant :

    ```
    # -----------------
    # THIS IS AN OUTPUT
    # -----------------

    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    L’utilisation de la régression logistique avec Spark vous fournit un modèle précis de la relation entre les descriptions des violations en anglais et indique si une entreprise donnée échoue ou réussit l’inspection alimentaire.

## <a name="create-a-visual-representation-of-the-prediction"></a>Créer une représentation visuelle de la prédiction
Vous pouvez désormais construire une visualisation finale pour faciliter l’examen des résultats de ce test.

1. Vous commencez par extraire les différents prédictions et résultats de la table temporaire **Predictions** créée précédemment. Les requêtes suivantes séparent la sortie en tant que *true_positive*, *false_positive*, *true_negative* et *false_negative*. Dans les requêtes ci-après, vous désactivez la visualisation en utilisant `-q` et vous enregistrez la sortie (avec `-o`) sous la forme de trames de données qui sont ensuite utilisables avec la magie `%%local`.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Enfin, utilisez l’extrait suivant pour générer le tracé à l’aide de **Matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Vous devez normalement voir la sortie suivante.

    ![Sortie de l’application de Machine Learning Spark : graphique en secteurs des pourcentages d’inspections alimentaires qui ont échoué.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Sortie du résultat de l’application de Machine Learning Spark")

    Dans ce graphique, un résultat « positif » fait référence à l’inspection de produits alimentaires ayant échoué, tandis qu’un résultat négatif fait référence à une inspection réussie.

## <a name="shut-down-the-notebook"></a>Arrêtez le bloc-notes
Une fois l’exécution de l’application terminée, fermez le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Cela a pour effet d’arrêter et de fermer le bloc-notes.

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour l’analyse de la température des bâtiments à l’aide des données des systèmes HVAC](apache-spark-ipython-notebook-machine-learning.md)
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
