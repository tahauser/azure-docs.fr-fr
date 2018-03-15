---
title: "Installer une application publiée - H2O Sparkling Water - Azure HDInsight | Microsoft Docs"
description: "Installez et utilisez l’application Hadoop tierce H2O Sparkling Water."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 8734daa5303aa76e9f8a074b5f709727cabb58b2
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---h2o-sparkling-water"></a>Installer une application publiée - H2O Sparkling Water

Cet article explique comment installer et exécuter l’application Hadoop publiée [H2O Sparkling Water](http://www.h2o.ai/) sur Azure HDInsight. Pour obtenir une vue d’ensemble de la plateforme d’application HDInsight et une liste des applications publiées par des éditeurs de logiciels indépendants (ISV), consultez [Installer des applications Hadoop tierces](hdinsight-apps-install-applications.md). Pour des instructions d’installation de votre propre application, consultez la page [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).

## <a name="about-h2o-sparkling-water"></a>À propos de H2O Sparkling Water

H2O Sparkling Water est une plateforme d’apprentissage automatique open source, en mémoire et entièrement distribuée, avec évolutivité linéaire. H2O Sparkling Water vous permet de combiner les algorithmes d’apprentissage automatique évolutifs et efficaces de H2O avec les fonctionnalités de Spark. Avec Sparkling Water, les utilisateurs peuvent effectuer des calculs dans Scala, R et Python à l’aide de l’interface utilisateur H2O Flux.

Avantages de H2O Sparkling Water :

* **Interfaces WebUI conviviales et familières** : accélérez la configuration et la mise en route à l’aide de l’interface graphique utilisateur H2O, intuitive et basée sur le web, ou des environnements de programmation comme R, Python, Java, Scala, JSON, et les API H2O.
* **Prise en charge indépendante des données pour tous les types courants de bases de données et de fichiers** : explorez et modélisez facilement le Big Data à partir de Microsoft Excel, R Studio, Tableau et bien plus encore. Connectez-vous aux données à partir de sources de données HDFS, S3, SQL et NoSQL.
* **Description et analyse de Big Data très évolutives** : H2O Big Joins permet d’effectuer jusqu’à 7x plus rapidement les opérations R data.table et de mettre à l’échelle de façon linéaire jusqu’à 10 milliards x 10 milliards de jointures de ligne.
* **Calcul du score des données en temps réel** : déployez rapidement des modèles en production à l’aide d’objets Java optimisés par modèle simple (POJO), d’objets Java optimisés par modèle (MOJO), ou de l’API REST H2O.

### <a name="resource-links"></a>Liens de ressources

* [Feuille de route de l’ingénierie H2O.ai](https://jira.h2o.ai/)
* [Accueil H2O.ai](http://www.h2o.ai/)
* [H2O.ai Documentation](http://docs.h2o.ai/)
* [Assistance H2O.ai](https://support.h2o.ai/)
* [Codebase open source H2O.ai](https://github.com/h2oai/)

## <a name="prerequisites"></a>Prérequis

Pour installer cette application sur un cluster existant ou sur un nouveau cluster HDInsight, la configuration suivante est nécessaire :

* Niveau(x) de cluster : Standard ou Premium
* Type de cluster : Spark
* Version(s) de cluster : 3.5 ou 3.6

## <a name="install-the-h2o-sparkling-water-published-application"></a>Installer l’application publiée H2O Sparkling Water

Pour obtenir des instructions détaillées sur l’installation de cette application et d’autres applications ISV disponibles, consultez [Installer des applications Hadoop tierces](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Lancer H2O Sparkling Water

1. Après l’installation, vous pouvez commencer à utiliser H2O Sparkling Water (h2o-sparklingwater) à partir de votre cluster dans le portail Azure en ouvrant des blocs-notes Jupyter (`https://<ClusterName>.azurehdinsight.net/jupyter`). Vous pouvez également accéder à Jupyter en sélectionnant **Tableau de bord du cluster** à partir du volet de votre cluster dans le portail, puis en sélectionnant **Bloc-notes Jupyter**. Vous êtes invité à entrer vos informations d’identification. Entrez les informations d’identification Hadoop du cluster comme indiqué à la création du cluster.

2. Jupyter comporte trois dossiers : H2O-PySparkling-Examples, PySpark Examples et Scala Examples. Sélectionnez le dossier **H2O-PySparkling-Examples**.

    ![Accueil Bloc-notes Jupyter](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. La première étape de la création d’un bloc-notes consiste à configurer l’environnement Spark. Ces informations sont incluses dans l’exemple **Sentiment_analysis_with_Sparkling_Water**. Lorsque vous configurez l’environnement Spark, veillez à utiliser le fichier jar approprié, puis spécifiez l’adresse IP fournie par la sortie de la première cellule.

    ![Accueil Bloc-notes Jupyter](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Démarrez le cluster H2O.

    ![Démarrer le cluster](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. Une fois le cluster H2O en cours d’exécution, ouvrez H2O Flow en accédant à **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]
    > Si vous ne parvenez pas à ouvrir H2O Flow, essayez de vider le cache de votre navigateur. Si vous ne parvenez toujours pas à atteindre H20 Flow, votre cluster ne dispose probablement pas de ressources suffisantes. Essayez d’augmenter le nombre de nœuds de travail sous l’option **Mettre à l’échelle le cluster** dans le volet de votre cluster.

    ![Tableau de bord H2O Flow](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Sélectionnez l’exemple **Million_Songs.flow** dans le menu de droite. Lorsque vous recevez un avertissement, cliquez sur **Load Notebook** (Charger le bloc-notes). Cette démonstration est conçue pour s’exécuter en quelques minutes à l’aide de données réelles. L’objectif est de prédire un résultat à partir des données si la chanson est sortie avant ou après 2004 à l’aide d’une classification binaire.

    ![Sélectionner Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Recherchez le chemin d’accès contenant **milsongs-cls-train.csv.gz**et remplacez le chemin d’accès complet par **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Recherchez le chemin d’accès contenant **milsongs-cls-test.csv.gz** et remplacez-le par **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Pour exécuter toutes les instructions dans les cellules du bloc-notes, sélectionnez le bouton **Exécuter tout** dans la barre d’outils.

    ![Exécuter tout](./media/hdinsight-apps-install-h2o/run-all.png)

10. Après quelques minutes, le résultat doit ressembler à ce qui suit.

    ![Sortie](./media/hdinsight-apps-install-h2o/output.png)

Et voilà ! Vous avez en quelques minutes maîtrisé l’intelligence artificielle de Spark. Vous pouvez maintenant explorer d’autres exemples dans H2O Flow, qui illustrent les différents types d’algorithmes d’apprentissage automatique.

## <a name="next-steps"></a>Étapes suivantes

* [Documentation H2O](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md) : découvrez comment déployer des applications HDInsight inédites vers HDInsight.
* [Publier des applications HDInsight dans la Place de marché Azure](hdinsight-apps-publish-applications.md): découvrez comment publier vos applications HDInsight personnalisées sur la Place de marché Azure.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN : installer une application HDInsight) : découvrez comment définir des applications HDInsight.
* [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md) : apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Utiliser les nœuds de périphérie vides dans HDInsight](hdinsight-apps-use-edge-node.md): apprenez à utiliser un nœud de périphérique vide pour accéder aux clusters HDInsight, ainsi que pour tester et héberger des applications HDInsight.
