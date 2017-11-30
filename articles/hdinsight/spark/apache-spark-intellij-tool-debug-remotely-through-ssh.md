---
title: "Kit de ressources Azure pour IntelliJ : déboguer des applications Spark à distance via SSH | Microsoft Docs"
description: "Obtenez des instructions étape par étape sur la façon d’utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications à distance sur des clusters HDInsight via SSH"
keywords: "déboguer à distance intellij, débogage à distance intellij, ssh, intellij, hdinsight, déboguer intellij, débogage"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: 6f9259ae5e8f382c6714d468004624c2cbcbbc33
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Déboguez des applications Spark localement ou à distance sur un cluster HDInsight avec le kit de ressources Azure pour IntelliJ via SSH

Cet article propose des instructions étape par étape sur la façon d’utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ pour déboguer des applications à distance sur un cluster HDInsight. Pour déboguer votre projet, vous pouvez également regarder la vidéo [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Déboguer des applications HDInsight Spark avec le kit de ressources Azure pour IntelliJ).

**Configuration requise**
* **Outils HDInsight dans le kit de ressources Azure pour IntelliJ**. Cet outil fait partie du kit de ressources Azure pour IntelliJ. Pour plus d’informations, consultez [Installation du kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). Et le **kit de ressources Azure pour IntelliJ**. Utilisez ce kit de ressources pour créer des applications Spark pour un cluster HDInsight. Pour plus d’informations, veuillez suivre les instructions de la rubrique [Utiliser le kit de ressources Azure pour IntelliJ pour créer des applications Spark pour un cluster HDInsight](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **Service HDInsight SSH avec gestion de nom d’utilisateur et de mot de passe**. Pour plus d’informations consultez la rubrique [Connexion à HDInsight (Hadoop) à l’aide de SSH](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) et [Utiliser le tunneling SSH pour accéder à l’IU web Ambari, JobHistory, NameNode, Oozie et à d’autres IU web](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Découvrez comment effectuer une exécution et un débogage locaux
### <a name="scenario-1-create-a-spark-scala-application"></a>Scénario 1 : Créer une application Spark Scala 

1. Démarrez IntelliJ IDEA et créez un projet. Dans la boîte de dialogue **Nouveau projet** , procédez comme suit :

   a. Sélectionnez **HDInsight**. 

   b. Sélectionnez un modèle Java ou Scala en fonction de vos préférences. Sélectionnez entre les options suivantes :

      - **Spark sur HDInsight (Scala)**

      - **Spark sur HDInsight (Java)**

      - **Spark sur HDInsight - Exemple (Scala)**

      Cet exemple utilise un modèle **Spark sur HDInsight - Exemple (Scala)**.

   c. Dans la liste des **outils de génération**, sélectionnez l’une des options suivantes, en fonction de vos besoins :

      - **Maven**, pour la prise en charge de l’Assistant de création de projets Scala

      -  **SBT**, pour gérer les dépendances et la génération du projet Scala 

      ![Créer un projet de débogage](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Sélectionnez **Suivant**.     
 
2. Dans la fenêtre **Nouveau projet** qui s’ouvre, effectuez les opérations suivantes :

   ![Sélectionner le SDK Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Entrez un nom de projet et un emplacement de projet.

   b. Dans la liste déroulante **Kit de développement logiciel (SDK) de projet**, sélectionnez **Java 1.8** pour le cluster **Spark 2.x** ou **Java 1.7** pour le cluster **Spark 1.x**.

   c. Dans la liste déroulante **Version Spark**, l’assistant de création de projets Scala intègre la version correcte pour le SDK Spark et le SDK Scala. Si la version du cluster spark est antérieure à la version 2.0, sélectionnez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x.** La version utilisée dans cet exemple est **Spark 2.0.2 (Scala 2.11.8)**.

   d. Sélectionnez **Terminer**.

3. Sélectionnez **src** > **principal** > **scala** pour ouvrir votre code dans le projet. Cet exemple utilise le script **SparkCore_wasbloTest**.

### <a name="prerequisite-for-windows"></a>Configuration requise pour Windows
Quand vous exécutez l’application Spark Scala locale sur un ordinateur Windows, vous pouvez obtenir une exception, comme l’explique le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356). Cette exception est liée à l’absence du fichier WinUtils.exe sur Windows. 

Pour résoudre cette erreur, [téléchargez le fichier exécutable](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) vers un emplacement tel que **C:\WinUtils\bin**. Ajoutez ensuite la variable d’environnement **HADOOP_HOME** et définissez la valeur de la variable sur **C:\WinUtils**.

### <a name="scenario-2-perform-local-run"></a>Scénario 2 : Effectuer une exécution locale
1. Ouvrez le script **SparkCore_wasbloTest**, cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez l’option **Exécuter '[travail Spark] XXX'** pour effectuer l’exécution locale.
2. Une fois l’exécution locale terminée, vous pouvez voir le fichier de sortie enregistré dans l’Explorateur de votre projet actuel **data** > **__default__**.

    ![Résultat de l’exécution locale](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. Nos outils définissent la configuration de l’exécution locale par défaut automatiquement lorsque vous effectuez l’exécution et le débogage locaux. Ouvrez le **[travail Spark] XXX** de la configuration dans le coin supérieur droit ; vous pouvez voir le **[travail Spark] XXX** déjà créé sous **Azure HDInsight Spark Job (Travail Spark Azure HDInsight)**. Basculez vers l’onglet **Locally Run (Exécuter localement)**.

    ![Configuration de l’exécution locale](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Variables d’environnement](#prerequisite-for-windows) : si vous avez déjà défini la variable d’environnement système **HADOOP_HOME** sur **C:\WinUtils**, elle peut détecter automatiquement qu’aucun ajout manuel n’est nécessaire.
    - [Emplacement de WinUtils.exe](#prerequisite-for-windows) : si vous n’avez pas défini la variable d’environnement système, vous pouvez trouver l’emplacement en cliquant sur son bouton.
    - Choisissez simplement l’une des deux options ; elles ne sont pas requises sur MacOS et Linux.
4. Vous pouvez également définir la configuration manuellement avant d’effectuer l’exécution et le débogage locaux. Dans la capture d’écran précédente, sélectionnez le signe plus (**+**). Puis sélectionnez l’option **Azure HDInsight Spark Job (Travail Spark Azure HDInsight)**. Entrez les informations de **Nom** et **Nom de la classe principale** à enregistrer, puis cliquez sur le bouton d’exécution locale.

### <a name="scenario-3-perform-local-debugging"></a>Scénario 3 : Effectuer un débogage local
1. Ouvrez le script **SparkCore_wasbloTest**, et définissez les points d’arrêt.
2. Cliquez avec le bouton droit sur l’éditeur de script, puis sélectionnez l’option **Déboguer '[travail Spark] XXX'** pour effectuer le débogage local.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Découvrez comment effectuer une exécution et un débogage à distance
### <a name="scenario-1-perform-remote-run"></a>Scénario 1 : effectuer une exécution à distance

1. Pour accéder au menu **Modifier les configurations**, sélectionnez l’icône dans le coin supérieur droit. Dans ce menu, vous pouvez créer ou modifier les configurations pour le débogage à distance.

   ![Modifier les configurations](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. Dans la boîte de dialogue **Run/Debug Configurations** (Exécuter/Déboguer les configurations) sélectionnez le signe plus (**+**). Puis sélectionnez l’option **Azure HDInsight Spark Job (Travail Spark Azure HDInsight)**.

   ![Ajouter une nouvelle configuration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. Basculez vers l’onglet **Remotely Run in Cluster (Exécuter à distance dans le cluster)**. Entrez les informations sur le **Nom**, le **Cluster Spark** et le **Nom principal de la classe**. Puis sélectionnez **Configuration avancée**. Nos outils prennent en charge le débogage avec **Exécuteurs**. Pour **numExectors**, la valeur par défaut est 5. Il est déconseillé de définir une valeur supérieure à 3.

   ![Exécuter le débogage des configurations](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. Dans la boîte de dialogue **Configuration avancée des soumissions Spark**, sélectionnez **Activer le débogage à distance Spark**. Entrez le nom d’utilisateur SSH, puis entrez un mot de passe ou utilisez un fichier de clé privée. Pour enregistrer la configuration, sélectionnez **OK**. Si vous souhaitez effectuer le débogage à distance, vous devez définir cette option. Par contre, cette action n’est pas nécessaire si vous souhaitez simplement utiliser l’exécution à distance.

   ![Activer le débogage à distance Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. La configuration est maintenant enregistrée avec le nom fourni. Pour afficher les détails de configuration, sélectionnez le nom de configuration. Pour apporter des modifications, sélectionnez **Modifier les configurations**. 

6. Une fois le paramétrage des configurations terminé, vous pouvez exécuter le projet sur le cluster à distance ou effectuer un débogage à distance.
   
   ![Bouton d’exécution à distance](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. Si ne souhaitez pas voir le journal d’exécution dans le volet droit, vous pouvez cliquer sur le bouton **Déconnecter**. Toutefois, il reste en cours d’exécution sur le serveur principal, et le résultat s’affichera dans le volet gauche.

   ![Bouton d’exécution à distance](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Scénario 2 : effectuer un débogage à distance
1. Configurez des points de rupture, puis sélectionnez l’icône **Débogage distant**. La différence avec la soumission à distance est que le nom d’utilisateur/mot de passe SSH doit être configuré.

   ![Sélectionner l’icône de débogage](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. Quand l’exécution du programme atteint le point de rupture, un onglet **Pilote** et deux onglets **Exécuteur** s’affichent dans le volet **Débogueur**. Sélectionnez l’icône **Reprendre le programme** pour continuer à exécuter le code ; le point de rupture suivant est alors atteint. Vous devez basculer vers l’onglet **Exécuteur** correct pour trouver l’exécuteur cible à déboguer. Vous pouvez consulter les journaux d’exécution sous l’onglet **Console** correspondant.

   ![Onglet Débogage](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Scénario 3 : effectuer un débogage à distance et une résolution des bogues
1. Configurez deux points de rupture, puis sélectionnez l’icône **Déboguer** pour démarrer le processus de débogage à distance.

2. Le code s’arrête au premier point de rupture et les informations sur les paramètres et les variables s’affichent dans le volet **Variable**. 

3. Sélectionnez l’icône **Reprendre le programme** pour continuer. Le code s’arrête au deuxième point. L’exception est interceptée comme prévu.

   ![Lever l’erreur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. Resélectionnez l’icône **Reprendre le programme**. La fenêtre **Soumission HDInsight Spark** affiche une erreur d’exécution de tâche.

   ![Soumission de l’erreur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. Pour mettre à jour dynamiquement la valeur de la variable à l’aide de la fonctionnalité de débogage IntelliJ, resélectionnez **Déboguer**. Le volet **Variables** réapparaît. 

6. Cliquez avec le bouton droit de la cible de l’onglet **Déboguer**, puis sélectionnez **Définir la valeur**. Ensuite, entrez une nouvelle valeur pour la variable. Puis sélectionnez **Entrer** pour enregistrer la valeur. 

   ![Définir la valeur](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. Sélectionnez l’icône **Reprendre le programme** pour continuer à exécuter le programme. Cette fois-ci, aucune exception n’est interceptée. Vous pouvez voir que le projet s’exécute correctement sans aucune exception.

   ![Déboguer sans exception](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Étapes suivantes
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Démonstration
* Créez un projet Scala (vidéo) : [Créer des applications Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Débogage à distance (vidéo) : [Utiliser le kit de ressources Azure pour IntelliJ afin de déboguer des applications Spark à distance sur un cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark : Utiliser Spark dans HDInsight pour créer des applications de diffusion en continu en temps réel](apache-spark-eventhub-streaming.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Créer et exécuter des applications
* [Créer une application autonome avec Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Exécuter des tâches à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser le kit de ressources Azure pour IntelliJ afin de créer des applications Spark pour un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans le cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
