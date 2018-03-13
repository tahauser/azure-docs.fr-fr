---
title: Guide pratique pour utiliser MMLSpark avec Machine Learning | Microsoft Docs
description: "Explique comment utiliser la bibliothèque MMLSpark avec Azure Machine Learning."
services: machine-learning
author: rastala
ms.author: roastala
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: f978805f800a35908629a6febb59d7db50d14023
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/13/2018
---
# <a name="how-to-use-microsoft-machine-learning-library-for-apache-spark"></a>Guide pratique pour utiliser la bibliothèque Microsoft Machine Learning pour Apache Spark

## <a name="introduction"></a>Introduction

La [bibliothèque MMLSpark](https://github.com/Azure/mmlspark) (Microsoft Machine Learning Library for Apache Spark) fournit des outils qui vous permettent de créer facilement des modèles d’apprentissage automatique évolutifs pour des jeux de données volumineux. Elle comprend l’intégration des pipelines SparkML à [Microsoft Cognitive Toolkit](https://github.com/Microsoft/CNTK) et [OpenCV](http://www.opencv.org/), ce qui vous permet d’effectuer les tâches suivantes : 
 * Entrée et prétraitement des données d’image
 * Caractérisation des images et du texte à l’aide de modèles d’apprentissage profond préformés
 * Formez et évaluez des modèles de classification et de régression à l’aide d’une caractérisation implicite.

## <a name="prerequisites"></a>Conditions préalables

Pour parcourir ce guide pratique, vous devez :
- [Installer Azure Machine Learning Workbench](quickstart-installation.md)
- [Configurer un cluster Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql)

## <a name="run-your-experiment-in-docker-container"></a>Exécuter votre expérimentation dans un conteneur Docker

Azure Machine Learning Workbench est configuré pour utiliser MMLSpark quand vous exécutez des expérimentations dans un conteneur Docker, localement ou dans une machine virtuelle distante. Cette fonctionnalité vous permet de facilement déboguer et tester vos modèles PySpark, avant de les exécuter à l’échelle sur un cluster. 

Pour commencer avec un exemple, créez un projet et sélectionnez l’exemple « MMLSpark sur Adult Census » dans la galerie. Sélectionnez « Docker » comme contexte de calcul à partir du tableau de bord du projet, puis cliquez sur « Exécuter ». Azure Machine Learning Workbench génère le conteneur Docker pour exécuter le programme PySpark, puis exécute le programme.

Une fois l’exécution terminée, vous pouvez afficher les résultats dans la vue de l’historique des exécutions d’Azure Machine Learning Workbench.

## <a name="install-mmlspark-on-azure-hdinsight-spark-cluster"></a>Installez MMLSpark sur le cluster Azure HDInsight Spark.

Pour effectuer cette étape et la suivante, vous devez d’abord [créer un cluster Azure HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql).

Par défaut, Azure Machine Learning Workbench installe le package MMLSpark sur votre cluster quand vous exécutez votre expérimentation. Vous pouvez contrôler ce comportement et installer d’autres packages Spark en modifiant un fichier nommé _aml_config/spark_dependencies.yml_ dans votre dossier de projet.

```
# Spark configuration properties.
configuration:
  "spark.app.name": "Azure ML Experiment"
  "spark.yarn.maxAppAttempts": 1

repositories:
  - "https://mmlspark.azureedge.net/maven"
packages:
  - group: "com.microsoft.ml.spark"
    artifact: "mmlspark_2.11"
    version: "0.9.9"
```

Vous pouvez également installer MMLSpark directement sur votre cluster HDInsight Spark à l’aide d’une [action de script](https://github.com/Azure/mmlspark#hdinsight).

## <a name="set-up-azure-hdinsight-spark-cluster-as-compute-target"></a>Configurer un cluster Azure HDInsight Spark en tant que cible de calcul

Ouvrez la fenêtre de l’interface de ligne de commande (CLI) à partir d’Azure Machine Learning Workbench en accédant au menu « Fichier », puis en cliquant sur « Ouvrir l’invite de commandes ».

Dans la fenêtre de l’interface CLI, exécutez les commandes suivantes :

```
az ml computetarget attach cluster --name <myhdi> --address <myhdi-ssh.azurehdinsight.net> --username <sshusername> --password <sshpwd> 
```

```
az ml experiment prepare -c <myhdi>
```

Le cluster est désormais disponible en tant que cible de calcul pour le projet.

## <a name="run-experiment-on-azure-hdinsight-spark-cluster"></a>Exécutez l’expérimentation sur le cluster Azure HDInsight Spark.

Revenez au tableau de bord du projet de l’exemple « MMLSpark sur Adult Census ». Sélectionnez votre cluster en tant que cible de calcul, puis cliquez sur Exécuter.

Azure Machine Learning Workbench soumet le travail Spark au cluster. Vous pouvez surveiller la progression et afficher les résultats dans la vue de l’historique des exécutions.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la bibliothèque MMLSpark et pour obtenir des exemples, consultez [Dépôt GitHub MMLSpark](https://github.com/Azure/mmlspark).

*Apache®, Apache Spark et Spark® sont soit des marques déposées, soit des marques commerciales d’Apache Software Foundation aux États-Unis et/ou dans d’autres pays.*
