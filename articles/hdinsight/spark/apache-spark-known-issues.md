---
title: "Résolution des problèmes liés au cluster Apache Spark dans Azure HDInsight | Microsoft Docs"
description: "En savoir plus sur les problèmes liés aux clusters Apache Spark dans Azure HDInsight et comment les résoudre."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: de7847055c00fe9d0d1cc08cf5ba5d2ab54a9fc0
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Problèmes connus du cluster Apache Spark sur Azure HDInsight

Ce document fait le suivi de tous les problèmes connus pour la version Preview publique de HDInsight Spark.  

## <a name="livy-leaks-interactive-session"></a>Livy divulgue une session interactive
Lorsque Livy est redémarré (à partir d’Ambari ou à cause d’un redémarrage de la machine virtuelle du nœud principal 0) avec une session interactive encore active, une session de travail interactive est divulguée. Pour cette raison, de nouvelles tâches peuvent rester bloquées à l’état Accepté sans pouvoir être démarrées.

**Atténuation :**

Pour contourner ce problème, suivez la procédure ci-dessous :

1. SSH dans le nœud principal. Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Exécutez la commande suivante pour rechercher l’ID d’application des tâches interactives démarrées via Livy. 
   
        yarn application –list
   
    Par défaut, le nom attribué à une tâche est « Livy » si celle-ci a été démarrée avec une session interactive Livy, sans qu’un nom ait été explicitement spécifié. Pour une session Livy démarrée par le Bloc-notes Jupyter, le nom de la tâche commence par remotesparkmagics_*. 
3. Exécutez la commande suivante pour mettre fin à ces tâches. 
   
        yarn application –kill <Application ID>

L’exécution des nouvelles tâches commence alors. 

## <a name="spark-history-server-not-started"></a>Serveur d’historique Spark non démarré
Le serveur d’historique Spark ne démarre pas automatiquement après la création d’un cluster.  

**Atténuation :** 

Démarrez manuellement le serveur d’historique à partir d’Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problème d’autorisation dans le répertoire des journaux Spark
Lorsque hdiuser soumet une tâche avec spark-submit, il existe une erreur java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Autorisation refusée) et le journal du pilote n’est pas écrit. 

**Atténuation :**

1. Ajoutez hdiuser au groupe Hadoop. 
2. Fournissez les autorisations 777 sur /var/log/spark après la création du cluster. 
3. Mettez à jour l’emplacement du journal Spark à l’aide d’Ambari pour obtenir un répertoire avec les autorisations 777.  
4. Exécutez spark-submit en tant que sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Le connecteur Spark-Phoenix n’est pas pris en charge.

Actuellement, le connecteur Spark-Phoenix n’est pas pris en charge avec un cluster HDInsight Spark.

**Atténuation :**

Vous devez plutôt utiliser le connecteur Spark-HBase. Pour savoir comment procéder, consultez [How to use Spark-HBase connector](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problèmes liés aux notebooks Jupyter
Voici certains problèmes connus liés aux notebooks Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notebooks avec des caractères non-ASCII dans les noms de fichiers
Les notebooks Jupyter qui peuvent être utilisés dans les clusters Spark HDInsight ne doivent pas contenir de caractères non-ASCII dans les noms de fichiers. Si vous essayez de charger un fichier (par le biais de l’interface utilisateur Jupyter) dont le nom n’est pas au format ASCII, l’opération échoue silencieusement (c’est-à-dire que Jupyter ne vous permet pas de charger le fichier, mais il n’affiche pas d’erreur visible). 

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Erreur lors du chargement de notebooks de taille supérieure
Vous pouvez obtenir une erreur **`Error loading notebook`** lorsque vous tentez de charger un bloc-notes de taille supérieure.  

**Atténuation :**

Si vous obtenez cette erreur, cela ne signifie pas que vos données sont endommagées ou perdues.  Vos blocs-notes sont toujours sur le disque, sous `/var/lib/jupyter`et vous pouvez exécuter SSH dans le cluster pour y accéder. Pour en savoir plus, voir [Utilisation de SSH avec HDInsight (Hadoop) depuis Bash (l’interpréteur de commande) sur Windows 10, Linux, Unix ou OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

Une fois connecté au cluster à l’aide de SSH, vous pouvez copier les blocs-notes depuis le cluster vers votre ordinateur local (à l’aide de SCP ou WinSCP) pour en faire une sauvegarde afin d’éviter la perte de toutes les données importantes dans le bloc-notes. Vous pouvez ensuite créer un tunnel SSH dans votre nœud principal sur le port 8001, afin d’accéder à Jupyter sans avoir à passer par la passerelle.  À partir de là, vous pouvez effacer la sortie de votre bloc-notes et l’enregistrer de nouveau pour réduire au maximum la taille du bloc-notes.

Pour éviter cette erreur à l’avenir, gardez en tête les conseils suivants :

* Il est important que la taille du bloc-notes reste réduite. Aucune sortie de vos travaux sous Spark qui est envoyée à Jupyter n’est conservée dans le bloc-notes.  Pour Jupyter, il est en général recommandé d’éviter l’exécution de l’élément `.collect()` sur des RDD ou trames de données larges. En revanche, si vous souhaitez lire le contenu d’un RDD, pensez à exécuter `.take()` ou `.sample()` afin que votre sortie ne devienne pas trop volumineuse.
* En outre, lorsque vous enregistrez un bloc-notes, supprimez toutes les cellules de sortie pour réduire sa taille.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Démarrage du bloc-notes plus long que prévu
La première instruction de code du bloc-notes Jupyter avec Spark Magic peut nécessiter plusieurs minutes.  

**Explication :**

Cela se produit lorsque la première cellule du code est exécutée. En arrière-plan, la configuration de la session est lancée, et les contextes Spark, SQL et Hive sont définis. Une fois ces contextes définis, la première instruction est exécutée et donne l'impression que l'instruction prend beaucoup de temps.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Délai d’attente du bloc-notes Jupyter lors de la création de la session
Lorsque le cluster Spark manque de ressources, les noyaux Spark et PySpark du bloc-notes Jupyter expirent en essayant de créer la session. 

**Atténuations :** 

1. Libérez certaines ressources de votre cluster Spark :
   
   * Arrêtez les autres blocs-notes Spark en allant dans le menu Fermer et Arrêter ou en cliquant sur Arrêter dans l’explorateur du bloc-notes.
   * Arrêtez les autres applications Spark à partir de YARN.
2. Redémarrez le bloc-notes que vous tentiez de démarrer. Un nombre suffisant de ressources devrait désormais être disponible pour vous permettre de créer une session.

## <a name="see-also"></a>Voir aussi
* [Vue d’ensemble : Apache Spark sur Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénarios
* [Spark avec BI : effectuez une analyse interactive des données à l’aide de Spark dans HDInsight avec des outils BI](apache-spark-use-bi-tools.md)
* [Spark avec Machine Learning : Utiliser Spark dans HDInsight pour l’analyse de la température de bâtiments à l’aide de données HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark avec Machine Learning : utilisez Spark dans HDInsight pour prédire les résultats de l’inspection des aliments](apache-spark-machine-learning-mllib-ipython.md)
* [Analyse des journaux de site web à l’aide de Spark dans HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Création et exécution d’applications
* [Créer une application autonome avec Scala](apache-spark-create-standalone-application.md)
* [Exécution de travaux à distance avec Livy sur un cluster Spark](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Outils et extensions
* [Utilisez le plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster (Installer Jupyter sur un ordinateur et se connecter au cluster Spark sur HDInsight)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)

