---
title: "Créer un cluster Apache Spark dans Azure HDInsight | Microsoft Docs"
description: "Démarrage rapide Spark HDInsight pour créer un cluster Apache Spark dans HDInsight."
keywords: "démarrage rapide spark,spark interactif,requête interactive,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/01/2018
ms.author: jgao
ms.openlocfilehash: baad137a6f982df987faf95d7c7c595698e8e399
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Créer un cluster Apache Spark dans Azure HDInsight

Découvrez comment créer un cluster Apache Spark sur Azure HDInsight et comment exécuter des requêtes Spark SQL sur des tables Apache Hive. Pour en savoir plus sur le service Spark sur HDInsight, consultez [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md).

## <a name="prerequisites"></a>configuration requise

* **Un abonnement Azure**. Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Voir [Créez votre compte Azure gratuit](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Créer un cluster HDInsight Spark

Créez un cluster HDInsight Spark à l’aide d’un [modèle Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md). Le modèle se trouve dans [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Pour obtenir d’autres méthodes de création de cluster, consultez [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Saisissez les valeurs suivantes :

    ![Créer un cluster HDInsight Spark à l’aide d’un modèle Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Créer un cluster Spark dans HDInsight à l’aide d’un modèle Azure Resource Manager")

    * **Abonnement** : sélectionnez l’abonnement Azure utilisé pour créer ce cluster.
    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez un groupe existant. Le groupe de ressources est utilisé pour gérer des ressources Azure pour vos projets.
    * **Emplacement** : sélectionnez un emplacement pour le groupe de ressources. Le modèle utilise cet emplacement pour créer le cluster, ainsi que pour stocker le cluster par défaut.
    * **ClusterName** : entrez un nom pour le cluster HDInsight que vous souhaitez créer.
    * **Nom d’utilisateur et mot de passe de cluster**: le nom de connexion par défaut est admin. Choisissez un mot de passe pour la connexion du cluster.
    * **Nom d’utilisateur et mot de passe SSH**. Choisissez un mot de passe pour l’utilisateur SSH.

3. Sélectionnez **J’accepte les termes et conditions mentionnés ci-dessus** et **Épingler au tableau de bord**, puis cliquez sur **Acheter**. Vous pouvez voir une nouvelle vignette intitulée **Déploiement du déploiement de modèle**. La création du cluster prend environ 20 minutes.

Si vous rencontrez un problème avec la création de clusters HDInsight, c’est que vous n’avez peut-être pas les autorisations requises pour le faire. Pour plus d’informations, consultez [Exigences de contrôle d’accès](../hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> Cet article crée un cluster Spark qui utilise des [objets Blob Azure Storage en tant que cluster de stockage](../hdinsight-hadoop-use-blob-storage.md). Vous pouvez également créer un cluster Spark qui utilise [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) comme stockage par défaut. Pour plus d’informations, voir [Créer un cluster HDInsight avec Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="create-a-jupyter-notebook"></a>Créer un bloc-notes Jupyter

[Bloc-notes Jupyter](http://jupyter.org) est un environnement de portable interactif qui prend en charge divers langages de programmation qui vous permettent d’interagir avec vos données, d’associer du code avec du texte Markdown et d’effectuer des visualisations simples. Spark sur HDInsight inclut également [Bloc-notes Zeppelin](apache-spark-zeppelin-notebook.md). Bloc-notes Jupyter est utilisé dans ce didacticiel.

**Pour créer un bloc-notes Jupyter**

1. Ouvrez le [portail Azure](https://portal.azure.com/).

2. Ouvrez le cluster Spark que vous avez créé. Pour obtenir des instructions, consultez la page [Énumération et affichage des clusters](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

3. À partir du portail, cliquez sur **Tableaux de bord de Cluster**, puis sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

   ![Ouvrir le bloc-notes Jupyter pour exécuter une requête interactive Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Ouvrir un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL")

   > [!NOTE]
   > Vous pouvez également accéder au bloc-notes Jupyter pour votre cluster en ouvrant l’URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Cliquez sur **Nouveau**, puis sur **Pyspark** pour créer un bloc-notes. Les blocs-notes Jupyter des clusters HDInsight prennent également en charge trois noyaux : **PySpark**, **PySpark3**, et **Spark**. Le noyau **PySpark** est utilisé dans ce didacticiel. Pour plus d’informations sur les noyaux et les avantages liés à l’utilisation de**PySpark**, consultez [Utiliser les noyaux de blocs-notes Jupyter avec les clusters Apache Spark dans HDInsight](apache-spark-jupyter-notebook-kernels.md).

   ![Créer un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Créer un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL")

   Un nouveau bloc-notes est créé et ouvert sous le nom Untitled(Untitled.pynb).

4. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial si vous le souhaitez.

    ![Fournir un nom pour le bloc-notes Jupyter à partir duquel exécuter une requête interactive Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Fournir un nom pour le bloc-notes Jupyter à partir duquel exécuter une requête interactive Spark")

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Exécuter des instructions Spark SQL sur une table Hive

SQL (Structured Query Language) est le langage le plus courant et le plus largement utilisé pour interroger et définir des données. Spark SQL fonctionne en tant qu’extension d’Apache Spark pour le traitement des données structurées, à l’aide de la syntaxe SQL classique.

Spark SQL prend en charge les langages de requête SQL et HiveQL. Ses fonctionnalités incluent la liaison dans Python, Scala et Java. Celle-ci vous permet d’interroger des données stockées dans de nombreux emplacements, tels que des bases de données externes, des fichiers de données structurées (p. ex. JSON) et des tables Hive.

Pour obtenir un exemple de lecture de données à partir d’un fichier csv au lieu d’une table Hive, consultez [Exécuter des requêtes interactives sur des clusters Spark dans HDInsight](apache-spark-load-data-run-query.md).

**Pour exécuter Spark SQL**

1. Lorsque vous démarrez le bloc-notes pour la première fois, le noyau effectue certaines tâches en arrière-plan. Attendez que le noyau soit prêt. Le noyau est prêt lorsque vous voyez un cercle vide à côté du nom du noyau dans le bloc-notes. Un cercle plein indique que le noyau est occupé.

    ![Requête Hive dans HDInsight Spark](./media/apache-spark-jupyter-spark-sql/jupyter-spark-kernel-status.png "Requête Hive dans HDInsight Spark")

2. Lorsque le noyau est prêt, collez l’exemple de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. La commande répertorie les tables Hive sur le cluster :

    ```PySpark
    %%sql
    SHOW TABLES
    ```
    Lorsque vous utilisez un bloc-notes Jupyter avec votre cluster HDInsight Spark, vous obtenez une présélection `sqlContext` que vous pouvez utiliser pour exécuter des requêtes Hive à l’aide de Spark SQL. `%%sql` demande au bloc-notes Jupyter d’utiliser la présélection `sqlContext` pour exécuter la requête Hive. La requête extrait les 10 premières lignes d’une table Hive (**hivesampletable**) qui est disponible par défaut sur tous les clusters HDInsight. Il faut environ 30 secondes pour obtenir les résultats. Le résultat se présente ainsi : 

    ![Requête Hive dans HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Requête Hive dans HDInsight Spark")

    Pour plus d’informations sur la commande magique `%%sql` et les contextes de présélection, consultez [Noyaux Jupyter disponibles pour un cluster HDInsight](apache-spark-jupyter-notebook-kernels.md).

    À chaque exécution d’une requête dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit.
    
2. Exécutez une autre requête pour afficher les données dans `hivesampletable`.

    ```PySpark
    %%sql
    SELECT * FROM hivesampletable LIMIT 10
    ```
    
    L’écran doit s’actualiser pour afficher la sortie de requête.

    ![Sortie de requête Hive dans HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Sortie de requête Hive dans HDInsight Spark")

2. Dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**. L’arrêt du bloc-notes libère les ressources de cluster.

3. Si vous envisagez d’effectuer les étapes suivantes à une date ultérieure, assurez-vous de supprimer le cluster HDInsight que vous avez créé avec cet article. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes 

Dans cet article, vous avez appris à créer un cluster HDInsight Spark et à exécuter une requête Spark SQL de base. Passer à l’article suivant pour apprendre à utiliser un cluster HDInsight Spark pour exécuter des requêtes interactives sur des données test.

> [!div class="nextstepaction"]
>[Exécuter des requêtes interactives sur un cluster HDInsight Spark](apache-spark-load-data-run-query.md)



