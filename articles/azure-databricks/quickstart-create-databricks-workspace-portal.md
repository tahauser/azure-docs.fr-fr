---
title: "Démarrage rapide : Exécuter une tâche Spark sur Azure Databricks à l’aide du portail Azure | Microsoft Docs"
description: "Ce guide de démarrage rapide montre comment utiliser le portail Azure pour créer un espace de travail Azure Databricks et un cluster Apache Spark et exécuter une tâche Spark."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/22/2018
ms.author: nitinme
ms.custom: mvc
ms.openlocfilehash: f7ec8872849ad7881fb46bca5831c2985d003c13
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-portal"></a>Démarrage rapide : Exécuter une tâche Spark sur Azure Databricks à l’aide du portail Azure

Ce guide de démarrage rapide montre comment créer un espace de travail Azure Databricks et un cluster Apache Spark au sein de cet espace de travail. Enfin, vous découvrirez comment exécuter un travail Spark sur le cluster Databricks. Pour plus d’informations sur Azure Databricks, consultez [Présentation d’Azure Databricks](what-is-azure-databricks.md)

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-databricks-workspace"></a>Créer un espace de travail Databricks

Dans cette section, vous créez un espace de travail Azure Databricks en utilisant le portail Azure. 

1. Dans le portail Azure, cliquez sur **Créer une ressource**, cliquez sur **Données + Analytique**, puis cliquez sur **Azure Databricks (préversion)**. 

    ![Databricks sur le portail Azure](./media/quickstart-create-databricks-workspace-portal/azure-databricks-on-portal.png "Databricks sur le portail Azure")

2. Sous **Azure Databricks (Préversion)**, cliquez sur **Créer**.

