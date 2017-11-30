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
ms.date: 09/07/2017
ms.author: jgao
ms.openlocfilehash: 20952702ee9dbe9880c80dddc0d75f39e53b6659
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="create-an-apache-spark-cluster-in-azure-hdinsight"></a>Créer un cluster Apache Spark dans Azure HDInsight

Dans cet article, vous allez découvrir comment créer un cluster Apache Spark dans Azure HDInsight, puis à exécuter une requête Spark SQL sur une table Hive. Pour en savoir plus sur le service Spark sur HDInsight, consultez [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md).

   ![Schéma de démarrage rapide décrivant la procédure de création d’un cluster Apache Spark dans Azure HDInsight](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-quickstart-interactive-spark-query-flow.png "Démarrage rapide Spark à l’aide d’Apache Spark dans HDInsight. Étapes illustrées : création d’un cluster ; exécution d’une requête interactive Spark")

## <a name="prerequisites"></a>Composants requis

* **Un abonnement Azure**. Avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Voir [Créez votre compte Azure gratuit](https://azure.microsoft.com/free).

## <a name="create-hdinsight-spark-cluster"></a>Créer un cluster HDInsight Spark

Dans cette section, vous créez un cluster HDInsight Spark à l’aide d’un [modèle Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Pour obtenir d’autres méthodes de création de cluster, consultez [Création de clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Saisissez les valeurs suivantes :

    ![Créer un cluster HDInsight Spark à l’aide d’un modèle Azure Resource Manager](./media/apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Créer un cluster Spark dans HDInsight à l’aide d’un modèle Azure Resource Manager")

    * **Abonnement** : sélectionnez votre abonnement Azure pour ce cluster.
    * **Groupe de ressources** : créez un groupe de ressources ou sélectionnez un groupe existant. Le groupe de ressources est utilisé pour gérer des ressources Azure pour vos projets.
    * **Emplacement** : sélectionnez un emplacement pour le groupe de ressources. Le modèle utilise cet emplacement pour créer le cluster, ainsi que pour stocker le cluster par défaut.
    * **ClusterName** : entrez un nom pour le cluster HDInsight que vous souhaitez créer.
    * **Version de Spark** : sélectionnez **2.0** comme version que vous souhaitez installer sur le cluster.
    * **Nom d’utilisateur et mot de passe de cluster**: le nom de connexion par défaut est admin.
    * **Nom d’utilisateur et mot de passe SSH**.

   Notez ces valeurs.  Vous en aurez besoin plus loin dans le didacticiel.

3. Sélectionnez **J’accepte les termes et conditions mentionnés ci-dessus** et **Épingler au tableau de bord**, puis cliquez sur **Acheter**. Vous pouvez voir une nouvelle vignette intitulée Envoi du déploiement pour Déploiement de modèle. La création du cluster prend environ 20 minutes.

Si vous rencontrez un problème avec la création de clusters HDInsight, c’est que vous n’avez peut-être pas les autorisations requises pour le faire. Pour plus d’informations, consultez [Exigences de contrôle d’accès](../hdinsight-administer-use-portal-linux.md#create-clusters).

> [!NOTE]
> Cet article crée un cluster Spark qui utilise des [objets Blob Azure Storage en tant que cluster de stockage](../hdinsight-hadoop-use-blob-storage.md). Vous pouvez également créer un cluster Spark qui utilise [Azure Data Lake Store](../hdinsight-hadoop-use-data-lake-store.md) comme stockage par défaut. Pour plus d’informations, voir [Créer un cluster HDInsight avec Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).
>
>

## <a name="run-spark-sql-statements-on-a-hive-table"></a>Exécuter des instructions Spark SQL sur une table Hive

SQL (Structured Query Language) est le langage le plus courant et le plus largement utilisé pour interroger et définir des données. Les créateurs de Spark cherchaient à exploiter ces connaissances, en proposant le langage d’interrogation des données bien connu à un public plus large d’analystes qui souhaitent utiliser des données résidant sur un système de fichiers DFS Hadoop (HDFS). Spark SQL est la réponse à ce besoin. Il fonctionne en tant qu’extension d’Apache Spark pour le traitement des données structurées, à l’aide de la syntaxe SQL classique.

Spark SQL prend en charge les langages de requête SQL et HiveQL. Ses fonctionnalités incluent la liaison dans Python, Scala et Java. Celle-ci vous permet d’interroger des données stockées dans de nombreux emplacements, tels que des bases de données externes, des fichiers de données structurées (p. ex. JSON) et des tables Hive.

### <a name="running-spark-sql-on-an-hdinsight-cluster"></a>Exécution de Spark SQL sur un cluster HDInsight

Lorsque vous utilisez un bloc-notes Jupyter configuré pour votre cluster HDInsight Spark, vous obtenez une présélection `sqlContext` que vous pouvez utiliser pour exécuter des requêtes Hive à l’aide de Spark SQL. Dans cette section, vous apprenez à démarrer un bloc-notes Jupyter, puis à exécuter une requête Spark SQL de base sur une table Hive existante (**hivesampletable**), disponible sur tous les clusters HDInsight.

1. Ouvrez le [portail Azure](https://portal.azure.com/).

2. Si vous avez choisi d’épingler le cluster au tableau de bord, cliquez sur la mosaïque du cluster dans le tableau de bord pour lancer le panneau du cluster.

    Si vous n’avez pas épinglé le cluster au tableau de bord, dans le volet gauche, cliquez sur **Clusters HDInsight**, puis cliquez sur le cluster que vous avez créé.

3. À partir de **Liens rapides**, cliquez sur **Tableaux de bord de Cluster**, puis sur **Bloc-notes Jupyter**. Si vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster.

   ![Ouvrir le bloc-notes Jupyter pour exécuter une requête interactive Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-open-jupyter-interactive-spark-sql-query.png "Ouvrir un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL")

   > [!NOTE]
   > Vous pouvez également accéder au bloc-notes Jupyter pour votre cluster en ouvrant l'URL suivante dans votre navigateur. Remplacez **CLUSTERNAME** par le nom de votre cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Créez un bloc-notes. Cliquez sur **Nouveau**, puis sur **PySpark**.

   ![Créer un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Créer un bloc-notes Jupyter pour exécuter une requête interactive Spark SQL")

   Un nouveau bloc-notes est créé et ouvert sous le nom Untitled(Untitled.pynb).

4. Cliquez sur le nom du bloc-notes en haut, puis entrez un nom convivial si vous le souhaitez.

    ![Fournir un nom pour le bloc-notes Jupyter à partir duquel exécuter une requête interactive Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-jupyter-notebook-name.png "Fournir un nom pour le bloc-notes Jupyter à partir duquel exécuter une requête interactive Spark")

5.  Collez l’exemple de code suivant dans une cellule vide, puis appuyez sur **MAJ + ENTRÉE** pour exécuter le code. Dans le code ci-dessous, `%%sql` (appelé la commande magique sql) demande au bloc-notes Jupyter d’utiliser la présélection `sqlContext` pour exécuter la requête Hive. La requête extrait les 10 premières lignes d’une table Hive (**hivesampletable**) qui est disponible par défaut sur tous les clusters HDInsight.

        %%sql
        SELECT * FROM hivesampletable LIMIT 10

    ![Requête Hive dans HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query.png "Requête Hive dans HDInsight Spark")

    Pour plus d’informations sur la commande magique `%%sql` et les contextes de présélection, consultez [Noyaux Jupyter disponibles pour un cluster HDInsight](apache-spark-jupyter-notebook-kernels.md).

    > [!NOTE]
    > À chaque exécution d’une requête dans Jupyter, le titre de la fenêtre du navigateur web affiche l’état **(Occupé)** ainsi que le titre du bloc-notes. Un cercle plein s’affiche également en regard du texte **PySpark** dans le coin supérieur droit. Une fois le travail terminé, ce cercle est remplacé par un cercle vide.
    >
    >
    
6. L’écran devrait s’actualiser pour afficher la sortie de requête.

    ![Sortie de requête Hive dans HDInsight Spark](./media/apache-spark-jupyter-spark-sql/hdinsight-spark-get-started-hive-query-output.png "Sortie de requête Hive dans HDInsight Spark")

7. Arrêtez le bloc-notes pour libérer les ressources du cluster une fois l’exécution de l’application terminée. Pour ce faire, dans le menu **Fichier** du bloc-notes, cliquez sur **Fermer et arrêter**.

8. Si vous envisagez d’effectuer les étapes suivantes à une date ultérieure, assurez-vous de supprimer le cluster HDInsight que vous avez créé avec cet article. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-step"></a>Étape suivante 

Dans cet article, vous avez appris à créer un cluster HDInsight Spark et à exécuter une requête Spark SQL de base. Passer à l’article suivant pour apprendre à utiliser un cluster HDInsight Spark pour exécuter des requêtes interactives sur des données test.

> [!div class="nextstepaction"]
>[Exécuter des requêtes interactives sur un cluster HDInsight Spark](apache-spark-load-data-run-query.md)



