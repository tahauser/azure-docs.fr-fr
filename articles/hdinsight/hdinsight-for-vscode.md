---
title: Azure HDInsight Tools - Utiliser Visual Studio Code pour Hive, LLAP ou pySpark | Microsoft Docs
description: "Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code pour créer et envoyer des requêtes et des scripts."
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 4f0bcd0992a02e64b4b10347fc30776af17900bd
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>Utiliser les Azure HDInsight Tools pour Visual Studio Code

Découvrez comment utiliser Azure HDInsight Tools pour Visual Studio Code (VSCode) pour créer et envoyer des travaux Hive de traitement par lots, des requêtes Hive interactives et des scripts pySpark. Vous pouvez installer Azure HDInsight Tools sur les plateformes prises en charge par VS Code. Il s’agit des instances Windows, Linux et macOS. Renseignez-vous sur les prérequis pour les différentes plateformes.


## <a name="prerequisites"></a>configuration requise

Avant de poursuivre cet article, vérifiez que vous avez les éléments nécessaires suivants :

- Un cluster HDInsight.  Pour créer un cluster, consultez [Prise en main de HDInsight]( hdinsight-hadoop-linux-tutorial-get-started.md).
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx).
- [Mono](http://www.mono-project.com/docs/getting-started/install/). Mono est nécessaire uniquement pour les plateformes Linux et MacOS.

## <a name="install-the-hdinsight-tools"></a>Installer HDInsight Tools
   
Une fois que vous avez tous les éléments prérequis, installez Azure HDInsight Tools pour VSCode. 

**Pour installer Azure HDInsight tools**

1. Ouvrez Visual Studio Code.

2. Dans le volet gauche, sélectionnez **Extensions**. Saisissez **HDInsight** dans la zone de recherche.

3. À côté de **Azure HDInsight tools**, cliquez sur **Installer**. Après quelques secondes, le bouton **Installer** est remplacé par le bouton **Recharger**.

4. Cliquez sur **Recharger** pour activer l’extension **Azure HDInsight tools**.

5. Sélectionnez **Recharger la fenêtre** pour confirmer. **Azure HDInsight tools** est maintenant affiché dans le volet **Extensions**.

   ![installation de Python avec HDInsight pour Visual Studio Code](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>Ouvrir un espace de travail HDInsight

Vous devez créer un espace de travail dans VS Code avant de pouvoir vous connecter à Azure.

**Pour ouvrir un espace de travail**

1. Dans le menu **Fichier**, sélectionnez **Ouvrir le dossier**. Ensuite, désignez un dossier existant en tant que dossier de travail, ou créez-en un nouveau. Le dossier s’affiche dans le volet gauche.

2. Dans le volet gauche, cliquez sur l’icône **Nouveau fichier** en regard du dossier de travail.

   ![Nouveau fichier](./media/hdinsight-for-vscode/new-file.png)

3. Nommez le nouveau fichier avec l’extension de fichier .hql (requêtes Hive) ou .py (script Spark). Un fichier de configuration **XXXX_hdi_settings.json** est automatiquement ajouté au dossier de travail.

4. Ouvrez le fichier **XXXX_hdi_settings.json** à partir de la fenêtre **EXPLORER** (EXPLORATEUR), ou cliquez avec le bouton droit sur l’éditeur de script pour sélectionner **Set Configuration** (Définir la configuration). Vous pouvez configurer les paramètres d’entrée de connexion, de cluster par défaut et d’envoi des travaux, comme indiqué dans l’exemple du fichier. Vous pouvez aussi laisser les autres paramètres vides.

## <a name="connect-to-azure"></a>Connexion à Azure

Pour pouvoir envoyer des scripts aux clusters HDInsight à partir de VS Code, vous devez vous connecter à votre compte Azure.

**Pour vous connecter à Azure**

1. Créez un dossier de travail et un fichier de script si vous n’en avez pas.

2. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **HDInsight: Login** dans le menu contextuel. Vous pouvez également appuyer sur **Ctrl+Maj+P** et entrer **HDInsight: Login**.

    ![connexion HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. Connectez-vous en suivant les instructions de connexion affichées dans le volet **OUTPUT**.

    **Azure :** ![informations de connexion HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    Une fois que vous êtes connecté, le nom de votre compte Azure s’affiche dans la barre d’état, dans l’angle inférieur gauche de la fenêtre VS Code. 

    > [!NOTE]
    > En raison d’un problème courant relatif à l’authentification Azure, vous devez ouvrir un navigateur en mode privé ou incognito. Si l’authentification à deux facteurs est activée pour votre compte Azure, nous vous recommandons d’utiliser l’authentification par téléphone au lieu du code PIN.
  

4. Cliquez avec le bouton droit sur l’éditeur de script pour ouvrir le menu contextuel. À partir du menu contextuel, vous pouvez effectuer les travaux suivants :

    - Se déconnecter
    - Énumérer les clusters
    - Définir les clusters par défaut
    - Envoyer des requêtes Hive interactives
    - Envoyer des scripts de commandes par lot Hive
    - Envoyer des requêtes PySpark interactives
    - Envoyer un script de commandes par lot PySpark
    - Définir des configurations

**Lier un cluster**

Vous pouvez lier un cluster normal à l’aide du nom d’utilisateur Ambari géré, vous pouvez également lier un cluster de sécurité Hadoop à l’aide du nom d’utilisateur de domaine (par exemple : user1@contoso.com).
1. Ouvrez la palette de commandes en sélectionnant **CTRL + MAJ + P**, puis entrez **HDInsight: Link a cluster**.

   ![commande de lien du cluster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Entrez HDInsight cluster URL -> input Username -> input Password -> select cluster type -> un indicateur indique la réussite de l’opération si la vérification est passée.
   
   ![boîte de dialogue de lien du cluster](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]
   > Nous utilisons le nom d’utilisateur lié et le mot de passe, si le cluster est à la fois connecté à un abonnement Azure et lié à un cluster. 
   
3. Vous pouvez voir un cluster lié à l’aide de la commande **List cluster**. Vous pouvez désormais soumettre un script à ce cluster lié.

   ![cluster lié](./media/hdinsight-for-vscode/linked-cluster.png)

4. Vous pouvez également dissocier un cluster en saisissant **HDInsight: Unlink a cluster** à partir de la palette de commandes.

## <a name="list-hdinsight-clusters"></a>Afficher la liste des clusters HDInsight

Pour tester la connexion, vous pouvez afficher la liste de vos clusters HDInsight :

**Pour afficher la liste des clusters HDInsight dans votre abonnement Azure**
1. Ouvrez un espace de travail, puis connectez-vous à Azure. Pour en savoir plus, consultez les sections [Ouvrir un espace de travail HDInsight](#open-hdinsight-workspace) et [Connexion à Azure](#connect-to-azure).

2. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez **HDInsight: List Cluster** dans le menu contextuel. 

3. Les clusters Hive et Spark s’affichent dans le volet **Output**.

    ![Définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>Définir un cluster par défaut
1. Ouvrez un espace de travail et connectez-vous à Azure. Consultez [Ouvrir un espace de travail HDInsight](#open-hdinsight-workspace) et [Connexion à Azure](#connect-to-azure).

2. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight: Set Default Cluster**. 

3. Sélectionnez un cluster à utiliser comme cluster par défaut pour le fichier de script actuel. Le fichier de configuration **XXXX_hdi_settings.json** est automatiquement mis à jour. 

   ![Définir la configuration du cluster par défaut](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>Configurer l’environnement Azure 
1. Ouvrez la palette de commandes en appuyant sur **CTRL+MAJ+P**.

2. Entrez **HDInsight: Set Azure Environment**.

3. Sélectionnez Azure ou AzureChina comme entrée de connexion par défaut.

4. Entre temps, l’outil a déjà enregistré votre entrée de connexion par défaut dans **XXXX_hdi_settings.json**. Elle est également directement mise à jour dans ce fichier de configuration. 

   ![Définir la configuration de l’entrée de connexion par défaut](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>Envoyer des requêtes Hive interactives

HDInsight Tools pour VS Code vous permet d’envoyer des requêtes Hive interactives aux clusters Interactive Query HDInsight.

1. Créez un dossier de travail et un fichier de script Hive si vous n’en avez pas.

2. Connectez-vous à votre compte Azure, puis configurez le cluster par défaut si vous ne l’avez pas encore fait.

3. Copiez et collez le code suivant dans votre fichier Hive, puis enregistrez-le.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight: Hive Interactive** pour envoyer la requête. HDInsight Tools vous permet également d’envoyer un bloc de code au lieu du fichier de script entier à partir du menu contextuel. Peu après, le résultat de la requête s’affiche dans un nouvel onglet.

   ![Résultat de la requête Hive interactive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Volet **RESULTS** (RÉSULTATS): vous pouvez enregistrer le résultat complet dans un fichier CSV, JSON ou EXCEL dans un chemin d’accès local, ou enregistrer seulement certaines lignes du résultat.

    - Volet **MESSAGES** : cliquez sur un numéro de **ligne** pour accéder à la première ligne du script en cours d’exécution.

Une requête interactive est beaucoup plus rapide que [l’exécution d’un travail Hive de traitement par lots](#submit-hive-batch-scripts).

## <a name="submit-hive-batch-scripts"></a>Envoyer des scripts de commandes par lot Hive

1. Créez un dossier de travail et un fichier de script Hive si vous n’en avez pas.

2. Connectez-vous à votre compte Azure, puis configurez le cluster par défaut si vous ne l’avez pas encore fait.

3. Copiez et collez le code suivant dans votre fichier Hive, puis enregistrez-le.

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight: Hive Batch** pour envoyer une tâche Hive. 

4. Sélectionnez le cluster vers lequel vous souhaitez effectuer l’envoi.  

    Après l’envoi du travail Hive, les informations sur la réussite de l’envoi et l’ID du travail s’affichent dans le panneau **OUTPUT** (SORTIE). Le travail Hive ouvre également l’élément **WEB BROWSER** (NAVIGATEUR WEB), qui affiche les journaux et l’état en temps réel du travail.

   ![résultat de l’envoi du travail Hive](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

L’[envoi de requêtes Hive interactives](#submit-interactive-hive-queries) prend bien moins de temps que l’envoi d’un travail de traitement par lots.

## <a name="submit-interactive-pyspark-queries"></a>Envoyer des requêtes PySpark interactives
HDInsight Tools pour VS Code vous permet également d’envoyer des requêtes PySpark interactives aux clusters Spark.
1. Créez un dossier de travail et un fichier de script avec l’extension .py si vous n’en avez pas.

2. Connectez-vous à votre compte Azure, si ce n’est déjà fait.

3. Copiez et collez le code suivant dans le fichier de script :
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. Mettez en surbrillance ces scripts. Cliquez ensuite avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight: PySpark Interactive**.

5. Si vous n’avez pas encore installé l’extension **Python** dans VS Code, sélectionnez le bouton **Install** (Installer) tel qu’indiqué dans l’illustration suivante :

    ![installation de Python avec HDInsight pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. Installez l’environnement Python dans votre système, si ce n’est déjà fait. 
   - Pour Windows, téléchargez et installez [Python](https://www.python.org/downloads/). Vérifiez ensuite la présence de `Python` et `pip` dans votre chemin système.

   - Pour les instructions relatives à MacOS et Linux, consultez [Définir l’environnement interactif PySpark pour Visual Studio Code](set-up-pyspark-interactive-environment.md).

7. Sélectionnez un cluster auquel envoyer la requête PySpark. Peu après, le résultat de la requête s’affiche sous le nouvel onglet à droite :

   ![Résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. L’outil prend également en charge les requêtes avec la **Clause SQL**.

   ![Résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png) L’état de l’envoi s’affiche en bas à gauche dans la barre d’état lors de l’exécution des requêtes. N’envoyez pas d’autres requêtes lorsque l’état est **PySpark Kernel (busy)**. 

>[!NOTE]
>Les clusters peuvent mettre à jour les informations de session. La variable et la fonction définies, ainsi que les valeurs correspondantes sont conservées dans la session, ce qui permet de les référencer dans plusieurs appels de service pour le même cluster. 
 

## <a name="submit-pyspark-batch-job"></a>Envoi de la tâche de traitement par lots PySpark

1. Créez un dossier de travail et un fichier de script avec l’extension .py si vous n’en avez pas.

2. Connectez-vous à votre compte Microsoft Azure, si ce n’est pas encore fait.

3. Copiez et collez le code suivant dans le fichier de script :

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. Cliquez avec le bouton droit sur l’éditeur de script, puis cliquez sur **HDInsight: PySpark Batch**. 

5. Sélectionnez un cluster auquel envoyer votre travail PySpark. 

   ![Résultat de l’envoi du travail Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

Une fois que vous avez envoyé un travail Python, les journaux d’envoi apparaissent dans la fenêtre **Output** (Sortie) dans VS Code. **L’URL de l’interface utilisateur Spark** et **l’URL de l’interface utilisateur Yarn** s’affichent également. Vous pouvez ouvrir l’URL dans un navigateur web pour suivre l’état du travail.


   


## <a name="additional-features"></a>Fonctionnalités supplémentaires

HDInsight pour VS Code prend en charge les fonctionnalités suivantes :

- **Saisie semi-automatique IntelliSense**. Fenêtre contextuelle de suggestions de mots-clés, de méthodes, de variables, etc. Des icônes différentes représentent des types d’objets différents.

    ![types d’objets IntelliSense dans HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **Marqueur d’erreurs IntelliSense**. Le service de langage souligne les erreurs de saisie dans le script Hive.     
- **Coloration syntaxique**. Le service de langage utilise plusieurs couleurs pour différencier les variables, les mots-clés, le type des données, les fonctions, etc. 

    ![coloration syntaxique dans HDInsight Tools pour Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>Étapes suivantes

### <a name="demo"></a>Démonstration
* HDInsight pour VS Code : [vidéo](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Outils et extensions

* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Visualiser des données Hive à l’aide de Microsoft Power BI dans Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Visualiser des données Interactive Query Hive à l’aide de Power BI dans Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [ Définir l’environnement interactif de PySpark pour Visual Studio Code](set-up-pyspark-interactive-environment.md)
* [Utiliser Zeppelin pour exécuter des requêtes Hive dans Azure HDInsight](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](spark/apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>Créer et exécuter des applications
* [Créer une application autonome avec Scala](spark/apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](spark/apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](spark/apache-spark-job-debugging.md)