3. Sous **Service Azure Databricks**, indiquez les valeurs suivantes :

    ![Créer un espace de travail Azure Databricks](./media/quickstart-create-databricks-workspace-portal/create-databricks-workspace.png "Créer un espace de travail Azure Databricks")

    * Pour **Nom de l’espace de travail**, spécifiez un nom pour votre espace de travail Databricks.
    * Pour **Abonnement**, sélectionnez votre abonnement Azure dans la liste déroulante.
    * Pour **Groupe de ressources**, indiquez si vous souhaitez créer un groupe de ressources Azure ou sélectionnez un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md).
    * Pour **Emplacement**, sélectionnez **États-Unis de l’Est 2**. Pour les autres régions disponibles, consultez [Disponibilité des services Azure par région](https://azure.microsoft.com/regions/services/).

4. Cliquez sur **Créer**.

## <a name="create-a-spark-cluster-in-databricks"></a>Créer un cluster Spark dans Databricks

1. Dans le portail Azure, accédez à l’espace de travail Databricks que vous avez créé, puis cliquez sur **Initialiser l’espace de travail**.

2. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, cliquez sur **Cluster**.

    ![Databricks sur Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks sur Azure")

3. Dans la page **Nouveau cluster**, renseignez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

    * Entrez un nom pour le cluster.
    * Veillez à cochez la case **Arrêter après ___ minutes d’activité**. Spécifiez une durée (en minutes) pour arrêter le cluster, si le cluster n’est pas utilisé.
    * Acceptez toutes les autres valeurs par défaut. 
    * Cliquez sur **Créer le cluster**. Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark.

Pour plus d’informations sur la création de clusters, consultez [Créer un cluster Spark dans Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Exécuter un travail Spark SQL

Avant de commencer cette section, vous devez effectuer les actions suivantes :

* [Création d’un compte de stockage Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account). 
* Téléchargez un exemple de fichier JSON [depuis GitHub](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json). 
* Chargez l’exemple de fichier JSON sur le compte de stockage Azure que vous avez créé. Vous pouvez utiliser [l’Explorateur Stockage Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) pour charger des fichiers.

Procédez comme suit pour créer un notebook dans Databricks, configurer le notebook pour la lecture de données d’un compte de stockage Blob Azure et exécuter un travail Spark SQL sur les données.

1. Dans le volet gauche, cliquez sur **Espace de travail**. Dans la liste déroulante **Espace de travail**, cliquez sur **Créer** et sur **Notebook**.

    ![Créer un notebook dans Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Créer un notebook dans Databricks")

2. Dans la boîte de dialogue **Créer un notebook**, entrez un nom, sélectionnez **Scala** comme langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Créer un notebook dans Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Créer un notebook dans Databricks")

    Cliquez sur **Créer**.

3. Dans l’extrait de code suivant, remplacez `{YOUR STORAGE ACCOUNT NAME}` par le nom du compte de stockage Azure que vous avez créé, et `{YOUR STORAGE ACCOUNT ACCESS KEY}` par la clé d’accès de votre compte de stockage. Collez l’extrait de code dans une cellule vide du notebook, puis appuyez sur Maj+Entrée pour exécuter la cellule de code. Cet extrait de code configure le notebook pour la lecture de données à partir d’un stockage Blob Azure.

       spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")
    
    Pour des instructions sur la récupération de la clé du compte de stockage, consultez [Gérer vos clés d’accès de stockage](../storage/common/storage-create-storage-account.md#manage-your-storage-account).

    > [!NOTE]
    > Vous pouvez également utiliser Azure Data Lake Store avec un cluster Spark sur Azure Databricks. Pour obtenir des instructions, consultez [Utiliser Data Lake Store avec Azure Databricks](https://go.microsoft.com/fwlink/?linkid=864084).

4. Exécutez une instruction SQL pour créer une table temporaire en utilisant les données de l’exemple de fichier de données JSON, **small_radio_json.json**. Dans l’extrait de code suivant, remplacez les valeurs des espaces réservés par le nom de votre conteneur et par le nom de votre compte de stockage. Collez l’extrait de code dans une cellule vide du notebook, puis appuyez sur Maj+Entrée. Dans l’extrait de code, `path` désigne l’emplacement de l’exemple de fichier JSON que vous avez chargé sur votre compte de stockage Azure.

    ```sql
    %sql 
    CREATE TEMPORARY TABLE radio_sample_data
    USING json
    OPTIONS (
     path "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/small_radio_json.json"
    )
    ```

    Une fois la commande terminée, vous avez toutes les données du fichier JSON sous forme de table dans le cluster Databricks.

    La commande magic `%sql` vous permet d’exécuter du code SQL à partir du notebook, même si celui-ci est d’un autre type. Pour plus d’informations, consultez [Mélange de langages dans un notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

5. Examinons un instantané des données JSON pour mieux comprendre la requête que nous exécutons. Collez l’extrait de code suivant dans une cellule de code, puis appuyez sur **Maj+Entrée**.

    ```sql
    %sql 
    SELECT * from radio_sample_data
    ```

6. Vous voyez une sortie tabulaire, comme celle qui est montrée dans la capture d’écran suivante (seules certaines colonnes apparaissent) :

    ![Exemples de données JSON](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Exemples de données JSON")

    Parmi d’autres informations, les exemples de données indiquent quel est le sexe des auditeurs d’une chaîne de radio (le nom de la colonne est **gender**), et si leur abonnement est gratuit ou payant (le nom de colonne est **level**).

7. Vous créez maintenant une représentation visuelle de ces données de façon à montrer, pour chaque sexe, le nombre d’utilisateurs avec un abonnement gratuit et avec un abonnement payant. Dans le bas de la sortie tabulaire, cliquez sur l’icône **Graphique à barres**, puis cliquez sur **Options de traçage**.

    ![Créer un graphique à barres](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Créer un graphique à barres")

8. Dans **Personnaliser le traçage**, faites un glisser-déplacer des valeurs comme indiqué dans la capture d’écran.

    ![Personnaliser le graphique à barres](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Personnaliser le graphique à barres")

    * Définissez **Clés** sur **gender**.
    * Définissez **Regroupements de séries** sur **level**.
    * Définissez **Valeurs** sur **level**.
    * Définissez **Agrégation** sur **COUNT**.

    Cliquez sur **Appliquer**.

9. La sortie montre la représentation visuelle, comme illustré dans la capture d’écran suivante :

     ![Personnaliser le graphique à barres](./media/quickstart-create-databricks-workspace-portal/databricks-sql-query-output-bar-chart.png "Personnaliser le graphique à barres")

## <a name="clean-up-resources"></a>Supprimer des ressources

Lors de la création du cluster Spark, si vous avez coché la case **Arrêter après __ minutes inactivité**, le cluster s’arrête automatiquement s’il a été inactif pendant la durée spécifiée.

Si vous n’avez pas coché la case, vous devez arrêter manuellement le cluster. Pour cela, dans l’espace de travail Azure Databricks, dans le volet gauche, cliquez sur **Clusters**. Pour le cluster que vous voulez arrêter, déplacez le curseur sur les points de suspension dans la colonne **Actions**, puis cliquez sur l’icône **Arrêter**.

![Arrêter le cluster Databricks](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Arrêter le cluster Databricks")

## <a name="next-steps"></a>étapes suivantes

Dans cet article, vous avez créé un cluster Spark dans Azure Databricks et vous avez exécuté un travail Spark avec des données dans Stockage Azure. Vous pouvez également consulter [Sources de données Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) pour découvrir comment importer des données à partir d’autres sources de données dans Azure Databricks. Passez à l’article suivant pour découvrir comment utiliser Azure Data Lake Store avec Azure Databricks.

> [!div class="nextstepaction"]
>[Utiliser Data Lake Store avec Azure Databricks](https://go.microsoft.com/fwlink/?linkid=864084)
