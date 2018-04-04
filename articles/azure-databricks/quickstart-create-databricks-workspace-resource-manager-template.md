---
title: 'Démarrage rapide : Exécuter un travail Spark sur Azure Databricks à l’aide d’un modèle Resource Manager | Microsoft Docs'
description: Ce démarrage rapide montre comment utiliser le modèle Resource Manager pour créer un espace de travail Azure Databricks et un cluster Apache Spark, puis exécuter un travail Spark.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/23/2018
ms.author: nitinme
ms.custom: mvc
ms.openlocfilehash: 8c1d4dd01d489413d1e0a0121b25e9506e5d6139
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-run-a-spark-job-on-azure-databricks-using-the-azure-resource-manager-template"></a>Démarrage rapide : Exécuter un travail Spark sur Azure Databricks à l’aide d’un modèle Azure Resource Manager

Ce démarrage rapide vous montre comment créer un espace de travail Azure Resource Databricks à l’aide d’un modèle Azure Resource Manager. L’espace de travail vous permet de créer un cluster Apache Spark et d’exécuter un travail Spark sur le cluster Databricks. Pour plus d’informations sur Azure Databricks, consultez [Présentation d’Azure Databricks](what-is-azure-databricks.md)

Dans ce démarrage rapide, dans le cadre du travail Spark, vous analysez des données d’abonnement à un canal de radio pour obtenir des informations sur l’utilisation gratuite/payante en fonction de données démographiques.

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

Dans cette section, vous créez un espace de travail Azure Databricks en utilisant le modèle Resource Manager. 

