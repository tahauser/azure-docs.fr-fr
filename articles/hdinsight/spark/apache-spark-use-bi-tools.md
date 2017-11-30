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
ms.date: 10/24/2017
ms.author: jgao
ms.openlocfilehash: fabf48da80cf44e82068d180c896ebbca7078d18
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI utilisant des outils de visualisation de données avec Azure HDInsight

Découvrez comment utiliser Power BI et Tableau pour visualiser les données d’un cluster Apache Spark sur Azure HDInsight.

## <a name="prerequisites"></a>Composants requis

* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Exemple de données dans le cluster. Pour connaître les instructions, consultez [Exécuter des requêtes interactives sur un cluster HDInsight Spark](apache-spark-load-data-run-query.md).
* Power BI : [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) et [abonnement d’évaluation à Power BI](https://app.powerbi.com/signupredirect?pbi_source=web) (facultatif).
* Tableau : [Tableau Desktop](http://www.tableau.com/products/desktop) et [pilote ODBC Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229).


## <a name="hivetable"></a>Examiner l’exemple de données

Le bloc-notes Jupyter que vous avez créé dans le [didacticiel précédent](apache-spark-load-data-run-query.md) inclut du code pour créer une table `hvac`. Cette table est basée sur le fichier CSV disponible sur tous les clusters HDInsight Spark à **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Passons en revue les données du cluster Spark avant de créer des visualisations.

1. Vérifiez que les tables attendues existent. Dans une cellule vide du bloc-notes, collez l’extrait suivant, puis appuyez sur **MAJ + ENTRÉE**.

        %%sql
        SHOW TABLES

    Vous verrez une sortie identique à celle ci-dessous :

    ![Afficher les tables dans Spark](./media/apache-spark-use-bi-tools/show-tables.png)

    Si vous avez fermé le bloc-notes avant de commencer ce didacticiel, `hvactemptable` est nettoyée et n’est donc pas incluse dans la sortie.
    Seules les tables Hive qui sont stockées dans le metastore (colonne **isTemporary** définie sur **False**) sont accessibles à partir des outils décisionnels. Dans ce didacticiel, nous nous connectons à la table **hvac** que nous avons créée.

2. Vérifiez que la table contient les données attendues. Dans une cellule vide du bloc-notes, collez l’extrait suivant, puis appuyez sur **MAJ + ENTRÉE**.

        %%sql
        SELECT * FROM hvac LIMIT 10

    Vous verrez une sortie identique à celle ci-dessous :

    ![Afficher les lignes de la table hvac dans Spark](./media/apache-spark-use-bi-tools/select-limit.png)

3. Arrêtez le bloc-notes pour libérer les ressources. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**.

## <a name="powerbi"></a>Utiliser Power BI pour la visualisation de données Spark

Les données attendues existant effectivement, vous pouvez utiliser Power BI pour créer des visualisations, des rapports et des tableaux de bord à partir de ces données. Dans cet article, nous allons présenter un exemple simple avec des données statiques, mais si vous souhaitez voir des exemples de streaming plus complexes, faites-le nous savoir dans les commentaires.

### <a name="create-a-report-in-power-bi-desktop"></a>Création d’un rapport dans Power BI Desktop
Les premières étapes de l’utilisation de Spark consistent à se connecter au cluster dans Power BI Desktop, à charger des données à partir du cluster et à créer une visualisation de base reposant sur ces données.

> [!NOTE]
> Le connecteur illustré dans cet article est actuellement en préversion, mais nous vous encourageons à l’utiliser et à nous faire part de vos commentaires par le biais du site [Communauté Power BI](https://community.powerbi.com/) ou [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Dans Power BI Desktop, sous l’onglet **Accueil**, cliquez sur **Obtenir les données**, puis sur **Autres**.

2. Recherchez `Spark`, sélectionnez **Azure HDInsight Spark**, puis cliquez sur **Se connecter**.

    ![Obtenir des données dans Power BI à partir d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Obtenir des données dans Power BI à partir Apache Spark BI")

3. Entrez l’URL de votre cluster (sous la forme `mysparkcluster.azurehdinsight.net`), sélectionnez **DirectQuery**, puis cliquez sur **OK**.

    ![Se connecter à Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Se connecter à Apache Spark BI")

    > [!NOTE]
    > Vous pouvez utiliser l’un ou l’autre des deux modes de connectivité avec Spark. Si vous utilisez DirectQuery, les modifications sont répercutées dans les rapports sans que la totalité du jeu de données soit actualisée. Si vous importez des données, vous devez actualiser le jeu de données pour voir les modifications. Pour plus d’informations sur la façon d’utiliser DirectQuery et sur quand y recourir, consultez [Utilisation de DirectQuery dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Entrez les informations du compte de connexion HDInsight dans **Nom d’utilisateur** et **Mot de passe** (le compte par défaut est `admin`), puis cliquez sur **Se connecter**.

    ![Nom d’utilisateur et mot de passe du cluster Spark](./media/apache-spark-use-bi-tools/user-password.png "Nom d’utilisateur et mot de passe du cluster Spark")

5. Sélectionnez la table `hvac` et attendez que s’affiche un aperçu des données. Ensuite, cliquez sur **Charger**.

    ![Nom d’utilisateur et mot de passe du cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Nom d’utilisateur et mot de passe du cluster Spark")

    Power BI Desktop dispose désormais de toutes les informations nécessaires pour se connecter au cluster Spark et charger des données à partir de la table `hvac`. La table et ses colonnes sont affichées dans le volet **CHAMPS**.

    ![épertorier des tables sur le tableau de bord Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "épertorier des tables sur le tableau de bord Apache Spark BI")

7. Générez une visualisation pour afficher l’écart entre la température cible et la température réelle de chaque bâtiment : 

    1. Dans le volet **VISUALISATIONS**, sélectionnez **Graphique en aires**. Faites glisser le champ **BuildingID** vers **Axe** et les champs **ActualTemp** et **TargetTemp** vers **Valeurs**.

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

    2. Par défaut, la visualisation affiche la somme des valeurs des champs **ActualTemp** et **TargetTemp**. Pour les deux champs, sélectionnez **Moyenne** dans la liste déroulante afin d’obtenir la moyenne des températures réelles et cibles des chaque bâtiment.

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

    3. La visualisation des données doit être semblable à celle illustrée dans la capture d’écran. Déplacez le curseur sur la visualisation pour obtenir des info-bulles contenant des données pertinentes.

        ![Créer des visualisations de données Spark à l’aide d’Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Créer des visualisations de données Spark à l’aide d’Apache Spark BI")

11. Cliquez sur **Fichier**, puis sur **Enregistrer** et entrez le nom `spark.pbix` pour le fichier. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>Publier le rapport sur le service Power BI (facultatif)
Vous avez créé un rapport entièrement opérationnel dans Power BI Desktop ; vous pouvez vous arrêter là ou, comme de nombreuses personnes, tirer parti du service Power BI, qui vous permet de partager facilement les rapports et les tableaux de bord dans votre organisation. 

Dans cette section, vous publiez le jeu de données et le rapport qui se trouve dans le fichier Power BI Desktop que vous avez créé. Ensuite, vous épinglez la visualisation du rapport à un tableau de bord. Les tableaux de bord sont généralement utilisés pour se concentrer sur un sous-ensemble de données dans un rapport ; vous avez une seule visualisation dans votre rapport, mais elle est quand même utile pour exécuter la procédure.

1. Dans Power BI Desktop, sous l’onglet **Accueil**, cliquez sur **Publier**.

    ![Publier à partir de Power BI Desktop](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Publier à partir de Power BI Desktop")

2. Sélectionnez l’espace de travail sur lequel vous souhaiter publier vos jeu de données et rapport, puis cliquez sur **Sélectionnez**. Dans l’image suivante, l’option par défaut **Mon espace de travail** est sélectionnée.

    ![Sélectionner l’espace de travail sur lequel publier le jeu de données et le rapport](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Sélectionner l’espace de travail sur lequel publier le jeu de données et le rapport") 

3. Une fois que Power BI Desktop affiche un message indiquant que l’opération a réussi, cliquez sur **Ouvrir 'spark.pbix' dans Power BI**.

    ![Publication réussie, cliquer pour entrer les informations d’identification](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publication réussie, cliquer pour entrer les informations d’identification") 

4. Dans le service Power BI, cliquez sur **Entrer les informations d’identification**.

    ![Entrer les informations d’identification dans le service Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Entrer les informations d’identification dans le service Power BI")

5. Cliquez sur **Modifier les informations d’identification**.

    ![Modifier les informations d’identification dans le service Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Modifier les informations d’identification dans le service Power BI")

6. Entrez les informations du compte de connexion HDInsight (généralement le compte `admin` par défaut que vous avez utilisé dans Power BI Desktop), puis cliquez sur **Se connecter**.

    ![Se connecter au cluster Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Se connecter au cluster Spark")

7. Dans le volet gauche, accédez à **Espaces de travail** > **Mon espace de travail** > **RAPPORTS**, puis cliquez sur **spark**.

    ![Rapport répertorié sous Rapports dans le volet gauche](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Rapport répertorié sous Rapports dans le volet gauche")

    Vous devez également voir **spark** sous **JEUX DE DONNÉES** dans le volet gauche.

8. Le visuel que vous avez créé dans Power BI Desktop est désormais disponible dans le service. Pour épingler ce visuel à un tableau de bord, pointez dessus, puis cliquez sur l’icône en forme d’épingle.

    ![Rapport dans le service Power BI](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Rapport dans le service Power BI")

9. Sélectionnez « Nouveau tableau de bord », entrez le nom `SparkDemo`, puis cliquez sur **Épingler**.

    ![Épingler au nouveau tableau de bord](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Épingler au nouveau tableau de bord")

10. Dans le rapport, cliquez sur **Accéder au tableau de bord**. 

    ![Accéder au tableau de bord](./media/apache-spark-use-bi-tools/apache-spark-bi-open-dashboard.png "Accéder au tableau de bord")

Votre visuel est épinglé au tableau de bord ; vous pouvez ajouter d’autres visuels au rapport et les épingler au même tableau de bord. Pour plus d’informations sur les rapports et tableaux de bord, consultez [Rapports dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) et [Tableaux de bord dans le service Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="tableau"></a>Utiliser Tableau Desktop pour la visualisation de données Spark

> [!NOTE]
> Cette section s’applique uniquement aux clusters Spark 1.5.2 créés dans Azure HDInsight.
>
>

1. Installez [Tableau Desktop](http://www.tableau.com/products/desktop) sur l’ordinateur que vous utilisez pour exécuter ce didacticiel Apache Spark BI.

2. Vérifiez que l’ordinateur est également équipé du pilote ODBC de Microsoft Spark. Pour installer le pilote, cliquez [ici](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Lancez Tableau Desktop. Dans le volet gauche, dans la liste de serveurs auxquels se connecter, cliquez sur **Spark SQL**. Si Spark SQL n’est pas répertorié par défaut dans le volet gauche, cliquez sur **Autres serveurs**pour l’afficher.
2. Dans la boîte de dialogue de connexion à Spark SQL, entrez les valeurs telles qu’indiquées dans la capture d’écran, puis cliquez sur **OK**.

    ![Se connecter à un cluster pour Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Se connecter à un cluster pour Apache Spark BI")

    La liste déroulante d’authentification contient l’option **Windows Microsoft Azure HDInsight Service** uniquement si vous avez installé le [Pilote ODBC de Microsoft Spark](http://go.microsoft.com/fwlink/?LinkId=616229) sur l’ordinateur.
3. Dans l’écran suivant, dans la liste déroulante **Schéma**, cliquez sur l’icône **Rechercher**, puis sur **default**.

    ![Rechercher un schéma pour Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Rechercher un schéma pour Apache Spark BI")
4. Pour le champ **Table**, cliquez de nouveau sur l’icône **Rechercher** pour dresser la liste de toutes les tables Hive disponibles dans le cluster. La table **hvac** créée précédemment à l’aide du bloc-notes doit être indiquée.

    ![Rechercher un tableau pour Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Rechercher un tableau pour Apache Spark BI")
5. Effectuez un glisser-déplacer de la table vers la zone supérieure située à droite. Tableau importe les données et affiche le schéma mis en exergue par l’encadré rouge.

    ![Ajouter des tables à Tableau pour Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Ajouter des tables à Tableau pour Apache Spark BI")
6. Cliquez sur l’onglet **Sheet1** en bas à gauche. Réalisez une visualisation qui affiche la moyenne des températures cibles et réelles de tous les bâtiments à chaque date. Faites glisser **Date** et **Building ID** vers **Columns** et **Actual Temp**/**Target Temp** vers **Rows**. Sous **Marks**, sélectionnez **Area** pour utiliser une carte de zone pour la visualisation de données Spark.

     ![Ajouter des champs pour une visualisation de données Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Ajouter des champs pour une visualisation de données Spark")
7. Par défaut, les champs de température sont affichés en tant qu’agrégat. Si vous préférez afficher les températures moyennes, vous pouvez le faire à partir de la liste déroulante, comme indiqué ci-dessous.

    ![Prendre une moyenne de températures pour une visualisation de données Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Prendre une moyenne de températures pour une visualisation de données Spark")

8. Vous pouvez également superposer les cartes des températures pour mieux comprendre la différence entre les températures cibles et réelles. Déplacez la souris vers le coin de la carte inférieure jusqu’à ce qu’une poignée s’affiche dans un cercle rouge. Faites glisser la carte vers l’autre carte située au-dessus, puis relâchez la souris lorsque la poignée s’affiche dans un rectangle rouge.

    ![Fusionner des cartes pour une visualisation de données Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Fusionner des cartes pour une visualisation de données Spark")

     La visualisation des données doit changer comme illustré dans la capture d’écran :

    ![Sortie de tableau pour une visualisation de données Spark](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Sortie de tableau pour une visualisation de données Spark")
9. Cliquez sur **Save** pour enregistrer la feuille de calcul. Vous pouvez créer des tableaux de bord et leur ajouter une ou plusieurs feuilles.

## <a name="next-steps"></a>Étapes suivantes

Jusqu’ici, vous avez appris à créer un cluster, des trames de données Spark pour interroger des données, et à accéder à ces données depuis des outils décisionnels. Vous pouvez maintenant suivre les instructions pour gérer les ressources du cluster et pour déboguer les tâches exécutées sur un cluster HDInsight Spark.

* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)

