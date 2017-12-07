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
ms.openlocfilehash: f6e13a159cf7f5a1302749d74d2833cf1a035464
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="get-started-using-r-server-on-hdinsight"></a>Commencer à utiliser R Server sur HDInsight

HDInsight inclut une option R Server à intégrer dans votre cluster HDInsight. Cette option permet aux scripts R d’utiliser Spark et MapReduce afin d’exécuter des calculs distribués. Dans ce document, vous allez apprendre à créer un R Server sur cluster HDInsight, puis à exécuter un script R illustrant l’utilisation de Spark pour effectuer des calculs R distribués.


## <a name="prerequisites"></a>Composants requis

* **Abonnement Azure** : avant de commencer ce didacticiel, vous devez disposer d’un abonnement Azure. Pour plus d’informations, consultez l’article [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obtenir une version d’essai de Microsoft Azure gratuite).
* **Client Secure Shell (SSH)** : un client SSH est utilisé pour se connecter à distance au cluster HDInsight et exécuter des commandes directement sur celui-ci. Pour en savoir plus, consultez [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* **Clés SSH (facultatives)** : vous pouvez sécuriser le compte SSH utilisé pour la connexion au cluster à l’aide d’un mot de passe ou d’une clé publique. Le recours à un mot de passe est plus facile et vous permet de commencer sans avoir à créer une paire de clés publique/privée. Toutefois, il est plus sûr d’utiliser une clé.

> [!NOTE]
> Les étapes décrites dans ce document supposent que vous utilisez un mot de passe.


## <a name="automated-cluster-creation"></a>Création de cluster automatisée

Vous pouvez automatiser la création de HDInsight R Server à l’aide de modèles Azure Resource Manager, du Kit de développement logiciel (SDK) et de PowerShell.

* Pour créer un R Server à l’aide d’un modèle Azure Resource Management, consultez la rubrique [Deploy an R-server HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/) (Déployer un cluster HDInsight R Server).
* Pour obtenir un R Server à l’aide du Kit de développement logiciel (SDK) .NET, consultez [Créer des clusters basés sur Linux dans HDInsight à l’aide du Kit de développement logiciel (SDK) .NET](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md).
* Pour déployer R Server à l’aide de PowerShell, consultez l’article sur la [création d’un R Server avec PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## <a name="create-the-cluster-using-the-azure-portal"></a>Créer le cluster à l’aide du portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Sélectionnez **NOUVEAU** -> **Intelligence et analyse**, -> **HDInsight**.

    ![Image de la création d’un cluster](./media/r-server-get-started/newcluster.png)

3. Dans le champ **Nom du cluster** de l’expérience **Création rapide**, entrez un nom pour le cluster. Si vous avez plusieurs abonnements Azure, utilisez l’entrée **Abonnement** pour sélectionner celui que vous voulez utiliser.

    ![Sélections de nom et d’abonnement de cluster](./media/r-server-get-started/clustername.png)

4. Sélectionnez **Type de cluster** pour ouvrir le panneau **Configuration de cluster**. Dans le panneau **Configuration de cluster** , sélectionnez les options suivantes :

    * **Type de cluster** : R Server
    * **Version** : sélectionnez la version de R Server à installer sur le cluster. La version actuellement disponible est ***R Server 9.1 (HDI 3.6)***. Les notes de publication pour chacune des versions disponibles de R Server se trouvent [ici](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes).
    * **R Studio Community Edition pour R Server** : cet IDE basé sur navigateur est installé par défaut sur le nœud de périmètre. Si vous préférez ne pas l’installer, décochez la case. Si vous choisissez de l’installer, vous trouverez l’URL d’accès à la connexion à RStudio Server sur le panneau d’une application du portail de votre cluster une fois qu’il a été créé.
    * Conservez les valeurs par défaut des autres options et utilisez le bouton **Sélectionner** pour enregistrer le type de cluster.

        ![Capture d’écran du panneau Type de cluster](./media/r-server-get-started/clustertypeconfig.png)

5. Saisissez un **nom de connexion au cluster** et un **mot de passe de connexion au cluster**.

    Spécifiez un **nom d’utilisateur SSH**. Le protocole SSH est utilisé pour se connecter à distance au cluster à l’aide d’un client **Secure Shell (SSH)** . Vous pouvez spécifier l’utilisateur SSH dans cette boîte de dialogue ou après la création du cluster (dans l’onglet Configuration du cluster). R Server est configuré pour attendre un **nom d’utilisateur SSH** de l’« utilisateur distant ».  **Si vous utilisez un autre nom d’utilisateur, vous devrez effectuer une étape supplémentaire après la création du cluster.**

    Laissez la case cochée pour **Utiliser le même mot de passe que pour la connexion au cluster** pour utiliser **MOT DE PASSE** comme type d’authentification, sauf si vous préférez utiliser une clé publique.  Vous aurez besoin d’une paire de clés publique/privée si vous souhaitez accéder au R Server sur le cluster via un client distant (par exemple, RTV, RStudio ou un autre IDE de bureau). Vous devez choisir un mot de passe SSH si vous installez RStudio Server Community Edition.     

    Pour créer et utiliser une paire de clés publique/privée, décochez **Utiliser le même mot de passe que pour la connexion au cluster**, puis sélectionnez **CLÉ PUBLIQUE** et procédez comme suit. Ces instructions supposent que Cygwin avec ssh-keygen ou équivalent est installé.

    * Générez une paire de clés publique/privée à partir de l’invite de commandes sur votre ordinateur portable :

        ssh-keygen -t rsa -b 2048

    * Suivez les instructions pour nommer un fichier de clé, puis entrez un mot de passe pour renforcer la sécurité. Votre écran doit ressembler à ceci :

        ![Ligne de commande SSH dans Windows](./media/r-server-get-started/sshcmdline.png)

    * Cette commande crée un fichier de clé privée et un fichier de clé publique sous le nom <private-key-filename>.pub, par exemple furiosa et furiosa.pub.

        ![SSH dir](./media/r-server-get-started/dir.png)

    * Spécifiez ensuite le fichier de clé publique (&#42;.pub) lors de l’attribution des informations d’identification du cluster HDI, puis confirmez votre groupe de ressources et votre région et sélectionnez **Suivant**.

        ![Panneau Informations d’identification](./media/r-server-get-started/publickeyfile.png)  

   * Modifiez les autorisations sur le fichier de clé privée sur votre ordinateur portable :

        chmod 600 <nom-fichier-clé-privée>

   * Utilisez le fichier de clé privée avec SSH pour la connexion à distance :

        ssh –i <private-key-filename> remoteuser@<hostname public ip>

      Ou, dans le cadre de la définition du contexte de calcul Hadoop Spark pour R Server sur le client. Consultez la sous-section **Using Microsoft R Server as a Hadoop Client** (Utilisation de Microsoft R Server comme client Hadoop) de [Create a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started#creating-a-compute-context-for-spark) (Créer un contexte de calcul pour Spark).

6. La création rapide vous fait passer au panneau **Stockage** pour sélectionner les paramètres de compte de stockage à utiliser pour l’emplacement principal du système de fichiers HDFS utilisé par le cluster. Sélectionnez un compte de stockage Azure nouveau ou existant ou un compte de stockage Data Lake existant.

    - Si vous sélectionnez un compte de stockage Azure, un compte de stockage existant est sélectionné en choisissant **Sélectionner un compte de stockage**, puis en spécifiant le compte en question. Créez un nouveau compte à l’aide du lien **Nouveau** dans la section **Sélectionner un compte de stockage**.

      > [!NOTE]
      > Si vous sélectionnez **Nouveau**, vous devez entrer un nom pour le nouveau compte de stockage. Une coche verte s’affiche si le nom est accepté.

      Le **Conteneur par défaut** correspond au nom du cluster. Laissez cette valeur par défaut.

      Si l’option Nouveau compte de stockage a été sélectionnée, une invite permettant de choisir un **Emplacement** s’affiche pour sélectionner la région dans laquelle le compte de stockage sera créé.  

         ![Panneau Source de données](./media/r-server-get-started/datastore.png)  

      > [!IMPORTANT]
      > La sélection de l’emplacement de la source de données par défaut définit également l’emplacement du cluster HDInsight. Le cluster et la source de données par défaut doivent se trouver dans la même région.

    - Si vous choisissez d’utiliser un Data Lake Store existant, sélectionnez le compte de stockage ADLS à utiliser et ajoutez l’identité *ADD* de cluster à votre cluster afin d’autoriser l’accès au magasin. Pour plus d’informations sur ce processus, consultez [Créer un cluster HDInsight avec Data Lake Store à l’aide du portail Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal).

    Pour enregistrer la configuration de la source de données, utilisez le bouton **Sélectionner** .


7. Le panneau **Résumé** s’affiche pour valider tous les paramètres. Ici, vous pouvez modifier votre **Taille de cluster** pour changer le nombre de serveurs de votre cluster et également spécifier les **Actions de script** vous souhaitez exécuter. Sauf si vous savez que vous avez besoin d’un cluster plus grand, conservez le nombre de nœuds Worker par défaut `4`. Le coût estimé du cluster s’affiche dans le panneau.

    ![Résumé du cluster](./media/r-server-get-started/clustersummary.png)

   > [!NOTE]
   > Si nécessaire, vous pouvez redimensionner votre cluster ultérieurement via le portail (**Cluster** -> **Paramètres** -> **Mettre le cluster à l’échelle**) pour augmenter ou diminuer le nombre de nœuds Worker.  Le redimensionnement peut être utile pour faire fonctionner le cluster au ralenti lorsqu’il n’est pas utilisé, ou pour ajouter de la capacité afin de répondre aux besoins des tâches plus intensives.
   >
   >

   Certains facteurs à prendre en compte lors du dimensionnement de votre cluster, les nœuds de données et le nœud de périmètre sont les suivants :  

   * Les performances des analyses du R Server distribué sur Spark sont proportionnelles au nombre de nœuds Worker lorsque les données sont volumineuses.  

   * Les performances des analyses du R Server sont linéaires quant à la taille des données analysées. Par exemple :  

     * Pour les volumes de données petits à moyens, les performances sont idéales lorsqu’elles sont analysées dans un contexte de calcul local à la périphérie du nœud.  Pour plus d’informations sur les scénarios de fonctionnement des contextes de calcul locaux et Spark, consultez la rubrique Options de contexte de calcul pour R Server sur HDInsight.<br>
     * Si vous vous connectez au nœud de périmètre et exécutez votre script R, toutes les fonctions seront exécutées <strong>localement</strong> sur le nœud de périmètre sauf les fonctions ScaleR rx. La mémoire et le nombre de cœurs du nœud de périmètre doivent donc être dimensionnés en conséquence. Ces conditions s’appliquent si vous utilisez R Server sur HDI comme contexte de calcul à distance à partir de votre ordinateur portable.

     ![Panneau Niveaux de tarification du nœud](./media/r-server-get-started/pricingtier.png)

     Pour enregistrer la configuration de la tarification du nœud, utilisez le bouton **Sélectionner**.

   Il existe également un lien permettant de **télécharger le modèle et les paramètres**. Cliquer sur ce lien permet d’afficher les scripts qui peuvent être utilisés pour automatiser la création d’un cluster avec la configuration sélectionnée. Ces scripts sont également disponibles dans l’entrée du portail Azure de votre cluster, une fois celui-ci créé.

   > [!NOTE]
   > La création du cluster prend un certain temps (en règle générale, environ 20 minutes). Utilisez la vignette du tableau d’accueil ou l’entrée **Notifications** à gauche de la page pour vérifier le processus de création.
   >
   >

<a name="connect-to-rstudio-server"></a>
## <a name="connect-to-rstudio-server"></a>Se connecter à RStudio Server

Si vous avez choisi d’inclure RStudio Server Community Edition dans votre installation, vous pouvez accéder à la connexion RStudio via deux méthodes différentes.

1. Accédez à l’URL suivante (où **CLUSTERNAME** est le nom du cluster que vous avez créé) :

    https://**CLUSTERNAME**.azurehdinsight.net/rstudio/

2. Ouvrez l’entrée relative à votre cluster dans le portail Azure, sélectionnez le lien rapide **Tableaux de bord R Server**, puis choisissez le **tableau de bord RStudio** :

     ![Accès au tableau de bord RStudio](./media/r-server-get-started/rstudiodashboard1.png)

     ![Accès au tableau de bord RStudio](./media/r-server-get-started/rstudiodashboard2.png)

   > [!IMPORTANT]
   > Quelle que soit la méthode que vous utilisez, vous devez vous authentifier deux fois lorsque vous ouvrez une session pour la première fois.  À la première authentification, fournissez *l’ID d’utilisateur* et le *mot de passe administrateur* pour le cluster. À la seconde invite, indiquez *l’ID d’utilisateur* et le *mot de passe SSH*. Les connexions suivantes ne nécessitent que le *mot de passe SSH* et *l’ID d’utilisateur*.

<a name="connect-to-edge-node"></a>
## <a name="connect-to-the-r-server-edge-node"></a>Se connecter au nœud de périmètre R Server

Connectez-vous au nœud de périmètre R Server du cluster HDInsight à l’aide de SSH, en utilisant la commande suivante :

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

> [!NOTE]
> Vous pouvez trouver l’adresse `USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net` dans le portail Azure en sélectionnant votre cluster, puis **Tous les paramètres**  -> **Applications** -> **RServer**. Cela a pour effet d’afficher les informations relatives au point de terminaison SSH pour le nœud de périmètre.
>
> ![Image du point de terminaison SSH pour le nœud de périmètre](./media/r-server-get-started/sshendpoint.png)
>
>

Si vous utilisez un mot de passe pour sécuriser votre compte utilisateur SSH, vous serez invité à le saisir. Si vous utilisez une clé publique, vous devrez peut-être utiliser le paramètre `-i` pour spécifier la clé privée correspondante. Par exemple :

    ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Pour en savoir plus, consultez la page [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

Une fois connecté, une invite semblable à la suivante s’affiche :

    sername@ed00-myrser:~$

<a name="enable-concurrent-users"></a>
## <a name="enable-multiple-concurrent-users"></a>Autoriser plusieurs utilisateurs simultanés

Vous pouvez autoriser plusieurs utilisateurs simultanés en les ajoutant au nœud de périmètre sur lequel la version de RStudio Community s’exécute.

Lorsque vous créez un cluster HDInsight, vous devez fournir deux utilisateurs, un utilisateur HTTP et un utilisateur SSH :

![Utilisateur simultané n° 1](./media/r-server-get-started/concurrent-users-1.png)

- **Nom d’utilisateur de connexion du cluster** : un utilisateur HTTP pour l’authentification via la passerelle HDInsight qui est utilisée pour protéger les clusters HDInsight que vous avez créés. Cet utilisateur HTTP est utilisé pour accéder à l’IU Ambari, l’IU YARN, ainsi qu’à d’autres composants d’interface utilisateur.
- **Nom d’utilisateur Secure Shell (SSH)** : un utilisateur SSH pour l’accès au cluster via Secure Shell. Ce dernier correspond à un utilisateur dans le système Linux pour tous les nœuds principaux, les nœuds Worker et les nœuds de périmètre. Par conséquent, vous pouvez utiliser Secure Shell pour accéder à n’importe quel nœud dans un cluster distant.

La version de RStudio Server Community utilisée dans les clusters de type Microsoft R Server sur HDInsight n’accepte comme mécanisme de connexion que les noms d’utilisateur et mots de passe Linux. Elle ne prend pas en charge le passage de jetons. Par conséquent, si vous avez créé un cluster et que vous souhaitez utiliser RStudio pour y accéder, vous devez ouvrir une session deux fois.

- Commencez par ouvrir une session à l’aide des informations d’identification de l’utilisateur HTTP via la passerelle HDInsight : 

    ![Utilisateur simultané 2a](./media/r-server-get-started/concurrent-users-2a.png)

- Utilisez ensuite les informations d’identification de l’utilisateur SSH pour ouvrir une session RStudio :
  
    ![Utilisateur simultané 2b](./media/r-server-get-started/concurrent-users-2b.png)

Actuellement, on ne peut créer qu’un seul compte d’utilisateur SSH lors de l’approvisionnement d’un cluster HDInsight. Par conséquent, pour permettre à plusieurs utilisateurs d’accéder aux clusters Microsoft R Server sur HDInsight, nous devons créer des utilisateurs supplémentaires dans le système Linux.

Comme RStudio Server Community est en cours d’exécution sur le nœud de périmètre du cluster, il existe plusieurs étapes à réaliser :

1. Utiliser l’utilisateur SSH créé pour ouvrir une session sur le nœud de périmètre
2. Ajouter d’autres utilisateurs Linux dans le nœud de périmètre
3. Utiliser la version RStudio Community avec l’utilisateur créé

### <a name="step-1-use-the-created-ssh-user-to-log-in-to-the-edge-node"></a>Étape n° 1 : Utiliser l’utilisateur SSH créé pour ouvrir une session sur le nœud de périmètre

Téléchargez n’importe quel outil SSH (tel que PuTTY) et utilisez l’utilisateur SSH existant afin d’ouvrir une session. Ensuite, suivez les instructions décrites à la page [Se connecter à HDInsight (Hadoop) à l’aide de SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) afin d’accéder au nœud de périmètre. L’adresse du nœud de périmètre pour un cluster R Server sur HDInsight est la suivante : *clustername-ed-ssh.azurehdinsight.net*


### <a name="step-2-add-more-linux-users-in-edge-node"></a>Étape n° 2 : Ajouter d’autres utilisateurs Linux dans le nœud de périmètre

Pour ajouter un utilisateur au nœud de périmètre, exécutez les commandes suivantes :

    sudo useradd yournewusername -m
    sudo passwd yourusername

Les éléments renvoyés doivent être les suivants : 

![Utilisateur simultané 3](./media/r-server-get-started/concurrent-users-3.png)

Ignorez le message vous invitant à saisir le « mot de passe Kerberos actuel » en appuyant simplement sur la touche **Entrée**. L’option `-m` de la commande `useradd` indique que le système va créer un dossier de base pour l’utilisateur, requis pour RStudio Community.


### <a name="step-3-use-rstudio-community-version-with-the-user-created"></a>Étape n° 3 : Utiliser la version RStudio Community avec l’utilisateur créé

Utilisez l’utilisateur créé pour vous connecter à RStudio :

![Utilisateur simultané 4](./media/r-server-get-started/concurrent-users-4.png)

Comme vous pouvez le voir, RStudio indique que vous utilisez le nouvel utilisateur (dans cet exemple, l’utilisateur *sshuser6*) pour ouvrir une session dans le cluster : 

![Utilisateur simultané 5](./media/r-server-get-started/concurrent-users-5.png)

Grâce aux informations d’identification d’origine (par défaut, *sshuser*), vous pouvez aussi ouvrir une session simultanément à partir d’une autre fenêtre du navigateur.

Vous pouvez soumettre un travail à l’aide des fonctions ScaleR. Voici un exemple des commandes utilisées pour exécuter un travail :

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


Notez que les travaux soumis se trouvent sous différents noms d’utilisateur dans l’IU YARN :

![Utilisateur simultané 6](./media/r-server-get-started/concurrent-users-6.png)

Notez également que les utilisateurs récemment ajoutés ne possèdent pas les privilèges racine dans le système Linux. Cependant, ils bénéficient du même accès à l’ensemble des fichiers dans les stockages distants HDFS et WASB.


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

    Prise en charge du langage naturel, mais exécution dans un paramètre régional anglais

        R is a collaborative project with many contributors.
        Type 'contributors()' for more information and
        'citation()' on how to cite R or R packages in publications.

        Type 'demo()' for some demos, 'help()' for on-line help, or
        'help.start()' for an HTML browser interface to help.
        Type 'q()' to quit R.

        Microsoft R Server version 8.0: an enhanced distribution of R
        Microsoft packages Copyright (C) 2016 Microsoft Corporation

    Tapez ’readme()’ pour consulter les notes de publication.
    >

3. Vous pouvez entrer un code R dans l’invite `>` . R Server inclut des packages qui vous permettent d’interagir facilement avec Hadoop et d’exécuter des calculs distribués. Par exemple, utilisez la commande suivante pour afficher la racine du système de fichiers par défaut du cluster HDInsight :

        rxHadoopListFiles("/")

4. Vous pouvez également utiliser l’adressage de style WASB.

        rxHadoopListFiles("wasb:///")


## <a name="using-r-server-on-hdi-from-a-remote-instance-of-microsoft-r-server-or-microsoft-r-client"></a>À l’aide de R Server sur HDI à partir d’une instance distante de Microsoft R serveur ou Microsoft R Client

Il est également possible de configurer l’accès au contexte de calcul HDI Hadoop Spark à partir d’une instance distante de Microsoft R Server ou Microsoft R Client en cours d’exécution sur un ordinateur de bureau ou portable. Consultez la sous-section **Using Microsoft R Server as a Hadoop Client** (Utilisation de Microsoft R Server comme client Hadoop) de [Creating a Compute Context for Spark](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started.md) (Créer un contexte de calcul pour Spark). Pour cela, vous devez spécifier les options suivantes lorsque vous définissez le contexte de calcul RxSpark sur votre ordinateur portable : hdfsShareDir, shareDir, sshUsername, sshHostname, sshSwitches et sshProfileScript. Par exemple :


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

Un contexte de calcul vous permet de contrôler si le calcul doit être effectué localement sur le nœud de périmètre, ou s’il doit être distribué entre les nœuds du cluster HDInsight.

1. Dans RStudio Server ou la console R (dans une session SSH), utilisez le code suivant pour charger les exemples de données dans le stockage par défaut pour HDInsight :

        # Set the HDFS (WASB) location of example data
        bigDataDirRoot <- "/example/data"

        # create a local folder for storaging data temporarily
        source <- "/tmp/AirOnTimeCSV2012"
        dir.create(source)

        # Download data to the tmp folder
        remoteDir <- "http://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
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

4. Ensuite, exécutez la même régression logistique en utilisant le contexte Spark. Le contexte Spark répartit le traitement sur tous les nœuds de travail dans le cluster HDInsight.

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


## <a name="accessing-data-in-hive-and-parquet"></a>Accès aux données dans Hive et Parquet

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


Pour des informations supplémentaires sur l’utilisation de ces nouvelles fonctions, consultez l’aide en ligne dans R Server via les commandes `?RxHivedata` et `?RxParquetData`.  


## <a name="install-additional-r-packages-on-the-edge-node"></a>Installer des packages R supplémentaires sur le nœud de périmètre

Si vous souhaitez installer des packages R supplémentaires sur le nœud de périphérie, vous pouvez utiliser `install.packages()` directement à partir de la console R quand vous êtes connecté au nœud de périphérie par le biais de SSH. Toutefois, si vous avez besoin d’installer des packages R sur les nœuds de travail du cluster, vous devez utiliser une action de script.

Les actions de script sont des scripts de commandes par lot permettant de modifier la configuration du cluster HDInsight ou d’installer d’autres logiciels, comme des packages R supplémentaires. Pour installer des packages supplémentaires à l’aide d’une action de Script, procédez comme suit :

> [!IMPORTANT]
> L’utilisation d’actions de script pour installer des packages R supplémentaires n’est possible qu’après la création du cluster. N’utilisez pas cette procédure lors de la création du cluster, car le script a besoin que R Server soit entièrement installé et configuré.
>
>

1. Dans le [portail Azure](https://portal.azure.com), sélectionnez votre R Server sur le cluster HDInsight.

2. Dans le panneau **Paramètres**, sélectionnez **Actions de script** puis **Envoyer** pour soumettre une nouvelle action de script.

   ![Image du panneau Actions de script](./media/r-server-get-started/scriptaction.png)

3. Dans le panneau **Envoyer une action de script**, entrez les informations suivantes :

   * **Nom** : un nom convivial pour identifier ce script

   * **URI de script bash** : `http://mrsactionscripts.blob.core.windows.net/rpackages-v01/InstallRPackages.sh`

   * **En-tête** : cet élément doit être **décoché**.

   * **Worker** : cet élément doit être **coché**.

   * **Nœuds de périmètre** : cet élément doit être **décoché**.

   * **Zookeeper** : cet élément doit être **décoché**.

   * **Paramètres** : les packages R à installer. Par exemple, `bitops stringr arules`

   * **Conservez cette action de script...** : cet élément doit être **coché**.  

   > [!NOTE]
   > 1. Par défaut, tous les packages R sont installés à partir d’un instantané du référentiel Microsoft MRAN cohérent avec la version du serveur R qui a été installée. Si vous souhaitez installer des versions plus récentes des packages, vous risquez de rencontrer des problèmes de compatibilité. Toutefois, vous pouvez effectuer ce type d’installation en spécifiant `useCRAN` comme premier élément de la liste des packages, par exemple `useCRAN bitops, stringr, arules`.  
   > 2. Certains packages R nécessitent des bibliothèques de système Linux supplémentaires. Pour plus de commodité, nous avons préinstallé les dépendances requises par les 100 premiers packages R les plus populaires. Toutefois, si les packages R que vous installez nécessitent des bibliothèques supplémentaires, vous devez télécharger le script de base utilisé ici et ajouter des étapes pour installer les bibliothèques système. Vous devez ensuite charger le script modifié dans un conteneur d’objets blob publics dans Azure Storage, puis utiliser le script modifié pour installer les packages.
   >    Pour plus d’informations sur le développement d’actions de script, consultez la section [Développer des actions de script](../hdinsight-hadoop-script-actions-linux.md).  
   >
   >

   ![Ajout d’une action de script](./media/r-server-get-started/submitscriptaction.png)

4. Sélectionnez **Créer** pour exécuter le script. Une fois le script exécuté, les packages R sont disponibles sur tous les nœuds de travail.


## <a name="using-microsoft-r-server-operationalization"></a>Utilisation de l’opérationnalisation de Microsoft R Server

Lorsque votre modélisation des données est terminée, vous pouvez opérationnaliser le modèle pour effectuer des prévisions. Pour configurer l’opérationnalisation de Microsoft R Server, procédez comme suit :

Tout d’abord, utilisez SSH dans le nœud de périphérie. Par exemple, 

    ssh -L USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Après l’utilisation de SSH, basculez du répertoire vers la version et le sudo appropriés du dll dotnet : 

- Pour Microsoft R Server 9.1 :

    cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0   sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

- Pour Microsoft R Server 9.0 :

    cd /usr/lib64/microsoft-deployr/9.0.1   sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

Pour configurer l’opérationnalisation de Microsoft R Server avec une configuration complète, procédez comme suit :

1. Sélectionnez « Configure R Server for Operationalization » (Configurer R Server pour l’opérationnalisation).
2. Sélectionnez « A. One-box (web + compute nodes) » (Solution complète + nœuds de calcul).
3. Entrez un mot de passe pour l’utilisateur **admin**.

![opérationnalisation complète](./media/r-server-get-started/admin-util-one-box-.png)

Vous pouvez éventuellement effectuer les vérifications de diagnostic en exécutant un test de diagnostic, comme suit :

1. Sélectionnez « 6. Exécuter des tests de diagnostic ».
2. Sélectionnez « A. Configuration de test ».
3. Entrez le nom d’utilisateur = « admin » et le mot de passe de l’étape de configuration précédente.
4. Confirmez l’intégrité globale = pass.
5. Quittez l’utilitaire d’administration.
6. Quittez SSH.

![Diagnostics pour l’opérationnalisation](./media/r-server-get-started/admin-util-diagnostics.png)


>[!NOTE]
>**Retards importants lors de l’utilisation d’un service web sur Spark**
>
>Si vous rencontrez d’importants retards lorsque vous utilisez un service web créé avec des fonctions mrsdeploy dans un contexte d’exécution Spark, vous devrez peut-être ajouter des dossiers manquants. L’application Spark appartient à un utilisateur nommé « *rserve2* » à chaque fois qu’elle est appelée depuis un service web à l’aide de fonctions mrsdeploy. Pour contourner ce problème :

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


À ce stade, la configuration de l’opérationnalisation est terminée. Vous pouvez désormais utiliser le package « mrsdeploy » sur votre RClient pour vous connecter à l’opérationnalisation sur le nœud de périmètre et commencer à utiliser ses fonctionnalités, telles que [l’exécution à distance](https://msdn.microsoft.com/microsoft-r/operationalize/remote-execution) et les [services web](https://msdn.microsoft.com/microsoft-r/mrsdeploy/mrsdeploy-websrv-vignette). Selon que votre cluster est configuré sur un réseau virtuel ou non, vous devrez peut-être configurer le tunneling de réacheminement du port via une connexion SSH. Les sections suivantes expliquent comment configurer ce tunnel.

### <a name="rserver-cluster-on-virtual-network"></a>Cluster RServer sur un réseau virtuel

Assurez-vous que vous autorisez le trafic via le port 12800 vers le nœud de périmètre. De cette façon, vous pouvez utiliser le nœud de périmètre pour vous connecter à la fonctionnalité d’opérationnalisation.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Si `remoteLogin()` ne peut pas se connecter au nœud de périmètre, mais si vous pouvez exécuter SSH sur ce dernier, vous devez alors vérifier si la règle permettant d’autoriser le trafic sur le port 12800 a été configurée correctement ou non. Si vous continuez à rencontrer ce problème, configurez le tunneling de réacheminement du port via SSH pour le contourner. Consultez la section suivante pour obtenir des instructions.

### <a name="rserver-cluster-not-set-up-on-virtual-network"></a>Cluster RServer non configuré sur un réseau virtuel

Si votre cluster n’est pas configuré sur un réseau virtuel ou si vous rencontrez des problèmes de connectivité via un réseau virtuel, vous pouvez utiliser le tunneling de réacheminement du port SSH :

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Vous pouvez également le configurer sur PuTTY.

![connexion SSH PuTTY](./media/r-server-get-started/putty.png)

Une fois votre session SSH active, le trafic à partir du port 12800 de votre machine est transféré vers le port du nœud de périmètre 12800, via une session SSH. Vérifiez que vous utilisez `127.0.0.1:12800` dans votre méthode `remoteLogin()`. Cela vous permet de vous connecter à l’opérationnalisation du nœud de périmètre via le réacheminement de port.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="how-to-scale-microsoft-r-server-operationalization-compute-nodes-on-hdinsight-worker-nodes"></a>Comment mettre à l’échelle des nœuds de calcul de l’opérationnalisation Microsoft R Server sur des nœuds Worker HDInsight ?

### <a name="decommission-the-worker-nodes"></a>Désactiver le(s) nœud(s) Worker

Microsoft R Server n’est actuellement pas géré par YARN. Si les nœuds Worker ne sont pas désactivés, le gestionnaire de ressources YARN ne fonctionnera pas comme prévu, car il n’aura pas connaissance des ressources prises en charge par le serveur. Afin d’éviter ce problème, nous vous recommandons de désactiver les nœuds Worker avant d’augmenter la taille des nœuds de calcul.

Étapes de désactivation des nœuds Worker :

* Connectez-vous à la console Ambari du cluster HDI et cliquez sur l’onglet « Hôtes »
* Sélectionnez les nœuds Worker (à désactiver), cliquez sur « Actions » > « Hôtes sélectionnés » > « Hôtes » > puis sur « Activer le mode de maintenance ». Par exemple, dans l’image suivante, nous avons sélectionné wn3 et wn4 pour la désactivation.  

   ![Désactiver les nœuds Worker](./media/r-server-get-started/get-started-operationalization.png)  

* Sélectionnez **Actions** > **Hôtes sélectionnés** > **DataNodes** > cliquez sur **Désactiver**
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **NodeManagers** > cliquez sur **Désactiver**
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **DataNodes** > cliquez sur **Arrêter**
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **NodeManagers** > cliquez sur **Arrêter**
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **Hôtes** > cliquez sur **Stop All Components** (Arrêter tous les composants)
* Désélectionner les nœuds Worker et sélectionner les nœuds principaux
* Sélectionnez **Actions** > **Hôtes sélectionnés** > **Hôtes** > **Restart All Components** (Redémarrer tous les composants)

### <a name="configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Configurer les nœuds de calcul sur chaque nœud Worker désactivé

1. Utilisez SSH dans chaque nœud Worker désactivé.
2. Exécutez l’utilitaire d’administration à l’aide de `dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll`.
3. Sélectionnez « 1 » pour sélectionner l’option « Configurer R Server pour l’opérationnalisation ».
4. Entrez « c » pour sélectionner l’option « C. Nœud de calcul ». Cette opération permet de configurer un nœud de calcul sur le nœud Worker.
5. Quittez l’utilitaire d’administration.

### <a name="add-compute-nodes-details-on-web-node"></a>Ajouter des détails sur les nœuds de calcul sur le nœud web

Une fois que tous les nœuds Worker désactivés ont été configurés pour exécuter le nœud de calcul, revenez au nœud de périmètre et ajoutez les adresses IP des nœuds Worker désactivés dans la configuration du nœud web Microsoft R Server :

* Utilisez SSH au sein du nœud de périmètre.
* Exécutez `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.
* Recherchez la section « URI » et ajouter l’adresse IP du nœud Worker ainsi que les détails du port.

    ![Ligne de commande Désactiver les nœuds Worker](./media/r-server-get-started/get-started-op-cmd.png)


## <a name="troubleshoot"></a>Résolution des problèmes

Si vous rencontrez des problèmes lors de la création de clusters HDInsight, reportez-vous aux [exigences de contrôle d’accès](../hdinsight-administer-use-portal-linux.md#create-clusters).


## <a name="next-steps"></a>Étapes suivantes

Maintenant, vous savez comment créer un cluster HDInsight qui inclut R Server et les principes fondamentaux de l’utilisation de la console R à partir d’une session SSH. Les rubriques suivantes expliquent les autres méthodes de gestion et l’utilisation de R Server sur HDInsight :

* [Options de contexte de calcul pour R Server sur HDInsight (version préliminaire)](r-server-compute-contexts.md)
* [Options d’Azure Storage pour R Server sur HDInsight](r-server-storage.md)
