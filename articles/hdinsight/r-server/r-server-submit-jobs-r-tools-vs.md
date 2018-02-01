---
title: Envoyer des travaux depuis Outils R pour Visual Studio - Azure HDInsight | Microsoft Docs
description: Envoyez des travaux R de votre ordinateur Visual Studio local vers un cluster HDInsight.
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: maxluk
ms.openlocfilehash: 1a82ba7790f739768156a8bee33a74d7130e24e1
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="submit-jobs-from-r-tools-for-visual-studio"></a>Envoyer des travaux depuis Outils R pour Visual Studio

[Outils R pour Visual Studio](https://www.visualstudio.com/vs/rtvs/) (RTVS) est une extension gratuite et open source pour les éditions Community (gratuite), Professional et Enterprise de [Visual Studio 2017](https://www.visualstudio.com/downloads/) et [Visual Studio 2015 Update 3](http://go.microsoft.com/fwlink/?LinkId=691129) ou version ultérieure.

RTVS améliore votre flux de travail R en offrant des outils tels que la [fenêtre interactive R](https://docs.microsoft.com/visualstudio/rtvs/interactive-repl) (REPL), intellisense (saisie semi-automatique du code), [la visualisation de tracé](https://docs.microsoft.com/visualstudio/rtvs/visualizing-data) par le biais de bibliothèques R telles que ggplot2 et ggviz, [le débogage du code R](https://docs.microsoft.com/visualstudio/rtvs/debugging), et bien plus encore.

## <a name="set-up-your-environment"></a>Configurer votre environnement

1. Installez les [Outils R pour Visual Studio](https://docs.microsoft.com/visualstudio/rtvs/installation).

    ![Installation de RTVS dans Visual Studio 2017](./media/r-server-submit-jobs-r-tools-vs/install-r-tools-for-vs.png)

2. Sélectionnez la charge de travail *Applications de science et analyse des données*, puis sélectionnez les options **Prise en charge du langage R**, **Prise en charge du runtime pour les outils de développement R** et **Microsoft R Client**.

3. Vous devez avoir des clés publiques et privées pour l’authentification SSH.
<!-- {TODO tbd, no such file yet}[use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-windows.md) -->

4. Installez [R Server](https://msdn.microsoft.com/microsoft-r/rserver-install-windows) sur votre ordinateur. R Server fournit les fonctions [`RevoScaleR`](https://msdn.microsoft.com/microsoft-r/scaler/scaler) et `RxSpark`.

5. Installez [PuTTY](http://www.putty.org/) pour fournir un contexte de calcul afin d’exécuter les fonctions `RevoScaleR` de votre client local sur votre cluster HDInsight.

6. Vous pouvez appliquer les Paramètres de science des données à votre environnement Visual Studio, qui fournit une nouvelle disposition pour votre espace de travail pour les outils R.
    1. Pour enregistrer vos paramètres Visual Studio actuels, utilisez la commande **Outils > Importation et exportation de paramètres**, puis sélectionnez **Exporter les paramètres d'environnement sélectionnés** et spécifiez un nom de fichier. Pour restaurer ces paramètres, utilisez la même commande et sélectionnez **Importer les paramètres d'environnement sélectionnés**.

    2. Accédez à l’élément de menu **Outils R**, puis sélectionnez **Paramètres de science des données**.

        ![Paramètres de science des données](./media/r-server-submit-jobs-r-tools-vs/data-science-settings.png)

    > [!NOTE]
    > À l’aide de l’approche décrite à l’étape 1, vous pouvez également enregistrer et restaurer votre disposition de scientifique des données personnalisée, plutôt que de répéter la commande **Paramètres de science des données**.

## <a name="execute-local-r-methods"></a>Exécuter des méthodes R locales

1. Créez votre [cluster HDInsight R Server](r-server-get-started.md).
2. Installez [l’extension RTVS](https://docs.microsoft.com/visualstudio/rtvs/installation).
3. Téléchargez [l’exemple de fichier zip](https://github.com/Microsoft/RTVS-docs/archive/master.zip).
4. Ouvrez `examples/Examples.sln` pour lancer la solution dans Visual Studio.
5. Ouvrez le fichier `1-Getting Started with R.R` dans le dossier de solution `A first look at R`.
6. En commençant par le haut du fichier, appuyez sur Ctrl + Entrée pour envoyer chaque ligne, une à la fois, vers la fenêtre interactive R. Certaines lignes peuvent prendre un certain temps, car elles installent des packages.
    * Vous pouvez aussi sélectionner toutes les lignes dans le fichier R (Ctrl + A), puis les exécuter toutes (Ctrl + Entrée), ou sélectionner l’icône d’exécution en mode interactif dans la barre d’outils.
        ![Exécuter en mode interactif](./media/r-server-submit-jobs-r-tools-vs/execute-interactive.png)

7. Après avoir exécuté toutes les lignes dans le script, vous devez obtenir une sortie semblable à celle-ci :

    ![Paramètres de science des données](./media/r-server-submit-jobs-r-tools-vs/workspace.png)

## <a name="submit-jobs-to-an-hdinsight-r-cluster"></a>Envoyer des travaux vers un cluster R HDInsight

À l’aide d’une instance Microsoft R Server/Microsoft R Client d’un ordinateur Windows équipé de PuTTY, vous pouvez créer un contexte de calcul qui exécute des fonctions `RevoScaleR` distribuées de votre client local sur votre cluster HDInsight. Utilisez `RxSpark` pour créer le contexte de calcul, en spécifiant votre nom d’utilisateur, le nœud de périphérie du cluster Hadoop, les commutateurs SSH, et ainsi de suite.

1. Pour rechercher le nom d’hôte de votre nœud de périphérie, ouvrez votre volet de cluster R HDInsight dans Azure, puis sélectionnez **Secure Shell (SSH)** dans le menu supérieur du volet Vue d’ensemble.

    ![SSH (Secure Shell)](./media/r-server-submit-jobs-r-tools-vs/ssh.png)

2. Copiez la valeur **Nom d’hôte du nœud de périphérie**.

    ![Nom d’hôte du nœud de périphérie](./media/r-server-submit-jobs-r-tools-vs/edge-node.png)

3. Collez le code suivant dans la fenêtre interactive R dans Visual Studio, en remplaçant les valeurs des variables de configuration par celle de votre environnement.

    ```R
    # Setup variables that connect the compute context to your HDInsight cluster
    mySshHostname <- 'r-cluster-ed-ssh.azurehdinsight.net ' # HDI secure shell hostname
    mySshUsername <- 'sshuser' # HDI SSH username
    mySshClientDir <- "C:\\Program Files (x86)\\PuTTY"
    mySshSwitches <- '-i C:\\Users\\azureuser\\r.ppk' # Path to your private ssh key
    myHdfsShareDir <- paste("/user/RevoShare", mySshUsername, sep = "/")
    myShareDir <- paste("/var/RevoShare", mySshUsername, sep = "/")
    mySshProfileScript <- "/usr/lib64/microsoft-r/3.3/hadoop/RevoHadoopEnvVars.site"

    # Create the Spark Cluster compute context
    mySparkCluster <- RxSpark(
          sshUsername = mySshUsername,
      sshHostname = mySshHostname,
      sshSwitches = mySshSwitches,
      sshProfileScript = mySshProfileScript,
      consoleOutput = TRUE,
      hdfsShareDir = myHdfsShareDir,
      shareDir = myShareDir,
      sshClientDir = mySshClientDir
    )
    
    # Set the current compute context as the Spark compute context defined above
    rxSetComputeContext(mySparkCluster)
    ```
    
    ![Définition du contexte Spark](./media/r-server-submit-jobs-r-tools-vs/spark-context.png)

4. Dans la fenêtre interactive R, exécutez les commandes suivantes :

    ```R
    rxHadoopCommand("version") # should return version information
    rxHadoopMakeDir("/user/RevoShare/newUser") # creates a new folder in your storage account
    rxHadoopCopy("/example/data/people.json", "/user/RevoShare/newUser") # copies file to new folder
    ```

    Le résultat doit ressembler à ce qui suit :

    ![Exécution réussie de la commande rx](./media/r-server-submit-jobs-r-tools-vs/rx-commands.png)

5. Vérifiez que le `rxHadoopCopy` a bien copié le fichier `people.json` du dossier d’exemple de données vers le nouveau dossier `/user/RevoShare/newUser` :

    1. Dans le volet de votre cluster R HDInsight dans Azure, sélectionnez **Comptes de stockage** dans le menu de gauche.

        ![Comptes de stockage](./media/r-server-submit-jobs-r-tools-vs/storage-accounts.png)

    2. Sélectionnez le compte de stockage par défaut pour votre cluster et prenez note du nom du conteneur/répertoire.

    3. Sélectionnez **Conteneurs** dans le menu de gauche du volet de votre compte de stockage.

        ![Conteneurs](./media/r-server-submit-jobs-r-tools-vs/containers.png)

    4. Sélectionnez le nom du conteneur de votre cluster, accédez au dossier **user** (vous devrez peut-être cliquer sur *Charger plus* en bas de la liste), puis sélectionnez *RevoShare*, puis **newUser**. Le fichier `people.json` doit être affiché dans le dossier `newUser`.

        ![Fichier copié](./media/r-server-submit-jobs-r-tools-vs/copied-file.png)

6. Une fois que vous avez terminé d’utiliser le contexte Spark actuel, vous devez l’arrêter. Vous ne pouvez pas exécuter plusieurs contextes à la fois.

    ```R
    rxStopEngine(mySparkCluster)
    ```

## <a name="next-steps"></a>Étapes suivantes

* [Options de contexte de calcul pour R Server sur HDInsight (préversion)](r-server-compute-contexts.md)
* [Combinaison de ScaleR et SparkR](../hdinsight-hadoop-r-scaler-sparkr.md) fournit un exemple de prédictions de retards de vols.
<!-- * You can also submit R jobs with the [R Studio Server](hdinsight-submit-jobs-from-r-studio-server.md) -->
