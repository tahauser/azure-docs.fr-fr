---
title: 'Kit de ressources Azure pour Eclipse : créer des applications Scala pour HDInsight Spark | Microsoft Docs'
description: Utilisez HDInsight Tools dans le kit de ressources Azure pour Eclipse pour développer des applications Spark écrites en Scala et envoyez-les à un cluster HDInsight Spark, directement à partir de l’IDE Eclipse.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: nitinme
ms.openlocfilehash: 4ff67ebcbac5b1b85345421b16036c96f8e807cc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Utiliser le kit de ressources Azure pour Eclipse pour créer des applications Spark pour un cluster HDInsight

Utilisez HDInsight Tools du kit de ressources Azure pour Eclipse pour développer des applications Spark écrites en Scala et envoyez-les à un cluster Azure HDInsight Spark, directement à partir de l’environnement de développement intégré (IDE) Eclipse. Vous pouvez utiliser le plug-in HDInsight Tools de différentes manières :

* Pour développer une application Spark Scala et l’envoyer à un cluster HDInsight Spark
* Pour accéder à vos ressources de cluster Azure HDInsight Spark
* Pour développer et exécuter une application Spark Scala localement

> [!IMPORTANT]
> Vous pouvez utiliser cet outil pour créer des applications et les envoyer à un cluster HDInsight Spark sur Linux uniquement.
> 
> 

## <a name="prerequisites"></a>Prérequis


* Cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit version 8, qui est utilisé pour l’exécution d’IDE Eclipse. Vous pouvez le télécharger à partir du [site web Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IDE Eclipse. Cet article utilise Eclipse Neon. Vous pouvez l’installer à partir du [site web Eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Installer HDInsight Tools dans le kit de ressources Azure pour Eclipse et le plug-in Scala
### <a name="install-azure-toolkit-for-eclipse"></a>Installer le Kit de ressources Azure pour Eclipse
HDInsight Tools pour Eclipse est disponible dans le cadre du kit de ressources Azure pour Eclipse. Pour plus d’informations sur l’installation, voir [Installation du kit de ressources Azure pour Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).
### <a name="install-the-scala-plug-in"></a>Installer le plug-in Scala
Quand vous ouvrez Eclipse, HDInsight Tools détecte automatiquement si vous avez installé le plug-in Scala. Sélectionnez **OK** pour continuer, puis suivez les instructions pour installer le plug-in à partir de la Place de marché Eclipse.

![Installation automatique du plug-in Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Pour commencer, l’utilisateur peut soit [se connecter à un abonnement Azure](#Sign-in-to-your-Azure-subscription), soit [lier un cluster HDInsight](#Link-a-cluster) avec un identifiant joint à un domaine ou nom d’utilisateur/mot de passe Ambari. 

## <a name="sign-in-to-your-azure-subscription"></a>Connectez-vous à votre abonnement Azure :
1. Démarrez l’IDE Eclipse et ouvrez l’Explorateur Azure. Dans le menu **Window** (Fenêtre), sélectionnez **Show View** (Afficher la vue), puis **Other** (Autre). Dans la boîte de dialogue qui s’ouvre, développez **Azure**, sélectionnez **Explorateur Azure**, puis **OK**.

   ![Boîte de dialogue Affichage](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Cliquez avec le bouton droit sur le nœud **Azure**, puis sélectionnez **Se connecter**.
3. Dans la boîte de dialogue **Azure Sign in (Connexion à Azure)**, choisissez la méthode d’authentification, sélectionnez **Se connecter** et entrez vos informations d’identification Azure.
   
   ![Boîte de dialogue Connexion à Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Une fois que vous êtes connecté, la boîte de dialogue **Sélectionner des abonnements** répertorie tous les abonnements Azure associés aux informations d’identification. Cliquez sur **Sélectionner** pour fermer la boîte de dialogue.

   ![Boîte de dialogue Sélectionner des abonnements](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. Dans l’onglet **Explorateur Azure**, développez **HDInsight** pour afficher les clusters HDInsight Spark de votre abonnement.
   
   ![Clusters HDInsight Spark dans l’Explorateur Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Vous pouvez développer davantage un nœud de nom de cluster pour voir les ressources (par exemple, les comptes de stockage) associées au cluster.
   
   ![Développement d’un nom de cluster pour voir les ressources](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Lier un cluster
Vous pouvez lier un cluster normal à l’aide du nom d’utilisateur Ambari managé, mais également lier un cluster de sécurité Hadoop à l’aide du nom d’utilisateur de domaine (par exemple : user1@contoso.com).
1. Cliquez sur **Lier un cluster** dans **Azure Explorer**.

   ![menu contextuel de lien du cluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Entrez le **Nom du cluster**, le **Nom d’utilisateur** et le **Mot de passe**, puis cliquez sur le bouton OK pour lier le cluster. Si vous le souhaitez, entrez un compte de stockage, une clé de stockage, puis sélectionnez un conteneur de stockage pour que l’explorateur de stockage fonctionne avec l’arborescence de gauche.
   
   ![boîte de dialogue de lien du cluster](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]
   > Nous utilisons la clé de stockage liée, le nom d’utilisateur et le mot de passe si le cluster est à la fois connecté sur un abonnement Azure et lié à un cluster.
   > ![Explorateur de stockage dans Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

3. Vous pouvez voir un cluster lié dans le nœud **HDInsight** après avoir cliqué sur le bouton OK, si les informations d’entrée sont correctes. Vous pouvez désormais soumettre une application à ce cluster lié.

   ![cluster lié](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Vous pouvez également dissocier un cluster à partir de **Azure Explorer**.
   
   ![cluster non lié](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Configuration d’un projet Spark Scala pour un cluster HDInsight Spark

1. Dans l’espace de travail de l’IDE Eclipse, sélectionnez **File** (Fichier), **New** (Nouveau), puis **Project** (Projet). 
2. Dans l’Assistant New Project (Nouveau projet), développez **HDInsight**, sélectionnez **Spark on HDInsight (Scala)** (Spark sur HDInsight [Scala]), puis **Next** (Suivant).

   ![Sélection du projet Spark sur HDInsight (Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. L’Assistant de création de projets Scala détecte automatiquement si vous avez installé le plug-in Scala. Sélectionnez **OK** pour continuer le téléchargement du plug-in Scala, puis suivez les instructions pour redémarrer Eclipse.

   ![Vérification de Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. Dans la boîte de dialogue **New HDInsight Scala Project** (Nouveau projet HDInsight Scala), indiquez les valeurs suivantes, puis sélectionnez **Next** (Suivant) :
   * Entrez un nom pour le projet.
   * Dans la zone **JRE**, vérifiez que l’option **Use an execution environment JRE** (Utiliser un environnement d’exécution JRE) est définie sur **JavaSE-1.7** ou version ultérieure.
   * Dans la zone **Bibliothèque Spark**, vous pouvez choisir l’option **Utiliser Maven pour configurer le kit de développement logiciel (SDK) Spark**.  Notre outil intègre la version correcte pour les kits de développement logiciel (SDK) Spark et Scala. Vous pouvez aussi choisir l’option **Ajouter le kit de développement logiciel (SDK) Spark manuellement**. Téléchargez et ajoutez le kit de développement logiciel Spark manuellement.

   ![Boîte de dialogue New HDInsight Scala Project (Nouveau projet HDInsight Scala)](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. Dans la boîte de dialogue suivante, sélectionnez **Terminer**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Créer une application Scala pour un cluster HDInsight Spark

1. Dans l’IDE Eclipse, dans Package Explorer (Explorateur de packages), développez le projet créé précédemment, cliquez avec le bouton droit sur **src**, pointez sur **New** (Nouveau), puis sélectionnez **Other** (Autre).
2. Dans la boîte de dialogue **Select a wizard** (Sélectionner un Assistant), développez **Scala Wizards** (Assistants Scala), sélectionnez **Scala Object** (Objet Scala), puis **Next** (Suivant).
   
   ![Boîte de dialogue Select a wizard (Sélectionner un Assistant)](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. Dans la boîte de dialogue **Create New File** (Créer un fichier), entrez un nom pour l’objet, puis sélectionnez **Finish** (Terminer).
   
   ![Boîte de dialogue Create New File (Créer un fichier)](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. Collez le code suivant dans l’éditeur de texte :
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
5. Exécutez l’application sur un cluster HDInsight Spark :
   
   a. Dans Package Explorer (Explorateur de packages), cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Submit Spark Application to HDInsight** (Envoyer l’application Spark à HDInsight).        
   b. Dans la boîte de dialogue **Spark Submission** (Envoi Spark), entrez les valeurs suivantes, puis sélectionnez **Submit** (Envoyer) :
      
      * Pour **Cluster Name**(Nom du cluster), sélectionnez le cluster HDInsight Spark sur lequel vous souhaitez exécuter votre application.
      * Sélectionnez un artefact à partir du projet Eclipse ou choisissez-en un sur un disque dur. La valeur par défaut dépend de l’élément sur lequel vous cliquez avec le bouton droit dans l’Explorateur de package.
      * Dans la liste déroulante **Main class name** (Nom de la classe principale), l’Assistant Envoi affiche tous les noms des objets de votre projet. Sélectionnez-en un ou entrez-en un que vous souhaitez exécuter. Si vous avez sélectionné un artefact à partir d’un disque dur, vous devez entrer le nom de la classe principale manuellement. 
      * Étant donné que le code d’application dans cet exemple n’exige aucun argument de ligne de commande et qu’il ne référence pas de fichiers JAR ou d’autres fichiers, vous pouvez laisser les autres zones de texte vides.
        
      ![Boîte de dialogue Spark Submission (Envoi Spark)](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. L’onglet **Spark Submission** (Envoi Spark) doit commencer à afficher la progression. Vous pouvez arrêter l’application en sélectionnant le bouton rouge dans la fenêtre **Spark Submission** (Envoi Spark). Vous pouvez également afficher les journaux pour cette exécution d’application spécifique en sélectionnant l’icône en forme de globe (indiquée par la zone bleue dans l’image).
      
   ![Fenêtre Spark Submission (Envoi Spark)](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Accéder aux clusters HDInsight Spark et les gérer à l’aide de HDInsight Tools dans le kit de ressources Azure pour Eclipse
Vous pouvez effectuer diverses opérations à l’aide de HDInsight Tools, y compris en accédant à la sortie du travail.

### <a name="access-the-job-view"></a>Accéder à la vue des travaux
1. Dans Azure Explorer, développez **HDInsight**, développez le nom du cluster Spark, puis sélectionnez **Travaux**. 

   ![Nœud Vue de la tâche](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

2. Sélectionnez le nœud **Travaux**. Si une version Java est antérieure à la version **1.8**, HDInsight Tools vous rappelle automatiquement d’installer le plug-in **E(fx)clipse**. Sélectionnez **OK** pour continuer, puis suivez l’assistant d’installation pour l’installer depuis la Place de marché Eclipse et redémarrer Eclipse. 

   ![Installer E(fx)clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. Ouvrez la vue des travaux à partir du nœud **Tâches**. Dans le volet droit, l’onglet **Spark Job View** (Affichage des travaux Spark) affiche toutes les applications qui ont été exécutées sur le cluster. Sélectionnez le nom de l’application pour laquelle vous souhaitez afficher plus de détails.

   ![Détails de l’application](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Vous pouvez alors effectuer une des actions suivantes :

   * Placez le curseur sur le graphe du travail. Il affiche des informations de base sur le travail en cours d’exécution. Sélectionnez le graphe de travail ; vous pouvez voir alors les étapes et les informations générées par chaque travail.

     ![Détails des étapes du travail](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Sélectionnez l’onglet **Journal** pour afficher les journaux fréquemment utilisés, notamment les journaux **Driver Stderr**, **Driver Stdout** et **Directory Info**.

     ![Détails des journaux](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Ouvrez l’interface utilisateur de l’historique Spark et l’interface utilisateur YARN (au niveau de l’application) en sélectionnant les liens hypertexte en haut de la fenêtre.

### <a name="access-the-storage-container-for-the-cluster"></a>Accéder au conteneur de stockage du cluster
1. Dans l’Explorateur Azure, développez le nœud racine **HDInsight** pour afficher la liste des clusters HDInsight Spark disponibles.
2. Développez le nom de cluster pour voir le compte de stockage et le conteneur de stockage par défaut du cluster.
   
   ![Compte de stockage et conteneur de stockage par défaut](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Sélectionnez le nom du conteneur de stockage associé au cluster. Dans le volet droit, double-cliquez sur le dossier **HVACOut**. Ouvrez l’un des fichiers **part-** pour afficher la sortie de l’application.

### <a name="access-the-spark-history-server"></a>Accéder au serveur d’historique Spark
1. Dans l’Explorateur Azure, cliquez avec le bouton droit sur le nom de votre cluster Spark, puis sélectionnez **Open Spark History UI** (Ouvrir l’interface utilisateur de l’historique Spark). Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous les avez spécifiées au moment de l’approvisionnement du cluster.
2. Dans le tableau de bord du serveur d’historique Spark, utilisez le nom de l’application pour rechercher l’application que vous venez d’exécuter. Dans le code précédent, vous définissez le nom de l’application en utilisant `val conf = new SparkConf().setAppName("MyClusterApp")`. Le nom de votre application Spark était donc **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Démarrer le portail Ambari
1. Dans l’Explorateur Azure, cliquez avec le bouton droit sur le nom de votre cluster Spark, puis sélectionnez **Open Cluster Management Portal (Ambari)** (Ouvrir le portail de gestion des clusters [Ambari]). 
2. Lorsque vous y êtes invité, entrez les informations d’identification d’administrateur pour le cluster. Vous les avez spécifiées au moment de l’approvisionnement du cluster.

### <a name="manage-azure-subscriptions"></a>Gérer les abonnements Azure
Par défaut, HDInsight Tools du kit de ressources Azure pour Eclipse répertorie les clusters Spark de tous vos abonnements Azure. Si nécessaire, vous pouvez spécifier les abonnements pour lesquels vous souhaitez accéder au cluster. 

1. Dans Azure Explorer, cliquez avec le bouton droit sur le nœud racine **Azure**, puis sélectionnez **Gérer les abonnements**. 
2. Dans la boîte de dialogue, décochez les cases concernant l’abonnement auquel vous ne souhaitez pas accéder, puis sélectionnez **Fermer**. Vous pouvez également sélectionner **Se déconnecter** si vous souhaitez vous déconnecter de votre abonnement Azure.

## <a name="run-a-spark-scala-application-locally"></a>Exécuter une application Spark Scala localement
Vous pouvez utiliser HDInsight Tools du kit de ressources Azure pour Eclipse pour exécuter des applications Spark Scala localement sur votre poste de travail. En règle générale, ces applications n’ont pas besoin d’accéder aux ressources de cluster telles que le conteneur de stockage, et elles peuvent être exécutées et testées localement.

### <a name="prerequisite"></a>Configuration requise
Quand vous exécutez l’application Spark Scala locale sur un ordinateur Windows, vous pouvez obtenir une exception, comme l’explique le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Cette exception est liée à l’absence du fichier **WinUtils.exe** dans Windows. 

Pour résoudre cette erreur, vous devez [télécharger le fichier exécutable ici](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) vers un emplacement tel que **C:\WinUtils\bin**, puis ajouter une variable d’environnement **HADOOP_HOME** et définir la valeur de la variable sur **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Exécuter une application Spark Scala locale
1. Démarrez Eclipse et créez un projet. Dans la boîte de dialogue **New Project** (Nouveau projet), choisissez les options suivantes, puis sélectionnez **Next** (Suivant).
   
   * Dans le volet gauche, sélectionnez **HDInsight**.
   * Dans le volet droit, sélectionnez **Spark on HDInsight Local Run Sample (Scala)**(Exemple d’exécution locale de Spark sur HDInsight [Scala]).

   ![Boîte de dialogue Nouveau projet](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. Pour fournir les détails du projet, suivez les étapes 3 à 6 indiquées dans la section précédente [Configuration d’un projet Spark Scala pour un cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

3. Le modèle ajoute un exemple de code (**LogQuery**) sous le dossier **src** que vous pouvez exécuter localement sur votre ordinateur.
   
   ![Emplacement de LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
4. Cliquez avec le bouton droit sur l’application **LogQuery**, pointez sur **Run As** (Exécuter en tant que), puis sélectionnez **1 Scala Application**. Un résultat ressemblant à ce qui suit apparaît sous l’onglet **Console** :
   
   ![Résultat de l’exécution locale de l’application Spark](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Problèmes connus
Pour soumettre une application à Azure Data Lake Store, sélectionnez le mode **interactif** pendant le processus de connexion à Azure. Si vous sélectionnez le mode **automatique**, vous pouvez obtenir une erreur.

![Connexion interactive](./media/apache-spark-eclipse-tool-plugin/interactive-authentication.png)

Vous pouvez choisir un cluster Azure Data Lake pour soumettre votre application avec n’importe quelle méthode de connexion.

Pour l’instant, la visualisation directe des sorties Spark n’est pas prise en charge.

## <a name="feedback"></a>Commentaires
Si vous avez des commentaires, ou que vous rencontrez d’autres problèmes pendant l’utilisation de cet outil, envoyez-nous un e-mail à l’adresse hdivstool@microsoft.com.

## <a name="seealso"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser le kit de ressources Azure pour IntelliJ pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)

