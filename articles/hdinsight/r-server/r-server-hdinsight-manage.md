---
title: Gérer un cluster R Server sur HDInsight - Azure | Microsoft Docs
description: Découvrez comment gérer un cluster R Server dans Azure HDInsight.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: c0a996555e35a99a6025e92bcb41fa192b18eece
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="manage-r-server-cluster-on-azure-hdinsight"></a>Gérer un cluster R Server sur Azure HDInsight

Dans cet article, vous découvrirez comment gérer un cluster R Server existant sur Azure HDInsight pour accomplir des tâches telles que l’ajout de plusieurs utilisateurs simultanés, la connexion à distance à un serveur ou client R (Microsoft ML), ou encore la modification du contexte de calcul.

## <a name="prerequisites"></a>Prérequis


* **Un cluster R Server sur HDInsight** : pour connaître la marche à suivre pour sa création, consultez [Prise en main de R Server sur HDInsight](r-server-get-started.md).

* **Client Secure Shell (SSH)** : un client SSH est utilisé pour se connecter à distance au cluster HDInsight et exécuter des commandes directement sur celui-ci. Pour en savoir plus, consultez [Utiliser SSH avec HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="enable-multiple-concurrent-users"></a>Autoriser plusieurs utilisateurs simultanés

Vous pouvez autoriser plusieurs utilisateurs simultanés pour le cluster R Server sur HDInsight en les ajoutant au nœud de périphérie sur lequel la version de RStudio Community s’exécute. Lorsque vous créez un cluster HDInsight, vous devez fournir deux utilisateurs, un utilisateur HTTP et un utilisateur SSH :

![Utilisateur simultané n° 1](./media/r-server-hdinsight-manage/concurrent-users-1.png)

- **Nom d’utilisateur de connexion du cluster** : un utilisateur HTTP pour l’authentification via la passerelle HDInsight qui est utilisée pour protéger les clusters HDInsight que vous avez créés. Cet utilisateur HTTP est utilisé pour accéder à l’IU Ambari, l’IU YARN, ainsi qu’à d’autres composants d’interface utilisateur.
- **Nom d’utilisateur Secure Shell (SSH)** : un utilisateur SSH pour l’accès au cluster via Secure Shell. Ce dernier correspond à un utilisateur dans le système Linux pour tous les nœuds principaux, les nœuds Worker et les nœuds de périmètre. Par conséquent, vous pouvez utiliser Secure Shell pour accéder à n’importe quel nœud dans un cluster distant.

La version de RStudio Server Community utilisée dans le cluster R Server sur HDInsight n’accepte comme mécanisme de connexion que les noms d’utilisateur et mots de passe Linux. Elle ne prend pas en charge le passage de jetons. Par conséquent, quand vous essayez d’accéder à R Studio pour la première fois sur un cluster R Server, vous devez vous connecter deux fois.

- Connectez-vous d’abord à l’aide des informations d’identification de l’utilisateur HTTP via la passerelle HDInsight. 

- Ensuite, utilisez les informations d’identification de l’utilisateur SSH pour vous connecter à RStudio.
  
Actuellement, on ne peut créer qu’un seul compte d’utilisateur SSH lors de l’approvisionnement d’un cluster HDInsight. Par conséquent, pour permettre à plusieurs utilisateurs d’accéder au cluster R Server sur HDInsight, vous devez créer des utilisateurs supplémentaires dans le système Linux.

Comme RStudio s’exécute sur le nœud de périphérie du cluster, il y a plusieurs étapes à suivre ici :

1. Utiliser l’utilisateur SSH créé pour se connecter au nœud de périphérie
2. Ajouter d’autres utilisateurs Linux dans le nœud de périmètre
3. Utiliser la version RStudio Community avec l’utilisateur créé

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Étape n° 1 : Utiliser l’utilisateur SSH créé pour ouvrir une session sur le nœud de périmètre

Suivez les instructions de l’article [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) afin d’accéder au nœud de périphérie. L’adresse du nœud de périphérie pour un cluster R Server sur HDInsight est `CLUSTERNAME-ed-ssh.azurehdinsight.net`.

### <a name="step-2-add-more-linux-users-in-edge-node"></a>Étape n° 2 : Ajouter d’autres utilisateurs Linux dans le nœud de périmètre

Pour ajouter un utilisateur au nœud de périmètre, exécutez les commandes suivantes :

    # Add a user 
    sudo useradd <yournewusername> -m

    # Set password for the new user 
    sudo passwd <yournewusername>

La capture d’écran qui suit présente les résultats.

![Utilisateur simultané 3](./media/r-server-hdinsight-manage/concurrent-users-2.png)

Ignorez le message vous invitant à saisir le « mot de passe Kerberos actuel » en appuyant simplement sur la touche **Entrée**. L’option `-m` de la commande `useradd` indique que le système va créer un dossier de base pour l’utilisateur, requis pour RStudio Community.

### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Étape n° 3 : Utiliser la version RStudio Community avec l’utilisateur créé

Accédez à RStudio à partir de https://CLUSTERNAME.azurehdinsight.net/rstudio/. Si vous vous connectez pour la première fois après avoir créé le cluster, entrez les informations d’identification de l’administrateur du cluster, suivies des informations d’identification de l’utilisateur SSH que vous venez de créer. S’il ne s’agit pas de votre première connexion, entrez uniquement les informations d’identification de l’utilisateur SSH que vous avez créé.

Grâce aux informations d’identification d’origine (par défaut, *sshuser*), vous pouvez aussi ouvrir une session simultanément à partir d’une autre fenêtre du navigateur.

Notez également que les utilisateurs récemment ajoutés ne possèdent pas les privilèges racine dans le système Linux. Cependant, ils bénéficient du même accès à l’ensemble des fichiers dans les stockages distants HDFS et WASB.

## <a name="connect-remotely-to-microsoft-ml-server-or-client"></a>Se connecter à distance à un serveur ou client Microsoft ML

Vous pouvez également configurer l’accès au contexte de calcul HDInsight Hadoop Spark à partir d’une instance distante de Microsoft ML Server ou Microsoft ML Client en cours d’exécution sur votre ordinateur de bureau. Pour ce faire, vous devez spécifier les options hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches et sshProfileScript quand vous définissez le contexte de calcul RxSpark sur votre ordinateur de bureau. Par exemple :

    myNameNode <- "default"
    myPort <- 0

    mySshHostname  <- 'rkrrehdi1-ed-ssh.azurehdinsight.net'  # HDI secure shell hostname
    mySshUsername  <- 'remoteuser'# HDI SSH username
    mySshSwitches  <- '-i /cygdrive/c/Data/R/davec'   # HDI SSH private key

    myhdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep="/")
    myShareDir <- paste("/var/RevoShare" , mySshUsername, sep="/")

    mySparkCluster <- RxSpark(
      hdfsShareDir = myhdfsShareDir,
      shareDir     = myShareDir,
      sshUsername  = mySshUsername,
      sshHostname  = mySshHostname,
      sshSwitches  = mySshSwitches,
      sshProfileScript = '/etc/profile',
      nameNode     = myNameNode,
      port         = myPort,
      consoleOutput= TRUE
    )

