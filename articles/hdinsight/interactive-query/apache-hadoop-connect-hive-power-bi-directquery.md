---
title: Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight | Microsoft Docs
description: Découvrez comment utiliser Microsoft Power BI pour visualiser des données Interactive Query Hive traitées par Azure HDInsight.
keywords: hdinsight,hadoop,hive,interactive query,interactive hive,LLAP,directquery
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: jgao
ms.openlocfilehash: c74e02ea32a5aacf1a5208c0e194a25b0b6bcb48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Visualiser des données Interactive Query Hive avec Microsoft Power BI à l’aide de la requête directe dans Azure HDInsight

Découvrez comment connecter Microsoft Power BI aux clusters Azure HDInsight Interactive Query et comment visualiser des données Hive à l’aide de la requête directe. Dans ce didacticiel, vous allez charger les données d’une table Hive hivesampletable dans Power BI. La table Hive contient des données sur l’utilisation des téléphones mobiles. Vous allez ensuite tracer ces données d’utilisation sur une carte du monde :

![rapport cartographique Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Vous pouvez tirer parti du [pilote ODBC Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) pour importer effectivement via le connecteur ODBC générique dans Power BI Desktop. Il n’est toutefois pas recommandé pour les charges de travail décisionnelles en raison de la nature non interactive du moteur de requête Hive. Le [connecteur de requêtes interactives HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) et le [connecteur HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) sont les choix les plus judicieux en raison de leurs performances.

## <a name="prerequisites"></a>Prérequis

Avant de poursuivre cet article, vérifiez que vous avez les éléments suivants :

* Un **cluster HDInsight**. Cela peut être un cluster HDInsight avec Hive ou un cluster du nouveau type Interactive Query. Pour plus d’informations sur la création de clusters, consultez [Créer un cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Vous pouvez télécharger une copie à partir du [Centre de téléchargement Microsoft](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Chargement des données à partir de HDInsight

La table Hive hivesampletable est fournie avec tous les clusters HDInsight.

1. Connectez-vous à Power BI Desktop.
2. Cliquez sur l’onglet **Accueil**, cliquez sur **Obtenir des données** dans le ruban **Données externes**, puis sélectionnez **Plus**.

    ![données affichées Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. À partir du volet **Get Data** (Obtenir les données), tapez **hdinsight** dans la zone de recherche. Si vous ne voyez pas **HDInsight Interactive Query (version bêta)**, vous devez mettre à jour Power BI Desktop vers la dernière version.
4. Cliquez sur **HDInsight Interactive Query (version bêta)**, puis cliquez sur **Se connecter**.
5. Cliquez sur **Continuer** pour fermer la boîte de dialogue d’avertissement **Connecteur en version préliminaire**.
6. À partir de **HDInsight Interactive Query**, sélectionnez ou entrez les informations suivantes :

    - **Serveur** : entrez le nom du cluster Interactive Query, par exemple *myiqcluster.azurehdinsight.net*.
    - **Base de données** : pour ce didacticiel, entrez **default**.
    - **Mode de connectivité des données**: pour ce didacticiel, sélectionnez **DirectQuery**.

    ![HDInsight interactive query power bi directquery connect](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Cliquez sur **OK**.
8. Saisissez les informations d’identification de l’utilisateur HTTP, puis cliquez sur **OK**.  Le nom d’utilisateur par défaut est **admin**.
9. Dans le volet de gauche, sélectionnez **hivesampletale**, puis cliquez sur **Charger**.

    ![HDInsight interactive query power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Visualiser les données

Poursuivez la procédure précédente.

1. Dans le volet Visualisations, sélectionnez **Carte**  (icône représentant un globe).

    ![personnalisation de rapport Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. Dans le volet Champs, sélectionnez **country** et **devicemake**. Vous pouvez voir les données tracées sur la carte.
3. Développez la carte.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à visualiser des données de HDInsight à l’aide de Power BI.  Pour en savoir plus, consultez les articles suivants :

* [Visualiser des données Hive à l’aide de Microsoft Power BI et ODBC dans Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Connecter Excel à HDInsight avec le pilote ODBC Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Connexion d’Excel à Hadoop à l’aide de Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Se connecter à Azure HDInsight et exécuter des requêtes Hive avec Data Lake Tools pour Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Utiliser Azure HDInsight Tools pour Visual Studio Code](../hdinsight-for-vscode.md).
* [Charger des données dans HDInsight](./../hdinsight-upload-data.md).
