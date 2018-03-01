---
title: Utiliser le blocs-notes Zeppelin avec un cluster Apache Spark sur HDInsight | Microsoft Docs
description: "Instructions détaillées sur l’utilisation des blocs-notes Zeppelin avec les clusters Apache Spark sur Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: df489d70-7788-4efa-a089-e5e5006421e2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 9546a0d99647b0debfbac9d6a5f3e2dcf34d0fa0
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Utiliser des blocs-notes Zeppelin avec un cluster Apache Spark sur HDInsight

Les clusters HDInsight Spark incluent des blocs-notes Zeppelin que vous pouvez utiliser pour exécuter des tâches Spark. Dans cet article, vous allez apprendre à utiliser le bloc-notes Zeppelin sur un cluster HDInsight.

> [!NOTE]
> Les blocs-notes Zeppelin sont disponibles uniquement pour Spark 1.6.3 sur HDInsight 3.5 et Spark 2.1.0 sur HDInsight 3.6.
>

**Configuration requise :**

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Lancement d’un bloc-notes Zeppelin
1. Dans le panneau du cluster Spark, cliquez sur **Tableau de bord du cluster**, puis sur **Bloc-notes Zeppelin**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.
   
   > [!NOTE]
   > Vous pouvez également atteindre le bloc-notes Zeppelin pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`
   > 
   > 
2. Créer un nouveau bloc-notes. Dans le volet d'en-tête, cliquez sur **Bloc-notes**, puis sur **Créer une note**.
   
    ![Créer un nouveau bloc-notes Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Créer un nouveau bloc-notes Zeppelin")
   
    Entrez un nom pour le bloc-notes, puis cliquez sur **Créer une note**.
3. Vérifiez également que l’en-tête du bloc-notes indique un état connecté. Il est indiqué par un point vert dans le coin supérieur droit.
   
    ![État du bloc-notes Zeppelin](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "État du bloc-notes Zeppelin")
4. Chargez un exemple de données dans une table temporaire. Lorsque vous créez un cluster Spark dans HDInsight, l’exemple de fichier de données **hvac.csv** est copié vers le compte de stockage associé dans **\HdiSamples\SensorSampleData\hvac**.
   
    Collez l’extrait suivant dans le paragraphe vide créé par défaut dans le nouveau bloc-notes.
   
        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter
   
        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
   
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
   
    Appuyez sur **MAJ + ENTRÉE** ou cliquez sur le bouton **Lire** pour que le paragraphe exécute l'extrait de code. L’état indiqué dans le coin supérieur droit du paragraphe doit progresser de READY, PENDING, RUNNING à FINISHED. Le résultat s’affiche au bas du même paragraphe. La capture d’écran ressemble à ceci :
   
    ![Créer une table temporaire à partir de données brutes](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Créer une table temporaire à partir de données brutes")
   
    Vous pouvez également indiquer un titre pour chaque paragraphe. Dans le coin droit, cliquez sur l’icône **Settings**, puis sur **Show title**.
5. Vous pouvez maintenant exécuter des instructions Spark SQL dans la table **hvac** . Collez la requête suivante dans un nouveau paragraphe. La requête récupère l’ID de bâtiment et la différence entre les températures cibles et réelles pour chaque bâtiment à une date donnée. Appuyez sur **MAJ + ENTRÉE**.
   
        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
   
    L’instruction **%sql** du début demande au bloc-notes d’utiliser l’interpréteur Livy Scala.
   
    La capture d’écran qui suit présente le résultat.
   
    ![Exécuter une instruction Spark SQL à l’aide du bloc-notes](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Exécuter une instruction Spark SQL à l’aide du bloc-notes")
   
     Cliquez sur les options d’affichage (mis en exergue dans un rectangle) pour basculer entre les différentes représentations du même résultat. Cliquez sur **Paramètres** pour choisir ce qui constitue la clé et les valeurs dans le résultat. La capture d'écran ci-dessus utilise la clé **buildingID** et la moyenne **temp_diff** comme valeur.
6. Vous pouvez également exécuter des instructions Spark SQL à l’aide de variables dans la requête. L’extrait suivant montre comment définir la variable **Temp**dans la requête avec les valeurs possibles d’interrogation. Lors de la première exécution de la requête, une liste déroulante est automatiquement renseignée avec les valeurs que vous avez spécifiées pour la variable.
   
        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 
   
    Collez cet extrait dans un nouveau paragraphe, puis appuyez sur **MAJ + ENTRÉE**. La capture d’écran qui suit présente le résultat.
   
    ![Exécuter une instruction Spark SQL à l’aide du bloc-notes](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Exécuter une instruction Spark SQL à l’aide du bloc-notes")
   
    Pour les requêtes suivantes, vous pouvez sélectionner une nouvelle valeur dans la liste déroulante et réexécuter la requête. Cliquez sur **Paramètres** pour choisir ce qui constitue la clé et les valeurs dans le résultat. La capture d'écran ci-dessus utilise la clé **buildingID**, la moyenne **temp_diff** comme valeur, et le groupe **targettemp**.
7. Redémarrez l’interpréteur Livy pour quitter l’application. Pour ce faire, ouvrez les paramètres de l’interpréteur en cliquant sur le nom d’utilisateur connecté dans le coin supérieur droit, puis cliquez sur **Interpreter** (Interpréteur).
   
    ![Lancer l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sortie Hive")
8. Accédez aux paramètres d’interpréteur Livy, puis cliquez sur **Redémarrer**.
   
    ![Redémarrer l’interpréteur Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Redémarrer l’interpréteur Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Comment utiliser des packages externes avec le bloc-notes ?
Vous pouvez configurer le bloc-notes Zeppelin dans un cluster Apache Spark sur HDInsight (Linux) pour utiliser des packages externes bénéficiant de la contribution de la communauté, qui ne sont pas inclus dans le cluster. Vous pouvez rechercher le [référentiel Maven](http://search.maven.org/) pour obtenir la liste complète des packages disponibles. Vous pouvez également obtenir une liste des packages disponibles à partir d’autres sources. Par exemple, une liste complète des packages bénéficiant de la contribution de la communauté est disponible sur le site [Spark Packages](http://spark-packages.org/)(Packages Spark).

Dans cet article, vous allez apprendre à utiliser le package [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) avec le bloc-notes Jupyter.

1. Ouvrez les paramètres de l’interpréteur. Dans le coin supérieur droit, cliquez sur le nom d’utilisateur connecté, puis cliquez sur **Interpreter** (Interpréteur).
   
    ![Lancer l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sortie Hive")
2. Accédez aux paramètres d’interpréteur Livy, puis cliquez sur **Modifier**.
   
    ![Modifier les paramètres de l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Modifier les paramètres de l’interpréteur")
3. Ajoutez une nouvelle clé, appelée **livy.spark.jars.packages** et définissez sa valeur au format `group:id:version`. Par conséquent, si vous souhaitez utiliser le package [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), vous devez définir la valeur de la clé sur `com.databricks:spark-csv_2.10:1.4.0`.
   
    ![Modifier les paramètres de l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Modifier les paramètres de l’interpréteur")
   
    Cliquez sur **Enregistrer**, puis redémarrez l’interpréteur Livy.
4. **Conseil** : si vous souhaitez savoir comment atteindre la valeur de la clé entrée ci-dessus, lisez ce qui suit.
   
    a. Recherchez le package dans le référentiel Maven. Dans ce didacticiel, nous avons utilisé [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. À partir du référentiel, rassemblez les valeurs pour **GroupId**, **ArtifactId** et **Version**.
   
    ![Utiliser des packages externes avec le bloc-notes Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Utiliser des packages externes avec le bloc-notes Jupyter")
   
    c. Concaténez les trois valeurs séparées par deux-points (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Où les blocs-notes Zeppelin sont-ils enregistrés ?
Les blocs-notes Zeppelin sont enregistrés dans les nœuds principaux du cluster. Par conséquent, si vous supprimez le cluster, les blocs-notes seront aussi supprimés. Si vous souhaitez conserver vos blocs-notes pour une utilisation ultérieure sur les autres clusters, vous devez les exporter après avoir terminé l’exécution des tâches. Pour exporter un bloc-notes, cliquez sur l’icône **Exporter** comme illustré dans l’image ci-dessous.

![Télécharger le bloc-notes](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Télécharger le bloc-notes")

Cela permet d’enregistrer le bloc-notes en tant que fichier JSON dans l’emplacement de téléchargement.

## <a name="livy-session-management"></a>Gestion des sessions Livy
Lorsque vous exécutez le premier paragraphe de code dans votre bloc-notes Zeppelin, une nouvelle session Livy est créée dans votre cluster HDInsight Spark. Cette session est partagée par tous les blocs-notes Zeppelin que vous créerez par la suite. Si, pour une raison quelconque, la session Livy est arrêtée (redémarrage de cluster, etc.), vous ne serez pas en mesure d’exécuter de tâches à partir du bloc-notes Zeppelin.

Dans ce cas, vous devez effectuer les étapes suivantes avant de commencer à exécuter des tâches à partir d’un bloc-notes Zeppelin. 

1. Redémarrez l’interpréteur Livy à partir du bloc-notes Zeppelin. Pour ce faire, ouvrez les paramètres de l’interpréteur en cliquant sur le nom d’utilisateur connecté dans le coin supérieur droit, puis cliquez sur **Interpreter** (Interpréteur).
   
    ![Lancer l’interpréteur](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Sortie Hive")
2. Accédez aux paramètres d’interpréteur Livy, puis cliquez sur **Redémarrer**.
   
    ![Redémarrer l’interpréteur Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Redémarrer l’interpréteur Zeppelin")
3. Exécutez une cellule de code à partir d’un bloc-notes Zeppelin existant. Cela crée une session Livy dans le cluster HDInsight.

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
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







