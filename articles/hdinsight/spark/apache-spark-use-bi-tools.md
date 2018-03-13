---
title: "BI Spark utilisant des outils de visualisation des données sur Azure HDInsight | Documents Microsoft"
description: "Utiliser des outils de visualisation de données à des fins d’analyse à l’aide d’Apache Spark BI sur des clusters HDInsight"
keywords: "apache spark bi,spark bi,visualisation de données spark,décisionnel spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: jgao
ms.openlocfilehash: 97305ec6774e89e776653adbcdcf86b1cd63642f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI utilisant des outils de visualisation de données avec Azure HDInsight

Découvrez comment utiliser [Microsoft Power BI](http://powerbi.microsoft.com) pour visualiser des données dans le cluster Apache Spark sur Azure HDInsight.

## <a name="prerequisites"></a>Prérequis

* **Terminez l’article [Exécuter des requêtes interactives sur des clusters Spark dans HDInsight](./apache-spark-load-data-run-query.md)**.
* **Power BI** : [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) et [abonnement d’évaluation à Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (facultatif).


## <a name="hivetable"></a>Vérifier les données

Le bloc-notes Jupyter que vous avez créé dans le [didacticiel précédent](apache-spark-load-data-run-query.md) inclut du code pour créer une table `hvac`. Cette table est basée sur le fichier CSV disponible sur tous les clusters HDInsight Spark à **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Utilisez la procédure suivante pour vérifier les données.

1. Dans le bloc-notes Jupyter, collez le code suivant, puis appuyez sur **MAJ + ENTRÉE**. Le code vérifie l’existence des tables.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    Le résultat se présente ainsi :

    ![Afficher les tables dans Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Si vous avez fermé le bloc-notes avant de commencer ce didacticiel, `hvactemptable` est nettoyée et n’est donc pas incluse dans la sortie.
    Seules les tables Hive qui sont stockées dans le metastore (colonne **isTemporary** définie sur **False**) sont accessibles à partir des outils décisionnels. Dans ce didacticiel, vous vous connectez à la table **hvac** que vous avez créée.

2. Collez le code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE**. Le code vérifie les données de la table.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    Le résultat se présente ainsi :

    ![Afficher les lignes de la table hvac dans Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. Arrêtez le bloc-notes pour libérer les ressources. 















## <a name="powerbi"></a>Utiliser Power BI

Dans cette section, vous utilisez Power BI pour créer des visualisations, des rapports et tableaux de bord à partir des données de cluster Spark. 

### <a name="create-a-report-in-power-bi-desktop"></a>Création d’un rapport dans Power BI Desktop
Les premières étapes de l’utilisation de Spark consistent à se connecter au cluster dans Power BI Desktop, à charger des données à partir du cluster et à créer une visualisation de base reposant sur ces données.

> [!NOTE]
> Le connecteur indiqué dans cet article est actuellement en préversion. Fournir vos commentaires via le site de la [Communauté Power BI](https://community.powerbi.com/) ou [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Ouvrez [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).
1. Dans l’onglet **Accueil**, cliquez sur **Obtenir des données**, puis sur **Plus**.

    ![Obtenir des données dans Power BI Desktop à partir d’HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Obtenir des données dans Power BI à partir d’Apache Spark BI")


2. Entrez `Spark` dans la zone de recherche, sélectionnez **Azure HDInsight Spark (bêta)**, puis cliquez sur **Connecter**.

    ![Obtenir des données dans Power BI à partir d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obtenir des données dans Power BI à partir Apache Spark BI")

3. Entrez l’URL de votre cluster (dans le formulaire `mysparkcluster.azurehdinsight.net`), sélectionnez **DirectQuery**, puis cliquez sur **OK**.

    Vous pouvez utiliser l’un ou l’autre des deux modes de connectivité des données avec Spark. Si vous utilisez DirectQuery, les modifications sont répercutées dans les rapports sans que la totalité du jeu de données soit actualisée. Si vous importez des données, vous devez actualiser le jeu de données pour voir les modifications. Pour plus d’informations sur la façon d’utiliser DirectQuery et sur quand y recourir, consultez [Utilisation de DirectQuery dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Entrez les informations du compte de connexion HDInsight, puis cliquez sur **Connecter**. Le nom du compte par défaut est *administrateur*.

5. Sélectionnez la `hvac`table, attendez que s’affiche un aperçu des données, puis cliquez sur **Charger**.

    ![Nom d’utilisateur et mot de passe du cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nom d’utilisateur et mot de passe du cluster Spark")

    Power BI Desktop dispose de toutes les informations nécessaires pour se connecter au cluster Spark et charger des données à partir de la `hvac` table. La table et ses colonnes sont affichées dans le volet **Champ**.  Voir la capture d’écran suivante :

6. Visualisez l’écart entre la température cible et la température réelle de chaque bâtiment : 

    1. Dans le volet **VISUALISATIONS**, sélectionnez **Graphique en aires**. 
    2. Faites glisser le champ **BuildingID** vers **Axe** et les champs **ActualTemp** et **TargetTemp** vers **Valeurs**.

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

        Le diagramme a l’aspect suivant :

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

        Par défaut, la visualisation affiche la somme des valeurs des champs **ActualTemp** et **TargetTemp**. Cliquez sur la flèche bas en regard d’**ActualTemp** et **TragetTemp** dans le volet Visualisations, vous pouvez voir que **Somme** est sélectionnée.

    3. Cliquez sur la flèche vers le bas en regard d’**ActualTemp** et **TragetTemp** dans le volet Visualisations, sélectionnez **Moyenne** pour obtenir une moyenne des températures réelle et cible de chaque bâtiment.

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

        La visualisation de vos données sera semblable à celle illustrée dans la capture d’écran. Déplacez le curseur sur la visualisation pour obtenir des info-bulles contenant des données pertinentes.

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

7. Cliquez sur **Fichier**, puis sur **Enregistrer** et entrez le nom `BuildingTemperature.pbix` pour le fichier. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publier le rapport sur le service Power BI (facultatif)

Le service Power BI vous permet de partager des rapports et tableaux de bord dans toute votre organisation. Dans cette section, vous publiez tout d’abord le jeu de données et le rapport. Ensuite, vous épinglez le rapport à un tableau de bord. Les tableaux de bord sont généralement utilisés pour se concentrer sur un sous-ensemble de données dans un rapport ; vous avez une seule visualisation dans votre rapport, mais elle est quand même utile pour exécuter la procédure.

1. Ouvrez Power BI Desktop.
2. À partir de l’ongle **Accueil**, cliquez sur **Publier**.

    ![Publier à partir de Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publier à partir de Power BI Desktop")

2. Sélectionnez l’espace de travail sur lequel vous souhaiter publier vos jeu de données et rapport, puis cliquez sur **Sélectionnez**. Dans l’image suivante, l’option par défaut **Mon espace de travail** est sélectionnée.

    ![Sélectionner l’espace de travail sur lequel publier le jeu de données et le rapport](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Sélectionner l’espace de travail sur lequel publier le jeu de données et le rapport") 

3. Une fois la publication réussie, cliquez sur **Ouvrir 'BuildingTemperature.pbix' dans Power BI**.

    ![Publication réussie, cliquer pour entrer les informations d’identification](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publication réussie, cliquer pour entrer les informations d’identification") 

4. Dans le service Power BI, cliquez sur **Entrer les informations d’identification**.

    ![Entrer les informations d’identification dans le service Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Entrer les informations d’identification dans le service Power BI")

5. Cliquez sur **Modifier les informations d’identification**.

    ![Modifier les informations d’identification dans le service Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Modifier les informations d’identification dans le service Power BI")

6. Entrez les informations du compte de connexion HDInsight, puis cliquez sur **Se connecter**. Le nom du compte par défaut est *administrateur*.

    ![Se connecter au cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Se connecter au cluster Spark")

7. Dans le volet gauche, accédez à **Espace de travail** > **Mon espace de travail** > **RAPPORTS**, puis cliquez sur **BuildingTemperature**.

    ![Rapport répertorié sous Rapports dans le volet gauche](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Rapport répertorié sous Rapports dans le volet gauche")

    Vous devez également voir **BuildingTemperature** sous **JEUX DE DONNÉES** dans le volet gauche.

    Le visuel que vous avez créé dans Power BI Desktop est désormais disponible dans le service Power BI. 

8. Pointez votre curseur sur la visualisation, puis cliquez sur l’icône d’épingle dans le coin supérieur droit.

    ![Rapport dans le service Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Rapport dans le service Power BI")

9. Sélectionnez « Nouveau tableau de bord », entrez le nom `Building temperature`, puis cliquez sur **Épingler**.

    ![Épingler au nouveau tableau de bord](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Épingler au nouveau tableau de bord")

10. Dans le rapport, cliquez sur **Accéder au tableau de bord**. 

Votre visuel est épinglé au tableau de bord ; vous pouvez ajouter d’autres visuels au rapport et les épingler au même tableau de bord. Pour plus d’informations sur les rapports et tableaux de bord, consultez [Rapports dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) et [Tableaux de bord dans le service Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>étapes suivantes

Jusqu’ici, vous avez appris à créer un cluster, des trames de données Spark pour interroger des données, et à accéder à ces données depuis des outils décisionnels. Vous pouvez maintenant suivre les instructions pour gérer les ressources du cluster et pour déboguer les tâches exécutées sur un cluster HDInsight Spark.

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)

