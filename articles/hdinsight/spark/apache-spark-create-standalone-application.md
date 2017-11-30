---
title: "Créer une application Scala à exécuter sur des clusters Spark - HDInsight Azure | Microsoft Docs"
description: "Créez une application Spark écrite en Scala avec Apache Maven comme système de génération et un archétype Maven existant pour Scala fourni par IntelliJ IDEA."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: b2467a40-a340-4b80-bb00-f2c3339db57b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 24becbf5f539ac031d6f5b217a4c5a6b59f572f8
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="create-a-scala-maven-application-to-run-on-apache-spark-cluster-on-hdinsight"></a>Créer une application Scala Maven à exécuter sur un cluster Apache Spark sur HDInsight

Découvrez comment créer une application Spark écrite en Scala à l’aide de Maven avec IntelliJ IDEA. Dans cet article, Apache Maven est utilisé comme système de génération. Le document présente dans un premier temps un archétype Maven existant pour Scala fourni par IntelliJ IDEA.  La création d’une application Scala avec IntelliJ IDEA implique les étapes suivantes :

* Utiliser Maven en tant que le système de génération.
* Mettre à jour le fichier de modèle objet du projet (POM) pour résoudre les dépendances de module Spark.
* Écrire votre application dans Scala.
* Générer un fichier jar qui peut être soumis à des clusters HDInsight Spark.
* Exécuter l’application à distance sur le cluster Spark à l’aide de Livy.

> [!NOTE]
> HDInsight propose également un outil de plug-in IntelliJ IDEA pour faciliter le processus de création et d’envoi des applications à un cluster HDInsight Spark sous Linux. Pour plus d’informations, consultez [Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA afin de créer et d’envoyer des applications Spark](apache-spark-intellij-tool-plugin.md).
> 
> 

