---
title: 'Didacticiel : Effectuer des opérations ETL à l’aide d’Azure Databricks | Microsoft Docs'
description: Découvrez comment extraire des données de Data Lake Store dans Azure Databricks, les transformer, puis les charger dans Azure SQL Data Warehouse.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.custom: mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: c3aa87f2c74175d1b61a8db6a9c7a0318a408658
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-extract-transform-and-load-data-using-azure-databricks"></a>Didacticiel : Extraire, transformer et charger des données à l’aide d’Azure Databricks

Dans ce didacticiel, vous allez effectuer une opération ETL (extraction, transformation et chargement de données) à l’aide d’Azure Databricks. Vous extrayez des données d’Azure Data Lake Store dans Azure Databricks, exécutez des transformations sur les données dans Azure Databricks, puis chargez les données transformées dans Azure SQL Data Warehouse. 

Les étapes décrites dans ce didacticiel utilisent le connecteur SQL Data Warehouse pour Azure Databricks, afin de transférer des données à Azure Databricks. Ce connecteur utilise ensuite le Stockage Blob Azure en tant que stockage temporaire pour les données transférées entre un cluster Azure Databricks et Azure SQL Data Warehouse.

L’illustration suivante montre le flux d’application :

![Azure Databricks avec Data Lake Store et SQL Data Warehouse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks avec Data Lake Store et SQL Data Warehouse")

Ce didacticiel décrit les tâches suivantes : 

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks
> * Créer un cluster Spark dans Azure Databricks
> * Créer un compte Azure Data Lake Store
> * Charger des données dans Azure Data Lake Store
> * Créer un notebook dans Azure Databricks
> * Extraire des données de Data Lake Store
> * Transformer des données dans Azure Databricks
> * Chargement de données dans Azure SQL Data Warehouse

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis


Avant de commencer le didacticiel, veillez à disposer des éléments suivants :
- Créez une instance Azure SQL Data Warehouse, créez une règle de pare-feu au niveau du serveur et connectez-vous au serveur en tant qu’administrateur du serveur. Suivez les instructions indiquées dans [Démarrage rapide : créer et interroger un entrepôt de données SQL Azure dans le portail Azure](../sql-data-warehouse/create-data-warehouse-portal.md).
- Créez une clé principale de base de données pour Azure SQL Data Warehouse. Suivez les instructions indiquées dans [Créer une clé principale de base de données](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).
- Créez un compte de stockage Blob Azure et un conteneur dans celui-ci. Récupérez également la clé d’accès au compte de stockage. Suivez les instructions indiquées dans [Démarrage rapide : Charger, télécharger et répertorier des objets blob à l’aide du portail Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="log-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-azure-databricks-workspace"></a>Créer un espace de travail Azure Databricks

Dans cette section, vous créez un espace de travail Azure Databricks en utilisant le portail Azure. 

1. Dans le portail Azure, sélectionnez **Créer une ressource** > **Données + Analytique** > **Azure Databricks**. 

    ![Databricks sur le portail Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png "Databricks sur le portail Azure")

3. Sous **Service Azure Databricks**, renseignez les valeurs pour créer un espace de travail Databricks.

    ![Créer un espace de travail Azure Databricks](./media/databricks-extract-load-sql-data-warehouse/create-databricks-workspace.png "Créer un espace de travail Azure Databricks")

    Renseignez les valeurs suivantes : 
     
    |Propriété  |Description  |
    |---------|---------|
    |**Nom de l’espace de travail**     | Renseignez un nom pour votre espace de travail Databricks.        |
    |**Abonnement**     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
    |**Groupe de ressources**     | Indiquez si vous souhaitez créer un groupe de ressources Azure ou utiliser un groupe existant. Un groupe de ressources est un conteneur réunissant les ressources associées d’une solution Azure. Pour plus d’informations, consultez [Présentation des groupes de ressources Azure](../azure-resource-manager/resource-group-overview.md). |
    |**Lieu**     | Sélectionnez **Est des États-Unis 2**. Pour les autres régions disponibles, consultez [Disponibilité des services Azure par région](https://azure.microsoft.com/regions/services/).        |
    |**Niveau tarifaire**     |  Choisissez entre **Standard** ou **Premium**. Pour plus d’informations sur ces niveaux, consultez la [page de tarification Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

    Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

4. La création du compte prend quelques minutes. Lors de la création d’un compte, le portail affiche la vignette **Envoi du déploiement pour Azure Databricks** sur le côté droit. Vous devrez peut-être faire défiler votre tableau de bord vers la droite pour voir la vignette. Il existe également une barre de progression en haut de l’écran. Vous pouvez surveiller la progression de la zone souhaitée.

    ![Vignette de déploiement Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-deployment-tile.png "Vignette de déploiement Databricks")

## <a name="create-a-spark-cluster-in-databricks"></a>Créer un cluster Spark dans Databricks

1. Dans le portail Azure, accédez à l’espace de travail Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. Vous êtes redirigé vers le portail Azure Databricks. Dans le portail, sélectionnez **Cluster**.

    ![Databricks sur Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks sur Azure")

3. Dans la page **Nouveau cluster**, renseignez les valeurs pour créer un cluster.

    ![Créer un cluster Databricks Spark sur Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Créer un cluster Databricks Spark sur Azure")

    Acceptez toutes les valeurs par défaut autres que les suivantes :

    * Entrez un nom pour le cluster.
    * Pour cet article, créez un cluster avec le runtime **4.0**. 
    * Veillez à cocher la case **Arrêter après ___ minutes d’inactivité**. Spécifiez une durée (en minutes) pour arrêter le cluster, si le cluster n’est pas utilisé.
    
    Sélectionnez **Créer un cluster**. Une fois que le cluster est en cours d’exécution, vous pouvez y attacher des notebooks et exécuter des travaux Spark.

## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte Azure Data Lake Store

Dans cette section, vous créez un compte Azure Data Lake Store et vous l’associez à un principal du service Azure Active Directory. Plus loin dans ce didacticiel, vous utilisez ce principal du service dans Azure Databricks pour accéder à Azure Data Lake Store. 

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **Créer une ressource** > **Stockage** > **Data Lake Store**.
3. Dans le panneau **Nouveau magasin Data Lake Store**, fournissez les valeurs indiquées dans la capture d’écran suivante :
   
    ![Créer un compte Azure Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/create-new-datalake-store.png "Créer un compte Azure Data Lake")

    Renseignez les valeurs suivantes : 
     
    |Propriété  |Description  |
    |---------|---------|
    |**Name**     | Entrez un nom unique pour le compte Data Lake Store.        |
    |**Abonnement**     | Sélectionnez votre abonnement Azure dans la liste déroulante.        |
    |**Groupe de ressources**     | Pour ce didacticiel, sélectionnez le même groupe de ressources que celui que vous avez utilisé lors de la création de l’espace de travail Azure Databricks.  |
    |**Lieu**     | Sélectionnez **Est des États-Unis 2**.  |
    |**Package des prix**     |  Sélectionnez **Paiement à l’utilisation**. |
    | **Paramètres de chiffrement** | Conservez les paramètres par défaut. |

    Sélectionnez **Épingler au tableau de bord**, puis sélectionnez **Créer**.

Vous créez maintenant un principal du service Azure Active Directory et associez-le au compte Data Lake Store que vous avez créé.

### <a name="create-an-azure-active-directory-service-principal"></a>Créer un principal du service Azure Active Directory
   
1. Dans le [portail Azure](https://portal.azure.com), sélectionnez **All services** (Tous les services), puis recherchez **Azure Active Directory**.

2. Sélectionnez **Inscriptions d’applications**.

   ![sélectionner des inscriptions d’applications](./media/databricks-extract-load-sql-data-warehouse/select-app-registrations.png)

3. Sélectionnez **Nouvelle inscription d’application**.

   ![ajouter une application](./media/databricks-extract-load-sql-data-warehouse/select-add-app.png)

4. Fournissez un nom et une URL pour l’application. Sélectionnez **Application Web / API** pour le type d’application que vous souhaitez créer. Fournissez une URL de connexion, puis sélectionnez **Créer**.

   ![nommer l’application](./media/databricks-extract-load-sql-data-warehouse/create-app.png)

Pour accéder au compte Data Lake Store depuis Azure Databricks, vous devez avoir les valeurs suivantes pour le principal du service Azure Active Directory que vous avez créé :
- ID de l'application
- Clé d’authentification
- ID client

Dans les sections suivantes, vous récupérez ces valeurs pour le principal du service Azure Active Directory que vous avez créé précédemment.

### <a name="get-application-id-and-authentication-key-for-the-service-principal"></a>Obtenir un ID d’application et une clé d’authentification pour le principal du service

Lors d’une connexion par programmation, vous aurez besoin de l’ID de votre application et d’une clé d’authentification. Pour obtenir ces valeurs, procédez comme suit :

1. Dans **Inscriptions d’applications** dans Azure Active Directory, sélectionnez votre application.

   ![sélectionner une application](./media/databricks-extract-load-sql-data-warehouse/select-app.png)

2. Copiez l’**ID d’application** et stockez-le dans votre code d’application. Certains [exemples d’applications](#log-in-as-the-application) font référence à cette valeur en tant qu’ID client.

   ![ID client](./media/databricks-extract-load-sql-data-warehouse/copy-app-id.png)

3. Pour générer une clé d’authentification, sélectionnez **Paramètres**.

   ![sélectionner paramètres](./media/databricks-extract-load-sql-data-warehouse/select-settings.png)

4. Pour générer une clé d’authentification, sélectionnez **Clés**.

   ![sélectionner des clés](./media/databricks-extract-load-sql-data-warehouse/select-keys.png)

5. Fournissez une description de la clé et la durée de la clé. Lorsque vous avez terminé, sélectionnez **Enregistrer**.

   ![enregistrer une clé](./media/databricks-extract-load-sql-data-warehouse/save-key.png)

   Après avoir enregistré la clé, la valeur de la clé s’affiche. Copiez cette valeur car vous ne pourrez pas récupérer la clé ultérieurement. Vous fournissez la valeur de la clé avec l’ID d’application pour vous connecter en tant qu’application. Stockez la valeur de la clé à un emplacement où votre application peut la récupérer.

   ![clé enregistrée](./media/databricks-extract-load-sql-data-warehouse/copy-key.png)

### <a name="get-tenant-id"></a>Obtenir l’ID de locataire

Lors d’une connexion par programmation, vous devez transmettre l’ID de locataire avec votre demande d’authentification.

1. Sélectionnez **Azure Active Directory**.

   ![sélectionner azure active directory](./media/databricks-extract-load-sql-data-warehouse/select-active-directory.png)

1. Pour obtenir l’ID de locataire, sélectionnez **Propriétés** pour votre client Azure AD.

   ![Sélectionnez les propriétés Azure AD](./media/databricks-extract-load-sql-data-warehouse/select-ad-properties.png)

1. Copiez l’**ID Directory**. Cette valeur est votre ID de locataire.

   ![ID client](./media/databricks-extract-load-sql-data-warehouse/copy-directory-id.png) 

### <a name="associate-service-principal-with-azure-data-lake-store"></a>Associer le principal du service Azure Data Lake Store

Dans cette section, vous associez le compte Azure Data Lake Store au principal du service Azure Active Directory que vous avez créé. Cela garantit que vous pouvez accéder au compte Data Lake Store à partir d’Azure Databricks.

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le compte Data Lake Store que vous avez créé.

2. Dans le volet gauche, sélectionnez **Contrôle d’accès** > **Ajouter**.

    ![Ajouter l’accès à Data Lake Store](./media/databricks-extract-load-sql-data-warehouse/add-adls-access.png "Ajouter l’accès à Data Lake Store")

3. Dans **Ajouter des autorisations**, sélectionnez un rôle que vous souhaitez affecter au principal du service. Sélectionnez **Propriétaire** pour ce didacticiel. Pour **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou application Azure AD**. Pour **Sélectionner**, entrez le nom du principal du service que vous avez créé pour filtrer le nombre de principaux du service à partir desquels effectuer la sélection.

    ![Sélectionner un principal du service](./media/databricks-extract-load-sql-data-warehouse/select-service-principal.png "Sélectionner un principal du service")

    Sélectionnez le principal du service que vous avez créé précédemment, puis sélectionnez **Enregistrer**. Le principal du service est maintenant associé au compte Azure Data Lake Store.

## <a name="upload-data-to-data-lake-store"></a>Téléchargement de données vers Data Lake Store

Dans cette section, vous chargez un exemple de fichier de données sur Data Lake Store. Vous allez utiliser ce fichier ultérieurement dans Azure Databricks pour exécuter quelques transformations. Les exemples de données (**small_radio_json.json**) que vous utilisez dans ce didacticiel sont disponibles dans ce [référentiel Github](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json).

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le compte Data Lake Store que vous avez créé.

2. Dans l’onglet **Vue d’ensemble**, cliquez sur **Explorateur de données**.

    ![Ouvrir l’Explorateur de données](./media/databricks-extract-load-sql-data-warehouse/open-data-explorer.png "Ouvrir l’Explorateur de données")

3. Dans l’Explorateur de données, cliquez sur **Charger**.

    ![Option de chargement](./media/databricks-extract-load-sql-data-warehouse/upload-to-data-lake-store.png "Option de chargement")

4. Dans **Charger des fichiers**, accédez à l’emplacement de votre exemple de fichier de données, puis sélectionnez **Ajouter les fichiers sélectionnés**.

    ![Option de chargement](./media/databricks-extract-load-sql-data-warehouse/upload-data.png "Option de chargement")

5. Dans ce didacticiel, vous avez chargé le fichier de données à la racine de Data Lake Store. Par conséquent, le fichier est maintenant disponible à l’emplacement `adl://<YOUR_DATA_LAKE_STORE_ACCOUNT_NAME>.azuredatalakestore.net/small_radio_json.json`.

## <a name="extract-data-from-data-lake-store"></a>Extraire des données de Data Lake Store

Dans cette section, vous créez un bloc-notes dans l’espace de travail Azure Databricks, puis exécutez les extraits de code pour récupérer des données de Data Lake Store dans Azure Databricks.

1. Dans le [portail Azure](https://portal.azure.com), accédez à l’espace de travail Azure Databricks que vous avez créé, puis sélectionnez **Initialiser l’espace de travail**.

2. Dans le volet gauche, sélectionnez **Espace de travail**. Dans la liste déroulante **Espace de travail**, sélectionnez **Créer** > **Notebook**.

    ![Créer un notebook dans Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Créer un notebook dans Databricks")

2. Dans la boîte de dialogue **Créer un bloc-notes**, entrez un nom pour le bloc-notes. Sélectionnez **Scala** comme langage, puis sélectionnez le cluster Spark que vous avez créé précédemment.

    ![Créer un notebook dans Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Créer un notebook dans Databricks")

    Sélectionnez **Créer**.

3. Ajoutez l’extrait de code suivant dans une cellule de code vide et remplacez les valeurs d’espace réservé avec les valeurs que vous avez enregistrées précédemment pour le principal du service Azure Active Directory.

        spark.conf.set("dfs.adls.oauth2.access.token.provider.type", "ClientCredential")
        spark.conf.set("dfs.adls.oauth2.client.id", "<APPLICATION-ID>")
        spark.conf.set("dfs.adls.oauth2.credential", "<AUTHENTICATION-KEY>")
        spark.conf.set("dfs.adls.oauth2.refresh.url", "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token")

    Appuyez sur **Maj+Entrée** pour exécuter la cellule de code.

4. Vous pouvez maintenant charger l’exemple de fichier json dans Data Lake Store comme une trame de données dans Azure Databricks. Collez l’extrait de code suivant dans une nouvelle cellule de code, remplacez la valeur d’espace réservé, puis appuyez sur **MAJ + ENTRÉE**.

        val df = spark.read.json("adl://<DATA LAKE STORE NAME>.azuredatalakestore.net/small_radio_json.json")

5. Exécutez l’extrait de code suivant pour afficher le contenu de la trame de données.

        df.show()

    Le résultat ressemble à ce qui suit :

        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
        +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
        | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
        | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
        | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
        ...
        ...

Vous avez extrait les données d’Azure Data Lake Store dans Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Transformer des données dans Azure Databricks

Les exemples de données brutes **small_radio_json.json** capturent l’audience d’une station de radio et possèdent plusieurs colonnes. Dans cette section, vous transformez les données pour récupérer uniquement des colonnes spécifiques dans le jeu de données. 

1. Commencez en récupérant uniquement les colonnes *firstName*, *lastName*, *gender*, *location* et *level* à partir de la trame de données que vous avez déjà créée.

        val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")

    Vous obtenez alors une sortie, comme montré dans l’extrait de code suivant :

        +---------+----------+------+--------------------+-----+
        |firstname|  lastname|gender|            location|level|
        +---------+----------+------+--------------------+-----+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Tess|  Townsend|     F|Nashville-Davidso...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |     Liam|     Watts|     M|New York-Newark-J...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        |     Alan|     Morse|     M|Chicago-Napervill...| paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
        +---------+----------+------+--------------------+-----+

2.  Vous pouvez transformer ces données pour renommer la colonne **level** en **subscription_type**.

        val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
        renamedColumnsDF.show()

    Vous obtenez alors une sortie, comme montré dans l’extrait de code suivant.

        +---------+----------+------+--------------------+-----------------+
        |firstname|  lastname|gender|            location|subscription_type|
        +---------+----------+------+--------------------+-----------------+
        | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |     Liam|     Watts|     M|New York-Newark-J...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
        |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
        |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
        +---------+----------+------+--------------------+-----------------+

## <a name="load-data-into-azure-sql-data-warehouse"></a>Chargement de données dans Azure SQL Data Warehouse

Dans cette section, vous chargez les données transformées dans Azure SQL Data Warehouse. À l’aide du connecteur Azure SQL Data Warehouse pour Azure Databricks, vous pouvez charger directement une trame de données sous forme de table dans l’entrepôt de données SQL.

Comme mentionné précédemment, le connecteur d’entrepôt de données SQL utilise le Stockage Blob Azure comme stockage temporaire pour charger des données entre Azure Databricks et Azure SQL Data Warehouse. Vous commencez donc par fournir la configuration pour vous connecter au compte de stockage. Vous devez déjà avoir créé le compte dans le cadre de la configuration requise pour cet article.

1. Fournissez la configuration pour accéder au compte de Stockage Azure à partir d’Azure Databricks.

        val blobStorage = "<STORAGE ACCOUNT NAME>.blob.core.windows.net"
        val blobContainer = "<CONTAINER NAME>"
        val blobAccessKey =  "<ACCESS KEY>"

2. Spécifiez un dossier temporaire qui sera utilisé lors du déplacement des données entre Azure Databricks et Azure SQL Data Warehouse.

        val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"

3. Exécutez l’extrait de code suivant pour stocker les clés d’accès du Stockage Blob Azure dans la configuration. Cela garantit que vous n’avez pas à conserver la clé d’accès dans le bloc-notes en texte brut.

        val acntInfo = "fs.azure.account.key."+ blobStorage
        sc.hadoopConfiguration.set(acntInfo, blobAccessKey)

4. Indiquez les valeurs pour vous connecter à l’instance Azure SQL Data Warehouse. Vous devez avoir créé un entrepôt de données SQL dans le cadre de la configuration requise.

        //SQL Data Warehouse related settings
        val dwDatabase = "<DATABASE NAME>"
        val dwServer = "<DATABASE SERVER NAME>" 
        val dwUser = "<USER NAME>"
        val dwPass = "<PASSWORD>"
        val dwJdbcPort =  "1433"
        val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
        val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
        val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ".database.windows.net:" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass

5. Exécutez l’extrait de code suivant pour charger la trame de données transformée, **renamedColumnsDF**, en tant que table dans l’entrepôt de données SQL. Cet extrait de code crée une table appelée **SampleTable** dans la base de données SQL.

        spark.conf.set(
          "spark.sql.parquet.writeLegacyFormat",
          "true")
        
        renamedColumnsDF.write
            .format("com.databricks.spark.sqldw")
            .option("url", sqlDwUrlSmall) 
            .option("dbtable", "SampleTable")
            .option( "forward_spark_azure_storage_credentials","True")
            .option("tempdir", tempDir)
            .mode("overwrite")
            .save()

6. Connectez-vous à la base de données SQL et vérifiez que vous y voyez une table **SampleTable**.

    ![Vérifier la présence de l’exemple de table](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Vérifier la présence de l’exemple de table")

7. Exécutez une requête SELECT pour vérifier le contenu de la table. Elle doit avoir les mêmes données que celles présentes dans la trame de données **renamedColumnsDF**.

    ![Vérifier le contenu de l’exemple de table](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Vérifier le contenu de l’exemple de table")

## <a name="clean-up-resources"></a>Supprimer des ressources

Une fois le didacticiel terminé, vous pouvez arrêter le cluster. Pour cela, dans l’espace de travail Azure Databricks, dans le volet gauche, sélectionnez **Clusters**. Pour le cluster que vous voulez arrêter, déplacez le curseur sur les points de suspension dans la colonne **Actions**, puis sélectionnez l’icône **Arrêter**.

![Arrêter un cluster Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Arrêter un cluster Databricks")

Si vous n’arrêtez pas le cluster manuellement, il s’arrêtera automatiquement, à condition d’avoir coché **Arrêter après __ minutes d’inactivité** lors de la création du cluster. Dans ce cas, le cluster s’arrête automatiquement s’il a été inactif pendant la période renseignée.

## <a name="next-steps"></a>Étapes suivantes 
Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Créer un espace de travail Azure Databricks
> * Créer un cluster Spark dans Azure Databricks
> * Créer un compte Azure Data Lake Store
> * Charger des données dans Azure Data Lake Store
> * Créer un notebook dans Azure Databricks
> * Extraire des données de Data Lake Store
> * Transformer des données dans Azure Databricks
> * Chargement de données dans Azure SQL Data Warehouse

Passez au didacticiel suivant pour en savoir plus sur la diffusion en continu des données en temps réel dans Azure Databricks à l’aide d’Azure Event Hubs.

> [!div class="nextstepaction"]
>[Diffuser en continu des données dans Azure Databricks à l’aide d’Event Hubs](databricks-stream-from-eventhubs.md)
