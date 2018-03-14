---
title: "Kit de ressources Azure pour IntelliJ : déboguer des applications à distance dans Spark HDInsight | Microsoft Docs"
description: "Découvrez comment utiliser HDInsight Tools dans le kit de ressources Azure pour IntelliJ afin de déboguer à distance des applications Spark qui s’exécutent sur des clusters HDInsight via VPN."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 315a879368e522cc17eeff7be766f0c8a38d470b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance dans HDInsight via VPN

Nous vous recommandons de déboguer les applications spark à distance via SSH. Pour obtenir des instructions, reportez-vous à la rubrique [Déboguer des applications Spark à distance sur un cluster HDInsight avec le kit de ressources Azure pour IntelliJ via SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Cet article fournit des instructions pas à pas sur l’utilisation d’HDInsight Tools dans le kit de ressources Azure pour IntelliJ afin de soumettre un travail Spark sur un cluster Spark HDInsight et effectuer un débogage à distance à partir de votre poste de travail. Pour effectuer ces tâches, vous devez suivre les étapes générales suivantes :

1. Créer un réseau virtuel Azure de site à site ou de point à site. Les étapes décrites dans ce document supposent d’utiliser un réseau de site à site.
2. Créer dans HDInsight un cluster Spark faisant partie du réseau virtuel de site à site.
3. Vérifier la connectivité entre le nœud principal du cluster et votre poste de travail.
4. Créer une application Scala dans IntelliJ IDEA, puis la configurer pour le débogage à distance.
5. Exécuter et déboguer l’application.

## <a name="prerequisites"></a>Prérequis
* **Un abonnement Azure**. Pour plus d’informations, consultez [Get a free trial of Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/) (Obtenir un essai gratuit d’Azure).
* **Un cluster Apache Spark dans HDInsight**. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **SDK Oracle Java**. Vous pouvez l’installer à partir du [site web Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* **IntelliJ IDEA**. Cet article utilise la version 2017.1. Vous pouvez l’installer à partir du [site web JetBrains](https://www.jetbrains.com/idea/download/).
* **Outils HDInsight dans le kit de ressources Azure pour IntelliJ**. HDInsight Tools pour IntelliJ est disponible dans le cadre du kit de ressources Azure pour IntelliJ. Pour obtenir des instructions sur l’installation du kit de ressources Azure, consultez [Installer le kit de ressources Azure pour IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Connectez-vous à votre abonnement Azure à partir d’IntelliJ IDEA**. Suivez les instructions dans [Utiliser le kit de ressources Azure pour IntelliJ pour créer des applications Spark pour un cluster HDInsight](apache-spark-intellij-tool-plugin.md).
* **Solution de contournement pour une exception**. Quand l’application Spark Scala s’exécute pour le débogage à distance sur un ordinateur Windows, vous pouvez obtenir une exception. Cette exception est expliquée dans le document [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) et se produit en raison d’un fichier WinUtils.exe manquant dans Windows. Pour résoudre cette erreur, vous devez [télécharger le fichier exécutable](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) vers un emplacement tel que **C:\WinUtils\bin**. Ajoutez une variable d’environnement **HADOOP_HOME**, puis définissez la valeur de la variable sur **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Étape 1 : Créer un réseau virtuel Azure
Suivez les instructions contenues dans les liens suivants pour créer un réseau virtuel Azure, puis vérifiez la connectivité entre votre ordinateur de bureau et le réseau virtuel :

* [Créer un réseau virtuel avec une connexion VPN de site à site à l’aide du portail Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Créer un réseau virtuel avec une connexion VPN de site à site à l’aide de PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Configurer une connexion point à site à un réseau virtuel à l’aide de PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Étape 2 : créer un cluster Spark HDInsight
Nous vous recommandons également de créer dans Azure HDInsight un cluster Apache Spark faisant partie du réseau virtuel Azure que vous avez créé. Utilisez les informations disponibles dans l’article [Création de clusters Hadoop basés sur Linux dans HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Dans le cadre de la configuration facultative, sélectionnez le réseau virtuel Azure que vous avez créé à l’étape précédente.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Étape 3 : Vérifier la connectivité entre le nœud principal du cluster et votre poste de travail
1. Récupérez l’adresse IP du nœud principal. Ouvrez l’interface utilisateur Ambari du cluster. Dans le panneau du cluster, sélectionnez **Tableau de bord**.

    ![Sélectionner le Tableau de bord dans Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. À partir de l’interface utilisateur Ambari, sélectionnez **Hosts** (Hôtes).

    ![Sélectionner Hosts (Hôtes) dans Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. Vous obtenez une liste de nœuds principaux, de nœuds worker et de nœuds zookeeper. Les nœuds principaux ont le préfixe **hn**\*. Sélectionnez le premier nœud principal.

    ![Rechercher le nœud principal dans Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. Dans le volet **Résumé** en bas de la page qui s’ouvre, copiez **l’adresse IP** du nœud principal et le **nom d’hôte**.

    ![Rechercher l’adresse IP dans Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. Ajoutez l’adresse IP et le nom d’hôte du nœud principal au fichier **hosts** de l’ordinateur sur lequel vous souhaitez exécuter et déboguer à distance le travail Spark. Cela vous permet de communiquer avec le nœud principal à l’aide de l’adresse IP et du nom d’hôte.

   a. Ouvrez un fichier Bloc-notes avec des autorisations élevées. Dans le menu **Fichier**, sélectionnez **Ouvrir** et recherchez l’emplacement du fichier hosts. Sur un ordinateur Windows, l’emplacement est **C:\Windows\System32\Drivers\etc\hosts**.

   b. Ajoutez les informations suivantes au fichier **hosts** :

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. À partir de l’ordinateur que vous avez connecté au réseau virtuel Azure utilisé par le cluster HDInsight, vérifiez que vous pouvez exécuter une commande ping sur les nœuds principaux aussi bien avec l’adresse IP qu’avec le nom d’hôte.
7. Utilisez SSH pour vous connecter au nœud principal du cluster en suivant les instructions fournies dans la section [Connexion à un cluster HDInsight sous Linux](../hdinsight-hadoop-linux-use-ssh-unix.md). À partir du nœud principal du cluster, exécutez une commande ping sur l’adresse IP du poste de travail. Tester la connectivité aux deux adresses IP assignées à l’ordinateur :

    - Une pour la connexion réseau
    - Une pour le réseau virtuel Azure

8. Répétez ces étapes pour l’autre nœud principal.

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Étape 4 : Créer une application Spark Scala en utilisant HDInsight Tools dans le kit de ressources Azure pour IntelliJ et la configurer pour le débogage à distance
1. Ouvrez IntelliJ IDEA et créez un projet. Dans la boîte de dialogue **Nouveau projet** , procédez comme suit :

    ![Sélectionner le nouveau modèle de projet dans IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Sélectionnez **HDInsight** > **Spark sur HDInsight (Scala)**.

    b. Sélectionnez **Suivant**.
2. Dans la boîte de dialogue **Nouveau projet**, effectuez les opérations suivantes, puis sélectionnez **Terminer** :

    - Entrez un nom de projet et un emplacement.

    - Dans la liste déroulante **Kit de développement logiciel (SDK) de projet**, sélectionnez **Java 1.8** pour le cluster Spark 2.x ou **Java 1.7** pour le cluster Spark 1.x.

    - Dans la liste déroulante **Version Spark**, l’Assistant de création de projets Scala intègre la version correcte pour le SDK Spark et le SDK Scala. Si la version du cluster spark est antérieure à la version 2.0, sélectionnez **Spark 1.x**. Sinon, sélectionnez **Spark 2.x**. La version utilisée dans cet exemple est **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Sélectionner le SDK et la version Spark du projet](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
3. Le projet Spark crée automatiquement un artefact à votre intention. Pour afficher l’artefact, procédez comme suit :

    a. Dans le menu **Fichier**, sélectionnez **Structure de projet**.

    b. Dans la boîte de dialogue **Structure de projet**, sélectionnez **Artefacts** pour afficher l’artefact créé par défaut. Vous pouvez également créer votre propre artefact en sélectionnant le signe plus (**+**).

   ![Créer un fichier jar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


4. Ajoutez des bibliothèques à votre projet. Pour ajouter une bibliothèque, effectuez les opérations suivantes :

    a. Cliquez avec le bouton droit sur le nom du projet dans l’arborescence du projet, puis sélectionnez **Open Module Settings**(Ouvrir les paramètres du module). 

    b. Dans la boîte de dialogue **Project Structure** (Structure de projet), sélectionnez **Libraries** (Bibliothèques), sélectionnez le symbole (**+**), puis sélectionnez **From Maven**(À partir de Maven).

    ![Ajouter une bibliothèque](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. Dans la boîte de dialogue **Download Library from Maven Repository** (Télécharger la bibliothèque à partir du référentiel Maven), recherchez et ajoutez les bibliothèques suivantes :

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. Copiez `yarn-site.xml` et `core-site.xml` à partir du nœud principal du cluster et ajoutez-les au projet. Exécutez les commandes suivantes pour copier les fichiers. Vous pouvez utiliser [Cygwin](https://cygwin.com/install.html) pour exécuter les commandes `scp` suivantes afin de copier les fichiers à partir des nœuds principaux du cluster :

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Étant donné que nous avons déjà ajouté l’adresse IP et les noms d’hôtes des nœuds principaux du cluster pour le fichier hosts de notre ordinateur de bureau, nous pouvons utiliser les commandes `scp` de la manière suivante :

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Pour ajouter ces fichiers à votre projet, copiez-les dans le dossier **/src** dans l’arborescence de votre projet, par exemple `<your project directory>\src`.
6. Mettez à jour le fichier `core-site.xml` pour effectuer les modifications suivantes :

   a. Remplacez la clé chiffrée. Le fichier `core-site.xml` inclut la clé chiffrée du compte de stockage associé au cluster. Dans le fichier `core-site.xml` que vous avez ajouté au projet, remplacez la clé chiffrée par la clé de stockage réelle associée au compte de stockage par défaut. Pour plus d’informations, consultez [Gérer vos clés d’accès de stockage](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. Supprimez les entrées suivantes de `core-site.xml` :

           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>

           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>

           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   c. Enregistrez le fichier .
7. Ajoutez la classe principale pour votre application. Dans **l’Explorateur de projets**, cliquez avec le bouton droit sur **src**, pointez sur **Nouveau**, puis sélectionnez **Scala class** (Classe Scala).

    ![Sélectionner la classe principale](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
8. Dans la boîte de dialogue **Create New Scala Class** (Créer une classe Scala), indiquez un nom, dans la zone **Kind** (Genre), sélectionnez **Objet**, puis **OK**.

    ![Créer une classe Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
9. Collez le code suivant dans le fichier `MyClusterAppMain.scala` . Ce code crée le contexte Spark et ouvre une méthode `executeJob` à partir de l’objet `SparkSample`.

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasb:///HVACOut")
          }
        }

10. Répétez les étapes 8 et 9 pour ajouter un nouvel objet Scala appelé `*SparkSample`. Ajoutez le code suivant à cette classe. Ce code lit les données du fichier HVAC.csv (disponible dans tous les clusters HDInsight Spark). Il récupère les lignes qui contiennent uniquement un chiffre dans la septième colonne du fichier CSV, puis écrit la sortie dans **/HVACOut** sous le conteneur de stockage par défaut du cluster.

        import org.apache.spark.SparkContext

        object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)

           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)

           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }
        }
11. Répétez les étapes 8 et 9 pour ajouter une nouvelle classe appelée `RemoteClusterDebugging`. Cette classe implémente le framework de test Spark utilisée pour déboguer les applications. Ajoutez le code suivant à la classe `RemoteClusterDebugging` :

        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }

     Deux points importants sont à prendre en considération :

      * Pour `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, assurez-vous que le fichier JAR de l’assembly Spark est disponible sur le stockage de cluster dans le chemin d’accès spécifié.
      * Pour `setJars`, spécifiez l’emplacement où le fichier JAR de l’artefact est créé. En général, il s’agit du répertoire `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
12. Dans la classe `*RemoteClusterDebugging`, cliquez avec le bouton droit sur le mot-clé `test`, puis sélectionnez **Create RemoteClusterDebugging Configuration** (Créer une configuration RemoteClusterDebugging).

    ![Créer une configuration distante](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

13. Dans la boîte de dialogue **Create RemoteClusterDebugging Configuration** (Créer une configuration RemoteClusterDebugging), fournissez un nom pour la configuration, puis sélectionnez **Test kind**(Type de test) pour **Test name** (Nom de test). Conservez les autres valeurs par défaut. Sélectionnez **Apply** (Appliquer), puis **OK**.

    ![Ajouter les détails de configuration](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
14. Une liste déroulante **Remote run** (Exécution à distance) s’affiche maintenant dans la barre de menus.

    ![Liste déroulante Remote run (Exécution à distance)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Étape 5 : exécuter l’application en mode débogage
1. Dans votre projet IntelliJ IDEA, ouvrez `SparkSample.scala` et créez un point d’arrêt en regard de `val rdd1`. Dans le menu contextuel **Create Breakpoint for** (Créer un point d’arrêt pour), sélectionnez **line in function executeJob** (ligne dans la fonction executeJob).

    ![Ajouter un point d’arrêt](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. Pour exécuter l’application, cliquez sur le bouton **Debug Run** (Exécuter le débogage) situé en regard de la liste déroulante de configuration **Remote Run** (Exécution à distance).

    ![Sélectionner le bouton Debug Run (Exécuter le débogage)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. Quand l’exécution du programme atteint le point d’arrêt, un onglet **Debugger** (Débogueur) apparaît dans le volet inférieur.

    ![Afficher l’onglet Debugger (Débogueur)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. Pour ajouter un espion, sélectionnez l’icône (**+**).

    ![Sélectionner l’icône +](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    Dans cet exemple, l’application s’est arrêtée avant que la variable `rdd1` ne soit créée. À l’aide de cet espion, nous pouvons voir les cinq premières lignes dans la variable `rdd`. Sélectionnez **Enter** (Entrer).

    ![Exécuter le programme en mode débogage](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Dans l’image précédente, on constate que, au moment de l’exécution, il est possible d’interroger des téraoctets de données et de corriger la progression de votre application. Par exemple, dans la sortie illustrée ci-dessus, vous pouvez voir que la première ligne de la sortie est un en-tête. Sur la base de cette sortie, vous pouvez modifier votre code d’application pour ignorer la ligne d’en-tête, si nécessaire.
5. Vous pouvez maintenant sélectionner l’icône **Resume Program** (Reprendre le programme) pour poursuivre l’exécution de votre application.

    ![Sélectionner Resume Program (Reprendre le programme)](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. Si l’application se termine correctement, vous devez obtenir une sortie similaire à ce qui suit :

    ![Sortie de la console](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Étapes suivantes
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Démonstration
* Créez un projet Scala (vidéo) : [Créer des applications Scala Spark](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Débogage à distance (vidéo) : [Utiliser le kit de ressources Azure pour IntelliJ afin de déboguer des applications Spark à distance sur un cluster HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utiliser le kit de ressources Azure pour IntelliJ afin de créer des applications Spark pour un cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Utiliser le kit de ressources Azure pour IntelliJ pour déboguer des applications Spark à distance via SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Utiliser HDInsight Tools pour IntelliJ avec Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Utiliser HDInsight Tools dans le kit de ressources Azure pour Eclipse pour créer des applications Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Utiliser des blocs-notes Zeppelin avec un cluster Spark dans HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches qui s’exécutent sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
