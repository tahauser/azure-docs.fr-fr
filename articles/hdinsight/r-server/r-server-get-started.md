---
title: "Prise en main de R Server sur HDInsight - Azure | Microsoft Docs"
description: "Apprenez à créer un Apache Spark sur un cluster HDInsight incluant R Server, puis à envoyer un script R sur le cluster."
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/14/2017
ms.author: bradsev
ms.openlocfilehash: aa7f2e6f44036738756391ecaa265c57c093c42c
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="get-started-with-r-server-on-hdinsight"></a>Prise en main de R Server sur HDInsight

Azure HDInsight inclut une option R Server à intégrer dans votre cluster HDInsight. Cette option permet aux scripts R d’utiliser Spark et MapReduce afin d’exécuter des calculs distribués. Dans cet article, vous allez découvrir comment créer un R Server sur le cluster HDInsight. Vous apprendrez également à exécuter un script R qui illustre l’utilisation de Spark pour effectuer des calculs R distribués.


## <a name="prerequisites"></a>configuration requise

* **Abonnement Azure** : avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Pour plus d’informations, consultez l’article [Obtenir une version d’évaluation gratuite de Microsoft Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Client Secure Shell (SSH)** : un client SSH est utilisé pour se connecter à distance au cluster HDInsight et exécuter des commandes directement sur celui-ci. Pour en savoir plus, voir [Utilisation de SSH avec Hadoop Linux sur HDInsight depuis Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).
* **Clés SSH (facultatives)** : vous pouvez sécuriser le compte SSH utilisé pour la connexion au cluster à l’aide d’un mot de passe ou d’une clé publique. Le recours à un mot de passe est plus facile et vous permet de commencer sans avoir à créer une paire de clés publique/privée. Toutefois, il est plus sûr d’utiliser une clé.

  > [!NOTE]
  > Les étapes décrites dans cet article partent du principe que vous utilisez un mot de passe.


## <a name="automate-cluster-creation"></a>Automatiser la création de cluster

Vous pouvez automatiser la création d’instances HDInsight R Server à l’aide de modèles Azure Resource Manager, du Kit de développement logiciel (SDK) et de PowerShell.

* Pour créer une instance R Server à l’aide d’un modèle Azure Resource Manager, consultez la rubrique [Déployer un cluster HDInsight R Server](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).
* Pour créer une instance R Server à l’aide du Kit de développement logiciel (SDK) .NET, consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Pour déployer R Server à l’aide de PowerShell, consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide d’Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-a-cluster-by-using-the-azure-portal"></a>Créer un cluster à l’aide du portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

2. Sélectionnez **Nouveau** > **Intelligence et analyse** > **HDInsight**.

    ![Image de la création d’un cluster](./media/r-server-get-started/newcluster.png)

3. Dans le champ **Nom du cluster** de l’expérience **Création rapide**, entrez un nom pour le cluster. Si vous avez plusieurs abonnements Azure, utilisez la zone **Abonnement** pour sélectionner celui que vous voulez utiliser.

    ![Sélections de nom et d’abonnement de cluster](./media/r-server-get-started/clustername.png)

4. Sélectionnez **Type de cluster** pour ouvrir le volet **Configuration de cluster**. Dans le volet **Configuration de cluster**, sélectionnez les options suivantes :

    * **Type de cluster** : sélectionnez **R Server**.
    * **Version** : sélectionnez la version de R Server à installer sur le cluster. La version actuellement disponible est **R Server 9.1 (HDI 3.6)**. Les notes de publication pour chacune des versions disponibles de R Server se trouvent sur [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).
    * **R Studio Community Edition pour R Server** : cet IDE basé sur navigateur est installé par défaut sur le nœud de périphérie. Si vous préférez ne pas l’installer, décochez la case. Si vous choisissez de l’installer, vous trouverez l’URL d’accès à la connexion à RStudio Server dans le panneau d’une application du portail de votre cluster une fois qu’il a été créé.
    * Conservez les valeurs par défaut des autres options et utilisez le bouton **Sélectionner** pour enregistrer le type de cluster.

        ![Capture d’écran du volet « Type de cluster »](./media/r-server-get-started/clustertypeconfig.png)

5. Dans les champs **Nom d’utilisateur de connexion du cluster** et **Mot de passe de connexion du cluster** du volet **Paramètres de base**, entrez un nom d’utilisateur et un mot de passe (respectivement) pour le cluster.

6. Dans la zone **Nom d’utilisateur SSH (Secure Shell)**, spécifiez le nom d’utilisateur SSH. SSH est utilisé pour se connecter à distance au cluster à l’aide d’un client SSH. Vous pouvez spécifier l’utilisateur SSH dans cette zone ou après la création du cluster (dans l’onglet **Configuration** du cluster).
   
   > [!NOTE] 
   > R Server est configuré pour attendre « remoteuser » comme nom d’utilisateur SSH. Si vous utilisez un autre nom d’utilisateur, vous devrez effectuer une étape supplémentaire après la création du cluster.

7. Laissez la case cochée pour **Utiliser le même mot de passe que pour la connexion au cluster** pour utiliser **MOT DE PASSE** comme type d’authentification, sauf si vous préférez utiliser une clé publique. Vous aurez besoin d’une paire de clés publique/privée si vous souhaitez accéder au R Server sur le cluster via un client distant (par exemple, Outils R pour Visual Studio, RStudio ou un autre IDE de bureau). Vous devez choisir un mot de passe SSH si vous installez RStudio Server Community Edition.     

   Pour créer et utiliser une paire de clés publique/privée, désactivez **Utiliser le même mot de passe que pour la connexion au cluster**. Puis sélectionnez **CLÉ PUBLIQUE** et procédez comme suit. Ces instructions supposent que Cygwin avec ssh-keygen ou équivalent est installé.

   a. Générez une paire de clés publique/privée à partir de l’invite de commandes sur votre ordinateur portable :

        ssh-keygen -t rsa -b 2048

   b. Suivez les instructions pour nommer un fichier de clé, puis entrez un mot de passe pour renforcer la sécurité. Votre écran doit ressembler à ceci :

      ![Ligne de commande SSH dans Windows](./media/r-server-get-started/sshcmdline.png)

      Cette commande crée un fichier de clé privée et un fichier de clé publique sous le nom <private-key-filename>.pub. Dans cet exemple, les fichiers sont furiosa et furiosa.pub :

      ![Exemple de résultats de la commande dir](./media/r-server-get-started/dir.png)

   c. Spécifiez le fichier de clé publique (&#42;.pub) lors de l’attribution des informations d’identification du cluster HDI. Confirmez le groupe de ressources et la région, puis sélectionnez **Suivant**.

      ![Volet d’informations d’identification](./media/r-server-get-started/publickeyfile.png)  

   d. Modifiez les autorisations sur le fichier de clé privée sur votre ordinateur portable :

        chmod 600 <private-key-filename>

   e. Utilisez le fichier de clé privée avec SSH pour la connexion à distance :

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      Ou, utilisez le fichier de clé privée dans le cadre de la définition du contexte de calcul Hadoop Spark pour R Server sur le client. Pour plus d’informations, consultez [Créer un contexte de calcul pour Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark).

8. La création rapide vous fait passer au volet **Stockage**. Là, vous sélectionnez les paramètres de compte de stockage à utiliser pour l’emplacement principal du système de fichiers HDFS utilisé par le cluster. Sélectionnez un compte de stockage Azure nouveau ou existant, ou un compte Azure Data Lake Store existant.

    - Si vous sélectionnez un compte de stockage Azure, vous pouvez opter pour un compte existant en choisissant **Sélectionner un compte de stockage**, puis en spécifiant le compte en question. Créez un nouveau compte à l’aide du lien **Nouveau** dans la section **Sélectionner un compte de stockage**.

      > [!NOTE]
      > Si vous sélectionnez **Nouveau**, vous devez entrer un nom pour le nouveau compte de stockage. Une coche verte s’affiche si le nom est accepté.

      Le **conteneur par défaut** correspond au nom du cluster. Laissez cette valeur par défaut.

      Si vous avez sélectionné un nouveau compte de stockage, utilisez **Emplacement** dans l’invite pour lui choisir une région.  

         ![Volet de source de données](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > La sélection de l’emplacement de la source de données par défaut définit également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même région.

    - Si vous souhaitez utiliser un compte Data Lake Store existant, sélectionnez le compte à utiliser. Puis ajoutez l’identité *ADD* de cluster à votre cluster pour autoriser l’accès au magasin. Pour plus d’informations sur ce processus, consultez [Créer un cluster HDInsight avec Data Lake Store à l’aide du Portail Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

    Pour enregistrer la configuration de la source de données, utilisez le bouton **Sélectionner** .


9. Le volet **Résumé** s’affiche alors, dans lequel vous pourrez valider tous vos paramètres. Ici, vous pouvez modifier la taille de votre cluster pour changer le nombre de serveurs de votre cluster. Vous pouvez également spécifier les actions de script que vous souhaitez exécuter. Sauf si vous savez que vous avez besoin d’un cluster plus grand, conservez le nombre de nœuds Worker par défaut (**4**). Le volet affiche également le coût estimé du cluster.

    ![Résumé du cluster](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Si nécessaire, vous pouvez redimensionner votre cluster ultérieurement via le portail (**Cluster** > **Paramètres** > **Mise à l’échelle du cluster**) pour augmenter ou diminuer le nombre de nœuds Worker. Ce redimensionnement peut être utile pour faire fonctionner le cluster au ralenti lorsqu’il n’est pas utilisé, ou pour ajouter de la capacité afin de répondre aux besoins des tâches plus intensives.
   >
   >

   Certains facteurs à prendre en compte lors du dimensionnement de votre cluster, les nœuds de données et le nœud de périphérie sont les suivants :  

   * Les performances des analyses du R Server distribué sur Spark sont proportionnelles au nombre de nœuds Worker lorsque les données sont volumineuses.  

   * Les performances des analyses du R Server sont linéaires quant à la taille des données analysées. Par exemple :   

     * Pour les volumes de données petits à moyens, les performances sont idéales lorsque les données sont analysées dans un contexte de calcul local sur le nœud de périphérie. Pour plus d’informations sur les scénarios de fonctionnement des contextes de calcul locaux et Spark, consultez [Options de contexte de calcul pour R Server sur HDInsight](r-server-compute-contexts.md).<br>
     * Si vous vous connectez au nœud de périphérie et exécutez votre script R, toutes les fonctions seront exécutées *localement* sur le nœud de périphérie, à l’exception des fonctions ScaleR rx. La mémoire et le nombre de cœurs du nœud de périphérie doivent donc être dimensionnés en conséquence. Ces conditions s’appliquent si vous utilisez R Server sur HDI comme contexte de calcul à distance à partir de votre ordinateur portable.

   Pour enregistrer la configuration de la tarification du nœud, utilisez le bouton **Sélectionner**.

   ![Volet des niveaux tarifaires des nœuds](./media/r-server-get-started/pricingtier.png)

   Il existe également un lien permettant de **télécharger le modèle et les paramètres**. Cliquer sur ce lien permet d’afficher les scripts qui peuvent être utilisés pour automatiser la création d’un cluster avec la configuration sélectionnée. Ces scripts sont également disponibles dans l’entrée du portail Azure de votre cluster, une fois celui-ci créé.

   > [!NOTE]
   > La création du cluster prend un certain temps (en règle générale, environ 20 minutes). Pour vérifier le processus de création, utilisez la vignette du tableau d’accueil ou l’entrée **Notifications** à gauche de la page.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Se connecter à RStudio Server

Si vous avez choisi d’inclure RStudio Server Community Edition dans votre installation, vous pouvez accéder à la connexion RStudio via deux méthodes :

- Accédez à l’URL suivante (où *CLUSTERNAME* est le nom du cluster que vous avez créé) :

    https://*CLUSTERNAME*.azurehdinsight.net/rstudio/

- Ouvrez l’entrée relative à votre cluster dans le portail Azure, sélectionnez le lien rapide **Tableaux de bord R Server**, puis choisissez le **tableau de bord RStudio** :

  ![Accès au tableau de bord RStudio](./media/r-server-get-started/rstudiodashboard1.png)

  ![Accès au tableau de bord RStudio](./media/r-server-get-started/rstudiodashboard2.png)

> [!IMPORTANT]
> Quelle que soit la méthode que vous utilisez, vous devez vous authentifier deux fois lorsque vous ouvrez une session pour la première fois. À la première authentification, fournissez l’ID d’utilisateur administrateur et le mot de passe pour le cluster. À la seconde invite, indiquez l’ID d’utilisateur et le mot de passe SSH. Les connexions suivantes ne requièrent que l’ID d’utilisateur et le mot de passe SSH.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Se connecter au nœud de périmètre R Server

Connectez-vous au nœud de périphérie R Server du cluster HDInsight à l’aide de SSH, en utilisant la commande suivante :

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> L’adresse `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` est disponible sur le Portail Azure. Sélectionnez votre cluster, puis sélectionnez **Tous les paramètres** > **Applications** > **RServer**. Cela permet d’afficher les informations relatives au point de terminaison SSH pour le nœud de périphérie.
>
> ![Image du point de terminaison SSH pour le nœud de périphérie](./media/r-server-get-started/sshendpoint.png)
>
>

Si vous utilisez un mot de passe pour sécuriser votre compte utilisateur SSH, vous serez invité à le saisir. Si vous utilisez une clé publique, vous devrez peut-être utiliser le paramètre `-i` pour spécifier la clé privée correspondante. Par exemple : 

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Pour en savoir plus, consultez la page [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Une fois connecté, une invite semblable à la suivante s’affiche :

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Autoriser plusieurs utilisateurs simultanés

Vous pouvez autoriser plusieurs utilisateurs simultanés en les ajoutant au nœud de périmètre sur lequel la version de RStudio Community s’exécute.

Lorsque vous créez un cluster HDInsight, vous devez fournir deux utilisateurs : un utilisateur HTTP et un utilisateur SSH.

![Entrer les informations d’identification pour l’utilisateur de cluster et l’utilisateur SSH](./media/r-server-get-started/concurrent-users-1.png)

- **Nom d’utilisateur de connexion du cluster** : un utilisateur HTTP pour l’authentification via la passerelle HDInsight qui est utilisée pour protéger les clusters HDInsight que vous avez créés. Cet utilisateur HTTP est utilisé pour accéder à l’IU Ambari, l’IU YARN, et à d’autres composants d’interface utilisateur.
- **Nom d’utilisateur Secure Shell (SSH)** : un utilisateur SSH pour l’accès au cluster via Secure Shell. Ce dernier correspond à un utilisateur dans le système Linux pour tous les nœuds principaux, les nœuds Worker et les nœuds de périmètre. Par conséquent, vous pouvez utiliser SSH pour accéder à n’importe quel nœud dans un cluster distant.

La version de RStudio Server Community utilisée dans les clusters de type Microsoft R Server sur HDInsight n’accepte comme mécanisme de connexion que les noms d’utilisateur et mots de passe Linux. Elle ne prend pas en charge le passage de jetons. Par conséquent, si vous avez créé un nouveau cluster et que vous souhaitez utiliser RStudio pour y accéder, vous devez ouvrir une session deux fois.

1. Commencez par ouvrir une session à l’aide des informations d’identification de l’utilisateur HTTP via la passerelle HDInsight : 

    ![Entrer les informations d’identification de l’utilisateur HTTP](./media/r-server-get-started/concurrent-users-2a.png)

2. Utilisez les informations d’identification de l’utilisateur SSH pour vous connecter à RStudio :
  
    ![Entrer les informations d’identification de l’utilisateur SSH](./media/r-server-get-started/concurrent-users-2b.png)

Actuellement, on ne peut créer qu’un seul compte d’utilisateur SSH lors de l’approvisionnement d’un cluster HDInsight. Pour permettre à plusieurs utilisateurs d’accéder aux clusters Microsoft R Server sur HDInsight, vous devez créer des utilisateurs supplémentaires dans le système Linux.

Comme la version RStudio Server Community est en cours d’exécution sur le nœud de périphérie du cluster, il y a plusieurs étapes à réaliser :

1. Utiliser l’utilisateur SSH créé pour ouvrir une session sur le nœud de périphérie.
2. Ajouter d’autres utilisateurs Linux au nœud de périphérie.
3. Utiliser la version RStudio Community avec l’utilisateur créé.

### <a name="use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Utiliser l’utilisateur SSH créé pour ouvrir une session sur le nœud de périmètre

Téléchargez n’importe quel outil SSH (tel que PuTTY) et utilisez l’utilisateur SSH existant afin d’ouvrir une session. Ensuite, suivez les instructions décrites à la page [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) afin d’accéder au nœud de périmètre. L’adresse du nœud de périphérie pour le cluster R Server sur HDInsight est la suivante : **clustername-ed-ssh.azurehdinsight.net**


### <a name="add-more-linux-users-to-the-edge-node"></a>Ajouter d’autres utilisateurs Linux au nœud de périphérie

Pour ajouter un utilisateur au nœud de périphérie, exécutez les commandes suivantes :

    sudo useradd yournewusername -m
    sudo passwd yourusername

Les éléments renvoyés doivent être les suivants : 

![Sortie des commandes de sudo](./media/r-server-get-started/concurrent-users-3.png)

Lorsque vous êtes invité à entrer le mot de passe Kerberos actuel, il vous suffit de sélectionner la touche Entrée pour l’ignorer. L’option `-m` de la commande `useradd` indique que le système va créer un dossier de base pour l’utilisateur. Ce dossier est requis pour la version RStudio Community.


### <a name="use-the-rstudio-community-version-with-the-created-user"></a>Utiliser la version RStudio Community avec l’utilisateur créé

Utilisez l’utilisateur créé pour vous connecter à RStudio :

![Page de connexion de RStudio](./media/r-server-get-started/concurrent-users-4.png)

Comme vous pouvez le voir, RStudio indique que vous utilisez le nouvel utilisateur (dans cet exemple, l’utilisateur **sshuser6**) pour ouvrir une session dans le cluster : 

![Emplacement du nouvel utilisateur sur la barre de commandes RStudio](./media/r-server-get-started/concurrent-users-5.png)

Grâce aux informations d’identification d’origine (par défaut, **sshuser**), vous pouvez aussi ouvrir une session simultanément à partir d’une autre fenêtre du navigateur.

Vous pouvez soumettre un travail à l’aide des fonctions ScaleR. Voici un exemple des commandes utilisées pour exécuter un travail :

    # Set the HDFS (Azure Blob storage) location of example data
    bigDataDirRoot <- "/example/data"

    # Create a local folder for storing data temporarily
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

    # Set the directory in bigDataDirRoot to load the data
    inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

    # Create the directory
    rxHadoopMakeDir(inputDir)

    # Copy the data from source to input
    rxHadoopCopyFromLocal(source, bigDataDirRoot)

    # Define the HDFS (Blob storage) file system
    hdfsFS <- RxHdfsFileSystem()

    # Create an info list for the airline data
    airlineColInfo <- list(
    DAY_OF_WEEK = list(type = "factor"),
    ORIGIN = list(type = "factor"),
    DEST = list(type = "factor"),
    DEP_TIME = list(type = "integer"),
    ARR_DEL15 = list(type = "logical"))

    # Get all the column names
    varNames <- names(airlineColInfo)

    # Define the text data source in HDFS
    airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

    # Define the text data source in the local system
    airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

    # Specify the formula to use
    formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

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


Notez que les travaux soumis se trouvent sous différents noms d’utilisateur dans l’IU YARN :

![Liste des utilisateurs dans l’interface utilisateur YARN](./media/r-server-get-started/concurrent-users-6.png)

Notez également que les nouveaux utilisateurs (ajoutés récemment) n’ont pas de privilèges root dans le système Linux. Mais ils ont le même accès à tous les fichiers dans le système de fichiers HDFS à distance et le stockage d’objets Blob.


<a name="use-r-console"></a>
## <a name="use-the-r-console"></a>Utiliser la console R

1. Dans la session SSH, utilisez la commande suivante pour démarrer la console R :  

        R

2. Le résultat ressemble à ce qui suit :
    
        R version 3.2.2 (2015-08-14) -- "Fire Safety"
        Copyright (C) 2015 The R Foundation for Statistical Computing
        Platform: x86_64-pc-linux-gnu (64-bit)

        R is free software and comes with ABSOLUTELY NO WARRANTY.
        You are welcome to redistribute it under certain conditions.
        Type 'license()' or 'licence()' for distribution details.

        Natural language support but running in an English locale

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

        Type 'readme()' for release notes.
        >

3. Vous pouvez entrer un code R dans l’invite `>` . R Server inclut des packages qui vous permettent d’interagir facilement avec Hadoop et d’exécuter des calculs distribués. Par exemple, utilisez la commande suivante pour afficher la racine du système de fichiers par défaut du cluster HDInsight :

        rxHadoopListFiles("/")

4. Vous pouvez également utiliser le style de stockage d’objets Blob d’adressage :

        rxHadoopListFiles("wasb:///")


## <a name="use-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>Utiliser R Server sur HDI à partir d’une instance distante de Microsoft R Server ou Microsoft R Client

Il est également possible de configurer l’accès au contexte de calcul HDI Hadoop Spark à partir d’une instance distante de Microsoft R Server ou Microsoft R Client en cours d’exécution sur un ordinateur de bureau ou portable. Pour plus d’informations, consultez la section « Utilisation de Microsoft R Server comme client Hadoop » de [Créer un contexte de calcul pour Spark](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-spark#more-spark-scenarios). Pour cela, spécifiez les options suivantes lorsque vous définissez le contexte de calcul RxSpark sur votre ordinateur portable : hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches et sshProfileScript. Voici un exemple :


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


## <a name="use-a-compute-context"></a>Utiliser un contexte de calcul

Un contexte de calcul vous permet de contrôler si le calcul doit être effectué localement sur le nœud de périphérie, ou s’il doit être distribué entre les nœuds du cluster HDInsight.

1. Dans RStudio Server ou la console R (dans une session SSH), utilisez le code suivant pour charger les exemples de données dans le stockage par défaut pour HDInsight :

        # Set the HDFS (Blob storage) location of example data
        bigDataDirRoot <- "/example/data"

        # Create a local folder for storing data temporarily
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

        # Set the directory in bigDataDirRoot to load the data into
        inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

        # Make the directory
        rxHadoopMakeDir(inputDir)

        # Copy the data from source to input
        rxHadoopCopyFromLocal(source, bigDataDirRoot)

2. Créez des données et définissez deux sources de données exploitables :

        # Define the HDFS (Blob storage) file system
        hdfsFS <- RxHdfsFileSystem()

        # Create an info list for the airline data
        airlineColInfo <- list(
             DAY_OF_WEEK = list(type = "factor"),
             ORIGIN = list(type = "factor"),
             DEST = list(type = "factor"),
             DEP_TIME = list(type = "integer"),
             ARR_DEL15 = list(type = "logical"))

        # Get all the column names
        varNames <- names(airlineColInfo)

        # Define the text data source in HDFS
        airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

        # Define the text data source in the local system
        airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

        # Formula to use
        formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

3. Exécutez une régression logistique sur les données à l’aide du contexte de calcul local :

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

4. Exécutez la même régression logistique en utilisant le contexte Spark. Le contexte Spark répartit le traitement sur tous les nœuds de travail dans le cluster HDInsight.

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

Avec R Server, vous pouvez facilement utiliser du code R existant pour l’exécuter sur plusieurs nœuds du cluster à l’aide de `rxExec`. Cette fonction est utile lors d’un balayage paramétrique ou lorsque vous effectuez des simulations. Le code suivant montre comment utiliser `rxExec` :

    rxExec( function() {Sys.info()["nodename"]}, timesToRun = 4 )

Si vous utilisez toujours les contextes Spark ou MapReduce, cette commande renvoie à la valeur `nodename` des nœuds Worker sur lesquels le code `(Sys.info()["nodename"])` s’exécute. Par exemple, sur un cluster à quatre nœuds, vous vous attendez à recevoir une sortie similaire ce qui suit :

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

Une nouvelle fonctionnalité disponible dans R Server 9.1 permet un accès direct aux données de Hive et Parquet pour une utilisation par les fonctions de ScaleR dans le contexte de calcul Spark. Ces fonctionnalités sont disponibles via les nouvelles fonctions de source de données ScaleR appelées RxHiveData et RxParquetData. Elles fonctionnent à l’aide de Spark SQL pour charger des données directement dans une tramedonnées Spark pour l’analyse par ScaleR.  

Le code suivant offre quelques exemples relatifs à l’utilisation des nouvelles fonctions :

    #Create a Spark compute context
    myHadoopCluster <- rxSparkConnect(reset = TRUE)

    #Retrieve some sample data from Hive and run a model
    hiveData <- RxHiveData("select * from hivesampletable",
                     colInfo = list(devicemake = list(type = "factor")))
    rxGetInfo(hiveData, getVarInfo = TRUE)

    rxLinMod(querydwelltime ~ devicemake, data=hiveData)

    #Retrieve some sample data from Parquet and run a model
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

    #Check on Spark data objects, clean up, and close the Spark session
    lsObj <- rxSparkListData() #Two data objects are cached
    lsObj
    rxSparkRemoveData(lsObj)
    rxSparkListData() #It should show an empty list
    rxSparkDisconnect(myHadoopCluster)


Pour plus d’informations sur ces nouvelles fonctions, consultez l’aide en ligne dans R Server en utilisant les commandes `?RxHivedata` et `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Installer des packages R supplémentaires sur le nœud de périmètre

Si vous souhaitez installer des packages R supplémentaires sur le nœud de périphérie, vous pouvez utiliser `install.packages()` directement à partir de la console R quand vous êtes connecté au nœud de périphérie par le biais de SSH. Toutefois, si vous avez besoin d’installer des packages R sur les nœuds Worker du cluster, vous devez utiliser une action de script.

Les actions de script sont des scripts Bash permettant de modifier la configuration du cluster HDInsight ou d’installer d’autres logiciels, comme des packages R supplémentaires. Pour installer des packages supplémentaires à l’aide d’une action de script, procédez comme suit :

> [!IMPORTANT]
> L’utilisation d’actions de script pour installer des packages R supplémentaires n’est possible qu’après la création du cluster. N’utilisez pas cette procédure lors de la création du cluster, car le script a besoin que R Server soit entièrement installé et configuré.
>
>

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre R Server sur le cluster HDInsight.

2. Dans le volet **Paramètres**, sélectionnez **Actions de script** > **Envoyer**.

   ![Image du volet Actions de script](./media/r-server-get-started/scriptaction.png)

3. Dans le volet **Envoyer une action de script**, entrez les informations suivantes :

   * **Nom** : un nom convivial pour identifier ce script.

   * **URI de script bash** : `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **En-tête** : cet élément doit être effacé.

   * **Worker** : cet élément doit être effacé.

   * **Zookeeper** : cet élément doit être effacé.

   * **Nœuds de périphérie** : cet élément doit être sélectionné.

   * **Paramètres** : les packages R à installer (par exemple, `bitops stringr arules`).

   * **Conservez cette action de script** : cet élément doit être sélectionné.  

   > [!NOTE]
   > Par défaut, tous les packages R sont installés à partir d’une capture instantanée du référentiel du réseau d’application Microsoft R compatible avec la version du serveur R qui a été installée. Si vous souhaitez installer des versions plus récentes des packages, vous risquez de rencontrer des problèmes de compatibilité. Toutefois, vous pouvez effectuer ce type d’installation en spécifiant `useCRAN` comme premier élément de la liste des packages (par exemple, `useCRAN bitops, stringr, arules`).  
   > 
   > Certains packages R nécessitent des bibliothèques de système Linux supplémentaires. Pour plus de commodité, nous avons préinstallé les dépendances requises par les 100 packages R les plus populaires. Si les packages R que vous installez nécessitent des bibliothèques supplémentaires, vous devez télécharger le script de base utilisé ici et ajouter des étapes pour installer les bibliothèques système. Vous devez ensuite charger le script modifié dans un conteneur d’objets blob publique dans le stockage Azure, puis utiliser le script modifié pour installer les packages.
   >
   > Pour plus d’informations sur le développement d’actions de script, consultez [Développer des actions de script](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Ajout d’une action de script](./media/r-server-get-started/submitscriptaction.png)

4. Sélectionnez **Créer** pour exécuter le script. Une fois le script exécuté, les packages R sont disponibles sur tous les nœuds Worker.


## <a name="configure-microsoft-r-server-operationalization"></a>Configurer l’opérationnalisation de Microsoft R Server

Lorsque votre modélisation des données est terminée, vous pouvez opérationnaliser le modèle pour effectuer des prévisions. Pour configurer l’opérationnalisation de Microsoft R Server, procédez comme suit :

1. Utilisez la commande `ssh` pour le nœud de périphérie--par exemple : 

       ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

2. Changez de répertoire pour la version adéquate et utilisez la commande `sudo dotnet` pour le fichier .dll. 

   Pour Microsoft R Server 9.1 :

       cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
       sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

   Pour Microsoft R Server 9.0 :

       cd /usr/lib64/microsoft-deployr/9.0.1
       sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Pour configurer l’opérationnalisation de Microsoft R Server avec une configuration complète, procédez comme suit :

   a. Sélectionnez `Configure R Server for Operationalization`.

   b. Sélectionnez `A. One-box (web + compute nodes)`.

   c. Entrez un mot de passe pour l’utilisateur `admin`.

   ![Opérationnalisation complète](./media/r-server-get-started/admin-util-one-box-.png)

4. Vous pouvez éventuellement exécuter un test de diagnostic, comme suit :

   a. Sélectionnez `6. Run diagnostic tests`.

   b. Sélectionnez `A. Test configuration`.

   c. Entrez `admin` pour le nom d’utilisateur, et entrez le mot de passe de l’étape de configuration précédente.

   d. Confirmez `Overall Health = pass`.

   e. Quittez l’utilitaire d’administration.

   f. Quittez SSH.

   ![Diagnostics pour l’opérationnalisation](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>Si vous rencontrez d’importants retards lorsque vous utilisez un service web créé avec des fonctions mrsdeploy dans un contexte de calcul Spark, vous devrez peut-être ajouter des dossiers manquants. L’application Spark appartient à un utilisateur nommé *rserve2* à chaque fois qu’elle est appelée depuis un service web à l’aide de fonctions mrsdeploy. Pour contourner ce problème :

    #Create these required folders for user rserve2 in local and HDFS
    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    #Create a new Spark compute context 
    rxSparkConnect(reset = TRUE)


À ce stade, la configuration de l’opérationnalisation est terminée. Maintenant, vous pouvez utiliser le package mrsdeploy sur R Client pour vous connecter à l’opérationnalisation sur le nœud de périphérie. Vous pouvez ensuite démarrer à l’aide de ses fonctionnalités, comme [l’exécution à distance](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) et les [services web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). Selon que votre cluster est configuré sur un réseau virtuel ou non, vous devrez peut-être configurer le tunneling de réacheminement du port via une connexion SSH.

### <a name="r-server-cluster-on-a-virtual-network"></a>Cluster RServer sur un réseau virtuel

Assurez-vous que vous autorisez le trafic via le port 12800 vers le nœud de périphérie. De cette façon, vous pouvez utiliser le nœud de périphérie pour vous connecter à la fonctionnalité d’opérationnalisation.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Si `remoteLogin()` ne peut pas se connecter au nœud de périphérie, mais que vous pouvez utiliser SSH pour vous connecter à ce dernier, vous devez vérifier si la règle permettant d’autoriser le trafic sur le port 12800 est configurée correctement. Si vous continuez à rencontrer ce problème, configurez le tunneling de réacheminement du port via SSH pour le contourner. Consultez la section suivante pour obtenir des instructions.

### <a name="r-server-cluster-not-set-up-on-a-virtual-network"></a>Cluster RServer non configuré sur un réseau virtuel

Si votre cluster n’est pas configuré sur un réseau virtuel, ou si vous rencontrez des problèmes de connectivité via un réseau virtuel, vous pouvez utiliser le tunneling de réacheminement du port SSH :

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Vous pouvez également le configurer sur PuTTY :

![Connexion SSH PuTTY](./media/r-server-get-started/putty.png)

Une fois votre session SSH active, le trafic à partir du port 12800 de votre machine est transféré vers le port du nœud de périphérie 12800, via une session SSH. Vérifiez que vous utilisez `127.0.0.1:12800` dans votre méthode `remoteLogin()`. Cette méthode se connecte à l’opérationnalisation du nœud de périphérie via le réacheminement de port.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Mettre à l’échelle des nœuds de calcul d’opérationnalisation Microsoft R Server sur des nœuds Worker HDInsight

### <a name="decommission-the-worker-nodes"></a>Désactiver les nœuds Worker

Microsoft R Server n’est actuellement pas géré par YARN. Si les nœuds Worker ne sont pas désactivés, ResourceManager YARN ne fonctionnera pas comme prévu, car il n’aura pas connaissance des ressources utilisées par le serveur. Afin d’éviter ce problème, nous vous recommandons de désactiver les nœuds Worker avant d’augmenter la taille des nœuds de calcul.

Pour désactiver les nœuds Worker :

1. Connectez-vous à la console Ambari du cluster HDI et cliquez sur l’onglet **Hôtes**.
2. Sélectionnez les nœuds Worker à désactiver, puis cliquez sur **Actions** > **Hôtes sélectionnés** > **Hôtes** > **Activer le mode de maintenance**. Par exemple, dans l’image suivante, nous avons sélectionné wn3 et wn4 pour la désactivation.  

   ![Capture d’écran des commandes pour activer le mode de maintenance](./media/r-server-get-started/get-started-operationalization.png)  

3. Sélectionnez **Actions** > **Hôtes sélectionnés** > **DataNodes** > **Désactiver**.
4. Sélectionnez **Actions** > **Hôtes sélectionnés** > **NodeManagers** > **Désactiver**.
5. Sélectionnez **Actions** > **Hôtes sélectionnés** > **DataNodes** > **Arrêter**.
6. Sélectionnez **Actions** > **Hôtes sélectionnés** > **NodeManagers** > **Arrêter**.
7. Sélectionnez **Actions** > **Hôtes sélectionnés** > **Hôtes** > **Arrêter tous les composants**.
8. Désélectionnez les nœuds Worker et sélectionnez les nœuds principaux.
9. Sélectionnez **Actions** > **Hôtes sélectionnés** > **Hôtes** > **Redémarrer tous les composants**.

### <a name="configure-compute-nodes-on-each-decommissioned-worker-node"></a>Configurer les nœuds de calcul sur chaque nœud Worker désactivé

1. Utilisez SSH pour vous connecter à chaque nœud Worker désactivé.
2. Exécutez un utilitaire d’administration à l’aide de `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Entrez `1` pour sélectionner l’option `Configure R Server for Operationalization`.
4. Entrez `c` pour sélectionner l’option `C. Compute node`. Cette étape permet de configurer un nœud de calcul sur le nœud Worker.
5. Quittez l’utilitaire d’administration.

### <a name="add-compute-nodes-details-on-the-web-node"></a>Ajouter des détails sur les nœuds de calcul sur le nœud web

Une fois que tous les nœuds Worker désactivés ont été configurés pour s’exécuter sur le nœud de calcul, revenez au nœud de périphérie et ajoutez les adresses IP des nœuds Worker désactivés dans la configuration du nœud web Microsoft R Server :

1. Utilisez SSH pour vous connecter au nœud de périmètre.
2. Exécutez `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
3. Recherchez la section `URIs` et ajouter l’adresse IP du nœud Worker ainsi que les détails du port.

    ![Ligne de commande du nœud de périphérie](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, consultez [Exigences de contrôle d’accès](../hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Étapes suivantes

Maintenant, vous devrez savoir comment créer un cluster HDInsight qui inclut R Server. Vous avez également appris les principes de base de l’utilisation de la console R à partir d’une session SSH. Les rubriques suivantes expliquent les autres méthodes de gestion et l’utilisation de R Server sur HDInsight :

* [Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)](r-server-compute-contexts.md)
* [Options d’Azure Storage pour R Server sur HDInsight](r-server-storage.md)