Pour plus d’informations, consultez la section « Using Microsoft R Server as a Hadoop Client » (Utilisation de Microsoft R Server comme client Hadoop) de l’article [Creating a Compute Context for Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios) (Créer un contexte de calcul pour Spark).

## <a name="use-a-compute-context"></a>Utiliser un contexte de calcul

Un contexte de calcul vous permet de contrôler si le calcul doit être effectué localement sur le nœud de périmètre, ou s’il doit être distribué entre les nœuds du cluster HDInsight.

1. Dans RStudio Server ou la console R (dans une session SSH), utilisez le code suivant pour charger les exemples de données dans le stockage par défaut pour HDInsight :

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
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

        # Set directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Ensuite, créez des données et définissez deux sources de données exploitables.

        # Define the HDFS (WASB) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in hdfs
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Exécutez une régression logistique sur les données à l’aide du contexte de calcul local.

        # Set a local compute context
        rxSetComputeContext("local")

        # Run a logistic regression
        system.time(
           modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
        )

        # Display a summary
        summary(modelLocal)

    Vous devez voir la sortie qui se termine par des lignes similaires à ce qui suit :

        Data: airOnTimeDataLocal (RxTextData Data Source)
        File name: /tmp/AirOnTimeCSV2012
        Dependent variable(s): ARR_DEL15
        Total independent variables: 634 (Including number dropped: 3)
        Number of valid observations: 6005381
        Number of missing observations: 91381
        -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)

        Coefficients:
                         Estimate Std. Error z value Pr(>|z|)
         (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
         ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
         ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
         ......
         DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
         DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
         DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
         DEST=BPT         Dropped    Dropped Dropped  Dropped

         ---

         Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

         Condition number of final variance-covariance matrix: 11904202
         Number of iterations: 7

4. Exécutez la même régression logistique à l’aide du contexte Spark. Le contexte Spark répartit le traitement sur tous les nœuds de travail dans le cluster HDInsight.

        # Define the Spark compute context
        mySparkCluster <- RxSpark()

        # Set the compute context
        rxSetComputeContext(mySparkCluster)

        # Run a logistic regression
        system.time(  
           modelSpark <- rxLogit(formula, data = airOnTimeData)
        )
        
        # Display a summary
        summary(modelSpark)


   > [!NOTE]
   > Vous pouvez également utiliser MapReduce pour répartir le calcul sur des nœuds de cluster. Pour plus d’informations sur le contexte de calcul, consultez [Options de contexte de calcul pour R Server sur HDInsight](r-server-compute-contexts.md).


## <a name="distribute-r-code-to-multiple-nodes"></a>Distribuer le code R à plusieurs nœuds

Avec R Server sur HDInsight, vous pouvez utiliser du code R existant pour l’exécuter sur plusieurs nœuds du cluster à l’aide de la commande `rxExec`. Cette fonction est utile lors d’un balayage paramétrique ou lorsque vous effectuez des simulations. Le code suivant montre comment utiliser `rxExec` :

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Si vous utilisez toujours le contexte Spark ou MapReduce, cette commande renvoie à la valeur nodename (nom de nœud) des nœuds Worker où le code `(Sys.info()["nodename"])` s’exécute. Par exemple, sur un cluster à quatre nœuds, vous vous attendez à recevoir une sortie similaire à l’exemple suivant :

    $rxElem1
        nodename
    "wn3-myrser"

    $rxElem2
        nodename
    "wn0-myrser"

    $rxElem3
        nodename
    "wn3-myrser"

    $rxElem4
        nodename
    "wn3-myrser"


## <a name="access-data-in-hive-and-parquet"></a>Accès aux données dans Hive et Parquet

Une nouvelle fonctionnalité disponible dans R Server 9.1 permet un accès direct aux données de Hive et Parquet pour une utilisation par les fonctions de ScaleR dans le contexte de calcul Spark. Ces fonctionnalités sont disponibles via les nouvelles fonctions de source de données ScaleR appelées RxHiveData et RxParquetData qui fonctionnent à l’aide de Spark SQL pour charger les données directement dans un tableau de données Spark pour analyse par ScaleR.  

Le code suivant offre quelques exemples de code relatifs à l’utilisation des nouvelles fonctions :

    #Create a Spark compute context:
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model:
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model:
    rxHadoopMakeDir('/share')
    rxHadoopCopyFromLocal(file.path(rxGetOption('sampleDataDir'), 'claimsParquet/'), '/share/')
    pqData <- RxParquetData('/share/claimsParquet',
                     colInfo = list(
                age    = list(type = "factor"),
               car.age = list(type = "factor"),
                  type = list(type = "factor")
             ) )
    rxGetInfo(pqData, getVarInfo = TRUE)

    rxNaiveBayes(type ~ age + cost, data = pqData)

    #Check on Spark data objects, cleanup, and close the Spark session:
    lsObj <- rxSparkListData() # two data objs are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() # it should show empty list
    rxSparkDisconnect(myHadoopCluster)


Pour plus d’informations sur l’utilisation de ces nouvelles fonctions, consultez l’aide en ligne dans ML Server via les commandes `?RxHivedata` et `?RxParquetData`.  

## <a name="install-additional-r-packages-on-the-cluster"></a>Installer des packages R supplémentaires sur le cluster

### <a name="to-install-r-packages-on-the-edge-node"></a>Pour installer des packages R sur le nœud de périphérie

Si vous souhaitez installer des packages R supplémentaires sur le nœud de périphérie, vous pouvez utiliser `install.packages()` directement à partir de la console R une fois que vous vous êtes connecté au nœud de périphérie par le biais de SSH. 

### <a name="to-install-r-packages-on-the-worker-node"></a>Pour installer des packages R sur le nœud Worker

Pour installer des packages R sur les nœuds Worker du cluster, vous devez utiliser une action de script. Les actions de script sont des scripts de commandes par lot permettant de modifier la configuration du cluster HDInsight ou d’installer d’autres logiciels, comme des packages R supplémentaires. 

> [!IMPORTANT]
> L’utilisation d’actions de script pour installer des packages R supplémentaires n’est possible qu’après la création du cluster. N’utilisez pas cette procédure lors de la création du cluster, car le script a besoin que R Server soit entièrement installé et configuré.
>
>

1. Suivez les étapes fournies dans [Personnaliser des clusters à l’aide d’une action de script](../hdinsight-hadoop-customize-cluster-linux.md).

3. Pour **Envoyer une action de script**, entrez les informations suivantes :

   * Pour **Type de script**, sélectionnez **Personnalisé**.

   * Dans le champ **Nom**, renseignez un nom pour l’action de script.

    * Pour **URI de script bash**, entrez `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`. Il s’agit du script permettant d’installer les packages R supplémentaires sur le nœud Worker.

   * Sélectionnez uniquement la case à cocher en regard de **Worker**.

   * **Paramètres** : les packages R à installer. Par exemple, `bitops stringr arules`

   * Sélectionnez la case à cocher en regard de **Conservez cette action de script**.  

   > [!NOTE]
   > 1. Par défaut, tous les packages R sont installés à partir d’un instantané du référentiel Microsoft MRAN cohérent avec la version du serveur R qui a été installée. Si vous souhaitez installer des versions plus récentes des packages, vous risquez de rencontrer des problèmes de compatibilité. Toutefois, vous pouvez effectuer ce type d’installation en spécifiant `useCRAN` comme premier élément de la liste des packages, par exemple `useCRAN bitops, stringr, arules`.  
   > 2. Certains packages R nécessitent des bibliothèques de système Linux supplémentaires. Pour plus de commodité, nous avons préinstallé les dépendances requises par les 100 premiers packages R les plus populaires. Toutefois, si les packages R que vous installez nécessitent des bibliothèques supplémentaires, vous devez télécharger le script de base utilisé ici et ajouter des étapes pour installer les bibliothèques système. Vous devez ensuite charger le script modifié dans un conteneur d’objets blob publics dans Azure Storage, puis utiliser le script modifié pour installer les packages.
   >    Pour plus d’informations sur le développement d’actions de script, consultez la section [Développer des actions de script](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Ajout d’une action de script](./media/r-server-hdinsight-manage/submitscriptaction.png)

4. Sélectionnez **Créer** pour exécuter le script. Une fois le script exécuté, les packages R sont disponibles sur tous les nœuds de travail.

## <a name="next-steps"></a>Étapes suivantes

* [Opérationnaliser un cluster R Server sur HDInsight](r-server-operationalize.md)
* [Options de contexte de calcul pour R Server sur HDInsight](r-server-compute-contexts.md)
* [Solutions de stockage Azure pour R Server sur HDInsight](r-server-storage.md)
