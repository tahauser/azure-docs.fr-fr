---
title: Prise en main de R Server sur HDInsight - Azure | Microsoft Docs
description: Apprenez à créer un Apache Spark sur un cluster HDInsight incluant R Server, puis à envoyer un script R sur le cluster.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: f4265ce7370542d8253222a5e268ea9cde7fd36e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-r-server-cluster-on-azure-hdinsight"></a>Prise en main du cluster R Server sur Azure HDInsight

Azure HDInsight inclut une option R Server à intégrer dans votre cluster HDInsight. Cette option permet aux scripts R d’utiliser Spark et MapReduce afin d’exécuter des calculs distribués. Dans cet article, vous allez découvrir comment créer un R Server sur le cluster HDInsight. Vous apprendrez également à exécuter un script R qui illustre l’utilisation de Spark pour effectuer des calculs R distribués.


## <a name="prerequisites"></a>Prérequis


* **Abonnement Azure** : avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Pour plus d’informations, consultez l’article [Obtenir une version d’évaluation gratuite de Microsoft Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Client Secure Shell (SSH)** : un client SSH est utilisé pour se connecter à distance au cluster HDInsight et exécuter des commandes directement sur celui-ci. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Créer le cluster à l’aide du portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Cliquez sur **Créer une ressource** > **Données + Analytique** > **HDInsight**.

3. À partir du panneau **Informations de base**, entrez les informations suivantes :

    * **Nom du cluster** : nom du cluster HDInsight.
    * **Abonnement** : sélectionnez l'abonnement souhaité.
    * **Nom d’utilisateur de connexion du cluster** et **Mot de passe de connexion du cluster** : les informations de connexion lors de l’accès au cluster sur HTTPS. Vous utilisez ces informations d’identification pour accéder aux services tels que l’interface utilisateur Ambari Web ou l’API REST.
    * **Nom d’utilisateur Secure Shell (SSH)** : information de connexion utilisée lors de l’accès au cluster sur SSH. Par défaut, le mot de passe est le même que le mot de passe de connexion de cluster.
    * **Groupe de ressources** : groupe de ressources dans lequel créer le cluster.
    * **Emplacement** : la région Azure dans laquelle créer le cluster.

        ![Détails de base du cluster](./media/r-server-get-started/clustername.png)

4. Sélectionnez le **Type de cluster**, puis définissez les valeurs suivantes dans la section **Configuration du cluster** :

    * **Type de cluster** : R Server

    * **Système d’exploitation** : Linux

    * **Version** : R Server 9.1 (HDI 3.6). Les notes de publication pour chacune des versions disponibles de R Server se trouvent sur [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).

    * **R Studio Community Edition pour R Server** : cet IDE basé sur navigateur est installé par défaut sur le nœud de périphérie. Si vous préférez ne pas l’installer, décochez la case. Si vous choisissez de l’installer, l’URL d’accès à la connexion à RStudio Server se trouve sur le panneau d’une application du portail de votre cluster une fois qu’il a été créé.

        ![Détails de base du cluster](./media/r-server-get-started/clustertypeconfig.png)

4. Après avoir sélectionné le type de cluster, utilisez le bouton __Sélectionner__ pour définir le type de cluster. Ensuite, utilisez le bouton __Suivant__ bouton pour terminer la configuration de base.

5. À partir de la section **Stockage**, sélectionnez ou créez un compte de stockage. Pour les étapes de ce document, laissez les autres champs de cette section sur leurs valeurs par défaut. Utilisez le bouton __Suivant__ pour enregistrer la configuration de stockage.

    ![Définir les paramètres de compte de stockage pour HDInsight](./media/r-server-get-started/cluster-storage.png)

6. Dans la section **Résumé**, passez en revue la configuration du cluster. Utilisez les liens __Modifier__ pour modifier les éventuels paramètres incorrects. Pour finir, cliquez sur le bouton __Créer__ pour créer le cluster.

    ![Définir les paramètres de compte de stockage pour HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > La création du cluster peut prendre jusqu’à 20 minutes.

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Se connecter à RStudio Server

Si vous avez choisi d’installer RStudio Server Community Edition pour votre cluster HDInsight, vous pouvez accéder à la connexion RStudio à l’aide d’une des deux méthodes suivantes :

* **Option 1** : accédez à l’URL suivante (où **CLUSTERNAME** est le nom du cluster R Server que vous avez créé) :

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Option 2** : ouvrez le cluster R Server dans le portail Azure, sous **Liens rapides** cliquez sur **Tableaux de bord R Server**.

     ![Définir les paramètres de compte de stockage pour HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    Dans **Tableaux de bord du cluster**, cliquez sur **R Studio Server**.

    ![Définir les paramètres de compte de stockage pour HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > Quelle que soit la méthode que vous utilisez, vous devez vous authentifier deux fois lorsque vous ouvrez une session pour la première fois.  Pour la première authentification, fournissez *l’ID d’utilisateur administrateur du cluster* et le *mot de passe*. Pour la deuxième authentification, fournissez *l’ID d’utilisateur SSH* et le *mot de passe*. Les connexions suivantes ne nécessitent que les informations d’identification SSH.

Une fois que vous êtes connecté, votre écran doit ressembler à la capture d’écran suivante :

![Se connecter à RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## <a name="run-a-sample-job"></a>Exécuter un exemple de travail

Vous pouvez soumettre un travail à l’aide des fonctions ScaleR. Voici un exemple des commandes utilisées pour exécuter un travail :

    # Set the HDFS (WASB) location of example data.
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storaging data temporarily.
    source <- "/tmp/AirOnTimeCSV2012"
    dir.create(source)

    # Download data to the tmp folder.
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

    # Set directory in bigDataDirRoot to load the data.
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory.
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input.
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (WASB) file system.
    hdfsFS <- RxHdfsFileSystem()

    # Create info list for the airline data.
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names.
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS.
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in local system.
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use.
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

    # Define the Spark compute context.
    mySparkCluster <- RxSpark()

    # Set the compute context.
    rxSetComputeContext(mySparkCluster)

    # Run a logistic regression.
    system.time(
        modelSpark <- rxLogit(formula, data = airOnTimeData)
    )

    # Display a summary.
    summary(modelSpark)

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-cluster-edge-node"></a>Se connecter au nœud de périphérie du cluster

Dans cette section, vous allez apprendre à vous connecter au nœud de périphérie d’un cluster HDInsight R Server à l’aide de SSH. Pour plus d’informations sur l’utilisation de SSH, consultez [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

La commande SSH pour se connecter au nœud de périphérie du cluster R Server est :

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

Pour trouver la commande SSH pour votre cluster, dans le portail Azure, cliquez sur le nom du cluster, puis sur **SSH + Connexion du cluster**, et pour **Nom d’hôte**, sélectionnez le nœud de périphérie. Cela a pour effet d’afficher les informations relatives au point de terminaison SSH pour le nœud de périmètre.

![Image du point de terminaison SSH pour le nœud de périmètre](./media/r-server-get-started/sshendpoint.png)

Si vous utilisez un mot de passe pour sécuriser votre compte utilisateur SSH, vous serez invité à le saisir. Si vous utilisez une clé publique, vous devrez peut-être utiliser le paramètre `-i` pour spécifier la clé privée correspondante. Par exemple : 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Une fois connecté, une invite semblable à la suivante s’affiche :

    sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## <a name="use-the-r-server-console"></a>Utiliser la console R Server

1. Dans la session SSH, utilisez la commande suivante pour démarrer la console R :  

        R

2. Vous devez voir une sortie avec la version de R Server, en plus d’autres informations.
    
3. Vous pouvez entrer un code R dans l’invite `>` . R Server sur HDInsight inclut des packages qui vous permettent d’interagir facilement avec Hadoop et d’exécuter des calculs distribués. Par exemple, utilisez la commande suivante pour afficher la racine du système de fichiers par défaut du cluster HDInsight :

        rxHadoopListFiles("/")

4. Vous pouvez également utiliser l’adressage de style WASB.

        rxHadoopListFiles("wasb:///")

5. Pour quitter la console R, utilisez la commande suivante :

        quit()

## <a name="automated-cluster-creation"></a>Création de cluster automatisée

Vous pouvez automatiser la création d’un cluster R Server pour HDInsight à l’aide de modèles Azure Resource Manager, du Kit de développement logiciel (SDK) et de PowerShell.

* Pour créer un cluster R Server à l’aide d’un modèle Gestion des ressources Azure, consultez [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Déployer un cluster HDInsight R Server).
* Pour créer un cluster R Server à l’aide du Kit de développement logiciel (SDK) .NET, consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Pour créer un cluster R Server à l’aide de PowerShell, consultez l’article [Créer des clusters basés sur Linux dans HDInsight à l’aide d’Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## <a name="delete-the-cluster"></a>Suppression du cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](../hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer un cluster R Server dans Azure HDInsight et les principes fondamentaux de l’utilisation de la console R à partir d’une session SSH. Les articles suivants expliquent les autres méthodes de gestion et d’utilisation de R Server sur HDInsight :

* [Envoyer des travaux depuis Outils R pour Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Manage R Server cluster on HDInsight](r-server-hdinsight-manage.md) (Gérer un cluster R Server sur HDInsight)
* [Operationalize R Server cluster on HDInsight](r-server-operationalize.md) (Faire fonctionner un cluster R Server sur HDInsight)
* [Options de contexte de calcul pour R Server sur HDInsight](r-server-compute-contexts.md)
* [Solutions de stockage Azure pour R Server sur HDInsight](r-server-storage.md)