1. Cliquez sur l’image suivante pour ouvrir le modèle dans le portail Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-databricks-workspace%2Fazuredeploy.json" target="_blank"><img src="./media/quickstart-create-databricks-workspace-resource-manager-template/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Fournir les valeurs requises pour créer votre espace de travail Azure Databricks 

    ![Créer un espace de travail Azure Databricks à l’aide d’un modèle Azure Resource Manager](./media/quickstart-create-databricks-workspace-resource-manager-template/create-databricks-workspace-using-resource-manager-template.png "Créer un espace de travail Azure Databricks à l’aide d’un modèle Azure Resource Manager")

    Renseignez les valeurs suivantes : 
     
    |Propriété  |Description  |
    |---------|---------|
    |**Abonnement**     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
    |**Groupe de ressources**     | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Lieu**     | Sélectionnez **Est des États-Unis 2**. Pour les autres régions disponibles, consultez [Disponibilité des services Azure par région](https://azure.microsoft.com/regions/services/).        |
    |**Nom de l’espace de travail**     | Renseignez un nom pour votre espace de travail Databricks.        |
    |**Niveau tarifaire**     |  Choisissez entre **Standard** ou **Premium**. Pour plus d’informations sur ces niveaux, consultez la [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. Sélectionnez **J’accepte les termes et conditions mentionnés ci-dessus** et **Épingler au tableau de bord**, puis cliquez sur **Acheter**.

4. La création de l’espace de travail dure quelques minutes. Lors de la création de l’espace de travail, le portail affiche la vignette **Envoi du déploiement pour Azure Databricks** sur le côté droit. Vous devrez peut-être faire défiler votre tableau de bord vers la droite pour voir la vignette. Il existe également une barre de progression en haut de l’écran. Vous pouvez surveiller la progression de la zone souhaitée.

    ![Vignette de déploiement Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-deployment-tile.png "Vignette de déploiement Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Créer un cluster Spark dans Databricks

1. Dans le portail Azure, accédez à l’espace de travail Databricks que vous avez créé, puis cliquez sur **Initialiser l’espace de travail**.

2. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, cliquez sur **Cluster**.

    ![Databricks sur Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks sur Azure")

3. Dans la page **Nouveau cluster**, renseignez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

    Acceptez toutes les valeurs par défaut autres que les suivantes :

    * Entrez un nom pour le cluster.
    * Pour cet article, créez un cluster avec le runtime **4.0**. 
    * Veillez à cocher la case **Arrêter après ___ minutes d’inactivité**. Spécifiez une durée (en minutes) pour arrêter le cluster, si le cluster n’est pas utilisé.
    
    Sélectionnez **Créer un cluster**. Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark. 

Pour plus d’informations sur la création de clusters, consultez [Créer un cluster Spark dans Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="run-a-spark-sql-job"></a>Exécuter un travail Spark SQL

Avant de commencer cette section, vous devez effectuer les prérequis suivants :

* [Créez un compte de stockage Blob Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account). 
* Téléchargez un exemple de fichier JSON [depuis GitHub](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json). 
* Chargez l’exemple de fichier JSON sur le compte de stockage Blob Azure que vous avez créé. Vous pouvez utiliser [l’Explorateur Stockage Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) pour charger des fichiers.

Effectuez les tâches suivantes pour créer un notebook dans Databricks, configurer le notebook pour la lecture de données d’un compte de stockage Blob Azure et exécuter un travail Spark SQL sur les données.

1. Dans le volet gauche, cliquez sur **Espace de travail**. Dans la liste déroulante **Espace de travail**, cliquez sur **Créer** et sur **Notebook**.

    ![Créer un notebook dans Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Créer un notebook dans Databricks")

2. Dans la boîte de dialogue **Créer un notebook**, entrez un nom, sélectionnez **Scala** comme langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Créer un notebook dans Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Créer un notebook dans Databricks")

    Cliquez sur **Créer**.

3. Dans cette étape, associez le compte de Stockage Azure au cluster Spark Databricks. Il existe deux manières d’y parvenir. Vous pouvez monter le compte de Stockage Azure sur le système de fichiers Databricks Filesystem (DBFS) ou accéder directement au compte de Stockage Azure depuis l’application que vous créez.  

    > [!IMPORTANT]
    >Cet article utilise la méthode de **montage du stockage sur le système de fichiers DBFS**. Cette méthode garantit l’association du stockage monté et du cluster de système de fichiers. Ainsi, toute application qui accède au cluster est en mesure d’utiliser aussi le stockage associé. La méthode d’accès direct est limitée à l’application à partir de laquelle vous configurez l’accès.
    >
    > Pour utiliser la méthode de montage, vous devez créer un cluster Spark avec la version **4.0** du runtime de Databricks, que vous choisissez dans cet article.

    Dans l’extrait suivant, remplacez les valeurs `{YOUR CONTAINER NAME}`, `{YOUR STORAGE ACCOUNT NAME}` et `{YOUR STORAGE ACCOUNT ACCESS KEY}` par les valeurs correspondantes à votre compte de Stockage Azure. Collez l’extrait de code dans une cellule vide du notebook, puis appuyez sur Maj+Entrée pour exécuter la cellule de code.

    * **Monter le compte de stockage avec DBFS (recommandé)**. Dans cet extrait, le chemin du compte de Stockage Azure est monté à `/mnt/mypath`. Ainsi, à chaque fois que vous accèderez au compte de Stockage Azure, vous n’aurez pas besoin de renseigner le chemin complet. Vous pouvez juste utiliser `/mnt/mypath`.

          dbutils.fs.mount(
            source = "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/",
            mountPoint = "/mnt/mypath",
            extraConfigs = Map("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net" -> "{YOUR STORAGE ACCOUNT ACCESS KEY}"))

    * **Accès direct au compte de stockage**

          spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")

    Pour des instructions sur la récupération de la clé du compte de stockage, consultez [Gérer vos clés d’accès de stockage](../storage/common/storage-create-storage-account.md#manage-your-storage-account).

    > [!NOTE]
    > Vous pouvez également utiliser Azure Data Lake Store avec un cluster Spark sur Azure Databricks. Pour obtenir des instructions, consultez [Utiliser Data Lake Store avec Azure Databricks](https://go.microsoft.com/fwlink/?linkid=864084).

4. Exécutez une instruction SQL pour créer une table temporaire en utilisant les données de l’exemple de fichier de données JSON, **small_radio_json.json**. Dans l’extrait de code suivant, remplacez les valeurs des espaces réservés par le nom de votre conteneur et par le nom de votre compte de stockage. Collez l’extrait de code dans une cellule vide du notebook, puis appuyez sur Maj+Entrée. Dans l’extrait de code, `path` désigne l’emplacement de l’exemple de fichier JSON que vous avez chargé sur votre compte de stockage Azure.

    ```sql
    %sql 
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path "/mnt/mypath/small_radio_json.json"
    )
    ```

    Une fois la commande terminée, vous avez toutes les données du fichier JSON sous forme de table dans le cluster Databricks.

    La commande magic `%sql` vous permet d’exécuter du code SQL à partir du notebook, même si celui-ci est d’un autre type. Pour plus d’informations, consultez [Mélange de langages dans un notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

5. Examinons un instantané des données JSON pour mieux comprendre la requête que vous exécutez. Collez l’extrait de code suivant dans une cellule de code, puis appuyez sur **Maj+Entrée**.

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

Une fois l’article terminé, vous pouvez arrêter le cluster. Pour cela, dans l’espace de travail Azure Databricks, dans le volet gauche, sélectionnez **Clusters**. Pour le cluster que vous voulez arrêter, déplacez le curseur sur les points de suspension dans la colonne **Actions**, puis sélectionnez l’icône **Arrêter**.

![Arrêter un cluster Databricks](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Arrêter un cluster Databricks")

Si vous n’arrêtez pas le cluster manuellement, il s’arrêtera automatiquement, à condition d’avoir coché **Arrêter après __ minutes d’inactivité** lors de la création du cluster. Dans ce cas, le cluster s’arrête automatiquement s’il a été inactif pendant la période renseignée.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez créé un cluster Spark dans Azure Databricks et vous avez exécuté un travail Spark avec des données dans Stockage Azure. Vous pouvez également consulter [Sources de données Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) pour découvrir comment importer des données à partir d’autres sources de données dans Azure Databricks. Vous pouvez également consulter le modèle Resource Manager pour [créer un espace de travail Azure Databricks avec l’adresse de réseau virtuel personnalisée](https://github.com/Azure/azure-quickstart-templates/tree/master/101-databricks-workspace-with-custom-vnet-address).

Passez à l’article suivant pour savoir comment effectuer une opération ETL (extraction, transformation et chargement de données) à l’aide d’Azure Databricks.

> [!div class="nextstepaction"]
>[Extraire, transformer et charger des données à l’aide d’Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
