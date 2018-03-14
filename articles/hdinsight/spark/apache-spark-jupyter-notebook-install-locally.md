---
title: "Installer un bloc-notes Jupyter localement et le connecter à un cluster Spark Azure HDInsight | Microsoft Docs"
description: "Découvrez comment installer un bloc-notes Jupyter localement sur votre ordinateur et le connecter à un cluster Apache Spark sur Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48593bdf-4122-4f2e-a8ec-fdc009e47c16
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 5549c175c280961b09f5996e3387a50dae31222f
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="install-jupyter-notebook-on-your-computer-and-connect-to-apache-spark-on-hdinsight"></a>Installer un bloc-notes Jupyter sur votre ordinateur et le connecter à Apache Spark sur HDInsight

Dans cet article, vous allez apprendre à installer le bloc-notes Jupyter avec les noyaux PySpark (pour Python) et Spark (pour Scala) personnalisés avec Spark magic et connecter le bloc-notes à un cluster HDInsight. Plusieurs raisons peuvent motiver l’installation de Jupyter sur votre ordinateur local. Différents défis peuvent également se présenter. Consultez la section [Pourquoi dois-je installer Jupyter sur mon ordinateur ?](#why-should-i-install-jupyter-on-my-computer) à la fin de cet article.

L’installation de Jupyter et de Spark magic sur votre ordinateur comprend trois étapes essentielles.

* Installation du bloc-notes Jupyter
* Installation des noyaux PySpark et Spark avec Spark Magic
* Configuration de Spark Magic pour accéder au cluster Spark sur HDInsight

Pour plus d’informations sur les noyaux personnalisés et Spark Magic disponible pour les blocs-notes Jupyter avec le cluster HDInsight, consultez [Noyaux disponibles pour les blocs-notes Jupyter avec les clusters HDInsight Spark Linux sur HDInsight](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Prérequis
La configuration requise indiquée ici ne concerne pas l’installation de Jupyter. Elle s’applique à la connexion du bloc-notes Jupyter à un cluster HDInsight une fois le bloc-notes installé.

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un cluster Apache Spark sur HDInsight. Pour obtenir des instructions, consultez [Création de clusters Apache Spark dans Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="install-jupyter-notebook-on-your-computer"></a>Installer le bloc-notes Jupyter sur votre ordinateur

Vous devez installer Python avant de pouvoir installer les blocs-notes Jupyter. Python et Jupyter font partie de la [distribution Anaconda](https://www.continuum.io/downloads). Lorsque vous installez Anaconda, vous installez une distribution de Python. Une fois Anaconda installé, vous ajoutez l’installation de Jupyter en exécutant les commandes appropriées.

1. Téléchargez le [programme d’installation Anaconda](https://www.continuum.io/downloads) pour votre plateforme et exécutez le programme d’installation. Lors de l’exécution de l’assistant d’installation, veillez à sélectionner l’option permettant d’ajouter Anaconda à votre variable PATH.
2. Exécutez la commande suivante pour installer Jupyter.

        conda install jupyter

    Pour plus d’informations sur l’installation de Jupyter, consultez [Installing Jupyter using Anaconda](http://jupyter.readthedocs.io/en/latest/install.html)(Installation de Jupyter à l’aide d’Anaconda).

## <a name="install-the-kernels-and-spark-magic"></a>Installer les noyaux et Spark Magic

Pour obtenir des instructions sur l’installation des noyaux Spark Magic, PySpark et Spark, consultez la [documentation sparkmagic](https://github.com/jupyter-incubator/sparkmagic#installation) sur GitHub. La première étape de la documentation vous invite à installer Spark Magic. Remplacez la première étape du lien par les commandes suivantes, selon la version du cluster HDInsight auquel vous vous connectez. Après cela, suivez les étapes restantes de la documentation Spark Magic. Si vous souhaitez installer les différents noyaux, vous devez effectuer l’étape 3 de la section contenant les instructions d’installation.

* Pour les clusters v3.4, installez sparkmagic 0.2.3 en exécutant `pip install sparkmagic==0.2.3`.

* Pour les clusters v3.5 et v3.6, installez sparkmagic 0.11.2 en exécutant `pip install sparkmagic==0.11.2`.

## <a name="configure-spark-magic-to-connect-to-hdinsight-spark-cluster"></a>Configurer Spark Magic pour la connexion au cluster Spark HDInsight

Dans cette section, vous configurez Spark Magic que vous avez installé précédemment pour qu’il se connecte à un cluster Apache Spark que vous devez avoir déjà créé dans Azure HDInsight.

1. Les informations de configuration de Jupyter sont généralement stockées dans le répertoire de base des utilisateurs. Pour localiser votre répertoire de base sur n’importe quelle plateforme de système d’exploitation, tapez les commandes suivantes.

    Démarrez l’interface de Python. Dans la fenêtre de commande, tapez la commande suivante :

        python

    Dans l’interface de Python, entrez la commande suivante pour rechercher le répertoire de base.

        import os
        print(os.path.expanduser('~'))

2. Accédez au répertoire de base et créez un dossier nommé **.sparkmagic** s’il n’existe pas.
3. Dans le dossier, créez un fichier appelé **config.json** et ajoutez-y l’extrait de code JSON suivant.

        {
          "kernel_python_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          },
          "kernel_scala_credentials" : {
            "username": "{USERNAME}",
            "base64_password": "{BASE64ENCODEDPASSWORD}",
            "url": "https://{CLUSTERDNSNAME}.azurehdinsight.net/livy"
          }
        }

4. Remplacez **{USERNAME}**, **{CLUSTERDNSNAME}** et **{BASE64ENCODEDPASSWORD}** par les valeurs appropriées. Vous pouvez utiliser un certain nombre d’utilitaires dans votre langage de programmation favori ou en ligne pour générer un mot de passe codé en base64 pour votre mot de passe actuel.

5. Configurez les bons paramètres de pulsation dans `config.json`. Vous devez ajouter ces paramètres au même niveau que les extraits de code `kernel_python_credentials` et `kernel_scala_credentials` ajoutés précédemment. Consultez cet [exemple config.json](https://github.com/jupyter-incubator/sparkmagic/blob/master/sparkmagic/example_config.json) pour voir comment et où ajouter les paramètres de pulsation.

    * Pour `sparkmagic 0.2.3` (clusters v3.4), incluez :

            "should_heartbeat": true,
            "heartbeat_refresh_seconds": 5,
            "heartbeat_retry_seconds": 1

    * Pour `sparkmagic 0.11.2` (clusters v3.5 et v3.6), incluez :

            "heartbeat_refresh_seconds": 5,
            "livy_server_heartbeat_timeout_seconds": 60,
            "heartbeat_retry_seconds": 1

    >[!TIP]
    >Les pulsations sont envoyées pour vous assurer que les sessions ne sont pas divulguées. Lorsqu’un ordinateur se met en veille ou s’arrête, la pulsation n’est pas envoyée, causant ainsi le nettoyage de la session. Pour les clusters v3.4, si vous souhaitez désactiver ce comportement, vous pouvez définir la configuration Livy `livy.server.interactive.heartbeat.timeout` à `0` à partir de l’interface utilisateur Ambari. Pour les clusters v3.5, si vous ne définissez pas la configuration 3.5 ci-dessus, la session ne sera pas supprimée.

6. Démarrez Jupyter. Utilisez la commande suivante depuis l’invite de commandes :

        jupyter notebook

7. Vérifiez que vous pouvez vous connecter au cluster à l’aide du bloc-notes Jupyter et que vous pouvez utiliser Spark Magic disponible avec les noyaux. Procédez comme suit.

    a. Créer un nouveau bloc-notes. Dans le coin de droite, cliquez sur **Nouveau**. Le noyau par défaut **Python2** doit s’afficher, ainsi que les deux nouveaux noyaux que vous installez : **PySpark** et **Spark**. Cliquez sur **PySpark**.

    ![Noyaux de bloc-notes Jupyter](./media/apache-spark-jupyter-notebook-install-locally/jupyter-kernels.png "Noyaux de bloc-notes Jupyter")

    b. Exécutez l’extrait de code suivant :

        %%sql
        SELECT * FROM hivesampletable LIMIT 5

    Votre connexion au cluster HDInsight sera testée, si vous parvenez à récupérer la sortie.

    >[!TIP]
    >Si vous souhaitez mettre à jour la configuration du bloc-notes pour vous connecter à un autre cluster, mettez à jour le fichier config.json en tenant compte du nouveau jeu de valeurs, comme indiqué à l’étape 3 ci-dessus.

## <a name="why-should-i-install-jupyter-on-my-computer"></a>Pourquoi dois-je installer Jupyter sur mon ordinateur ?
Plusieurs raisons peuvent motiver l’installation de Jupyter sur votre ordinateur et sa connexion à un cluster Spark sur HDInsight.

* Même si les blocs-notes Jupyter sont déjà disponibles sur le cluster Spark dans Azure HDInsight, l’installation de Jupyter sur votre ordinateur vous permet de créer vos blocs-notes localement, de tester votre application sur un cluster en cours d’exécution, puis de charger les blocs-notes sur le cluster. Vous pouvez charger les blocs-notes dans le cluster à l’aide du bloc-notes Jupyter en cours d’exécution ou du cluster, ou en les enregistrant dans le dossier /HdiNotebooks du compte de stockage associé au cluster. Pour plus d’informations sur le stockage des blocs-notes, consultez [Where are Jupyter notebooks stored](apache-spark-jupyter-notebook-kernels.md#where-are-the-notebooks-stored)(Où sont stockés les blocs-notes Jupyter) ?
* Les blocs-notes étant disponibles localement, vous pouvez vous connecter à différents clusters Spark selon les besoins de votre application.
* Vous pouvez utiliser GitHub pour implémenter un système de contrôle de code source et disposer du contrôle de version pour les blocs-notes. Vous pouvez également disposer d’un environnement de collaboration où plusieurs utilisateurs peuvent travailler avec le même bloc-notes.
* Vous pouvez travailler avec les blocs-notes localement sans avoir un cluster activé. Il vous suffit d’un cluster avec lequel tester vos blocs-notes, sans avoir à gérer manuellement vos blocs-notes ou à disposer d’un environnement de développement.
* Il peut être plus facile de configurer votre propre environnement de développement local que de configurer l’installation de Jupyter sur le cluster.  Vous pouvez tirer parti de tous les logiciels que vous avez installés localement sans configurer un ou plusieurs clusters distants.

> [!WARNING]
> Lorsque Jupyter est installé sur votre ordinateur local, plusieurs utilisateurs peuvent exécuter le même bloc-notes sur le même cluster Spark en même temps. Dans ce cas, plusieurs sessions Livy sont créées. Si vous rencontrez un problème et que vous souhaitez effectuer un débogage, il sera difficile de savoir quelle session Livy appartient à quel utilisateur.
>
>

## <a name="seealso"></a>Voir aussi
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
* [Utilisation du plugin d’outils HDInsight pour IntelliJ IDEA pour créer et soumettre des applications Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Utiliser le plug-in Outils HDInsight pour IntelliJ IDEA pour déboguer des applications Spark à distance)](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Utiliser des bloc-notes Zeppelin avec un cluster Spark sur HDInsight](apache-spark-zeppelin-notebook.md)
* [Noyaux disponibles pour le bloc-notes Jupyter dans un cluster Spark pour HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Utiliser des packages externes avec les blocs-notes Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)

### <a name="manage-resources"></a>Gestion des ressources
* [Gérer les ressources du cluster Apache Spark dans Azure HDInsight](apache-spark-resource-manager.md)
* [Suivi et débogage des tâches en cours d’exécution sur un cluster Apache Spark dans HDInsight](apache-spark-job-debugging.md)