## <a name="prerequisites"></a>Composants requis

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Kit de développement logiciel (SDK) Oracle Java. Vous pouvez l’installer à partir d’ [ici](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* IDE Java. Cet article utilise IntelliJ IDEA 15.0.1. Vous pouvez l’installer à partir d’ [ici](https://www.jetbrains.com/idea/download/).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installer le plug-in Scala pour IntelliJ IDEA
Si l’installation d’IntelliJ IDEA ne vous invite pas à activer le plug-in Scala, lancez IntelliJ IDEA et parcourez les étapes suivantes pour installer le plug-in :

1. Démarrez IntelliJ IDEA, puis à partir de l’écran d’accueil, cliquez sur **Configurer**, puis sur **Plug-ins**.
   
    ![Activer le plug-in Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)
2. Dans l'écran suivant, cliquez sur **Installer le plug-in JetBrains** dans le coin inférieur gauche. Dans la boîte de dialogue **Parcourir les plug-ins JetBrains** qui s’ouvre, recherchez Scala, puis cliquez sur **Installer**.
   
    ![Installer le plug-in Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)
3. Une fois que le plug-in est correctement installé, cliquez sur bouton **Redémarrer IntelliJ IDEA** pour redémarrer l'IDE.

## <a name="create-a-standalone-scala-project"></a>Créer un programme Scala autonome
1. Lancez IntelliJ IDEA et créez un nouveau projet. Dans la boîte de dialogue Nouveau projet, choisissez les options suivantes, puis cliquez sur **Suivant**.
   
    ![Créer un projet Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)
   
   * Sélectionnez **Maven** comme type de projet.
   * Spécifiez un **projet SDK**. Cliquez sur Nouveau, puis accédez au répertoire d'installation Java, généralement `C:\Program Files\Java\jdk1.8.0_66`.
   * Sélectionnez l’option **Créer à partir d'un archétype** .
   * Dans la liste des archétypes, sélectionnez **org.scala-tools.archetypes:scala-archetype-simple**. Cela créera la structure de répertoire appropriée et téléchargera les dépendances requises par défaut pour écrire le programme Scala.
2. Indiquez les valeurs appropriées pour **GroupId**, **ArtifactId** et **Version**. Cliquez sur **Next**.
3. Dans la boîte de dialogue suivante, où vous spécifiez le répertoire de base Maven et d'autres paramètres utilisateur, acceptez les valeurs par défaut et cliquez sur **Suivant**.
4. Dans la dernière boîte de dialogue, spécifiez un nom de projet et un emplacement, puis cliquez sur **Terminer**.
5. Supprimez le fichier **MySpec.Scala** dans **src\test\scala\com\microsoft\spark\example**. Ce fichier est inutile pour l'application.
6. Si nécessaire, renommez les fichiers source et de test nommés par défaut. Dans le volet gauche d’IntelliJ IDEA, accédez à **src\main\scala\com.microsoft.spark.example**. Cliquez avec le bouton droit sur **App.scala**, puis cliquez sur **Refactoriser** et sur Renommer un fichier. Dans la boîte de dialogue, indiquez le nouveau nom de l’application, puis cliquez sur **Refactoriser**.
   
    ![Renommer les fichiers](./media/apache-spark-create-standalone-application/rename-scala-files.png)  
7. Dans les étapes suivantes, vous allez mettre à jour le fichier pom.xml pour définir les dépendances de l'application Spark Scala. Ces dépendances sont téléchargées et résolues automatiquement, c’est pourquoi vous devez configurer Maven en conséquence.
   
    ![Configurer Maven pour les téléchargements automatiques](./media/apache-spark-create-standalone-application/configure-maven.png)
   
   1. Dans le menu **Fichier**, cliquez sur **Paramètres**.
   2. Dans la boîte de dialogue **Paramètres**, accédez à **Génération, exécution, déploiement** > **Outils de génération** > **Maven** > **Importation**.
   3. Sélectionnez l'option **Importer les projets Maven automatiquement**.
   4. Cliquez sur **Appliquer**, puis sur **OK**.
8. Mettez à jour le fichier source Scala pour inclure le code de votre application. Ouvrez et remplacez le code existant par le code suivant et enregistrez les modifications. Ce code lit les données du fichier HVAC.csv (disponible sur tous les clusters HDInsight Spark), récupère les lignes qui contiennent uniquement un chiffre dans la sixième colonne et écrit la sortie dans **/HVACOut** , sous le conteneur de stockage par défaut du cluster.
   
        package com.microsoft.spark.example
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        /**
          * Test IO to wasb
          */
        object WasbIOTest {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACout")
          }
        }
9. Mettez à jour le fichier pom.xml.
   
   1. Ajoutez le bloc de code suivant à `<project>\<properties>` :
      
          <scala.version>2.10.4</scala.version>
          <scala.compat.version>2.10.4</scala.compat.version>
          <scala.binary.version>2.10</scala.binary.version>
   2. Ajoutez le bloc de code suivant à `<project>\<dependencies>` :
      
           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>1.4.1</version>
           </dependency>
      
      Enregistrez les modifications apportées au fichier pom.xml.
10. Créez le fichier .jar IntelliJ IDEA permet de créer le fichier JAR en tant qu’artefact d'un projet. Procédez comme suit.
    
    1. Dans le menu **Fichier**, cliquez sur **Structure de projet**.
    2. Dans la boîte de dialogue **Structure de projet**, cliquez sur **Artefacts**, puis sur le signe plus. Dans la boîte de dialogue contextuelle, cliquez sur **JAR**, puis sur **À partir de modules ayant des dépendances**.
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/create-jar-1.png)
    3. Dans la boîte de dialogue **Créer un fichier JAR à partir de modules**, cliquez sur le bouton de sélection (![ellipse](./media/apache-spark-create-standalone-application/ellipsis.png)) en regard de **Classe principale**.
    4. Dans la boîte de dialogue **Sélectionner une classe principale**, sélectionnez la classe qui s’affiche par défaut, puis cliquez sur **OK**.
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/create-jar-2.png)
    5. Dans la boîte de dialogue **Create JAR from Modules** (Créer un fichier JAR à partir de modules), assurez-vous que l’option **Extract to the target JAR** (Extraire vers le fichier JAR cible) est activée, puis cliquez sur **OK** (OK). Cela crée un fichier JAR contenant toutes les dépendances.
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/create-jar-3.png)
    6. L’onglet Disposition de la sortie répertorie tous les fichiers JAR inclus dans le cadre du projet Maven. Vous pouvez sélectionner et supprimer ceux sur lesquels l’application Scala n’a aucune dépendance directe. Pour l’application que nous créons ici, vous pouvez tous les supprimer sauf le dernier (**SparkSimpleApp compile output**). Sélectionnez les fichiers JAR à supprimer, puis cliquez sur l’icône **Delete** .
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/delete-output-jars.png)
       
        Assurez-vous que la case à cocher **Générer à la création** est activée, ce qui garantit la création du fichier JAR à chaque génération ou mise à jour du projet. Cliquez sur **Appliquer**, puis sur **OK**.
    7. Dans la barre de menus, cliquez sur **Générer**, puis sur **Créer le projet**. Vous pouvez également cliquer sur **Générer les artefacts** pour créer le fichier JAR. Le fichier JAR de sortie est créé sous **\out\artifacts**.
       
        ![Créer un fichier jar](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-spark-cluster"></a>Exécuter l’application sur le cluster Spark
Pour exécuter l'application sur le cluster, procédez comme suit :

* **Copiez le fichier JAR d'application sur l'objet blob Azure Storage** associé au cluster. Pour ce faire, vous pouvez utiliser l’utilitaire de ligne de commande [**AzCopy**](../../storage/common/storage-use-azcopy.md). De nombreux autres clients permettent également de télécharger des données. Pour en savoir plus à leur sujet, consultez [Téléchargement de données pour les travaux Hadoop dans HDInsight](../hdinsight-upload-data.md).
* **Utilisez Livy pour soumettre une tâche de l'application à distance** au cluster Spark. Les clusters Spark sur HDInsight incluent Livy qui expose des points de terminaison REST permettant d’envoyer à distance des travaux Spark. Pour plus d’informations, consultez [Envoi de travaux Spark à distance en utilisant Livy avec les clusters Spark sur HDInsight](apache-spark-livy-rest-interface.md).

## <a name="next-step"></a>Étape suivante

Dans cet article, vous avez appris comment créer une application Spark Scala. Passez à l’article suivant pour savoir comment exécuter cette application sur un cluster HDInsight Spark en utilisant Livy.

> [!div class="nextstepaction"]
>[Exécution de travaux à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

