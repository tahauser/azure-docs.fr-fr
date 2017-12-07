---
title: "Scénario réel de maintenance prédictive | Microsoft Docs"
description: "Scénario réel de maintenance prédictive avec PySpark"
services: machine-learning
author: ehrlinger
ms.author: jehrling
manager: jhubbard
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.custom: mvc
ms.date: 10/05/2017
ms.openlocfilehash: 2687eb022bce0b71c217f0be611c8fabdfb66040
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="predictive-maintenance-real-world-scenario"></a>Scénario réel de maintenance prédictive

L’impact des temps d’arrêt non planifiés de l’équipement peut nuire aux entreprises. Il est par conséquent essentiel de maintenir les équipements opérationnels afin d’optimiser les performances et l’utilisation, et de réduire les temps d’arrêt non planifiés et coûteux. Cette identification précoce des problèmes peut aider à allouer des ressources de maintenance limitées de façon économique et à améliorer la qualité et les processus de la chaîne d’approvisionnement. 

Pour ce scénario, nous utilisons un [ensemble de données relativement important](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) pour guider l’utilisateur à travers les principales étapes, de l’ingestion des données, l’ingénierie des fonctionnalités et la création d’un modèle à l’opérationnalisation et au déploiement du modèle. Le code de l’ensemble du processus est écrit dans PySpark et implémenté à l’aide de blocs-notes Jupyter dans Azure ML Workbench. Pour terminer, le meilleur modèle est opérationnalisé à l’aide de Gestion des modèles Azure Machine Learning en vue de son utilisation dans un environnement de production pour effectuer des prédictions de défaillance en temps réel.   

## <a name="link-to-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie

Le lien vers le dépôt GitHub public est le suivant : [https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance)


## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage

L’un des problèmes majeurs auxquels sont confrontées les entreprises des secteurs gourmands en ressources n’est autre que les coûts importants associés aux retards dus à des problèmes mécaniques. La plupart des entreprises aimeraient être en mesure de prédire quand ces problèmes surviendront pour éviter de façon proactive qu’ils ne se produisent. Cela permet d’alléger les coûts en réduisant les temps d’arrêt et, dans certains cas, d’améliorer la sécurité. Pour découvrir une explication détaillée des cas d’usage courants et de l’approche de modélisation utilisée pour la maintenance prédictive, consultez le [manuel d’utilisation concernant la maintenance prédictive](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance).

Ce scénario tire parti des idées du playbook dans le but de décrire les étapes nécessaires pour implémenter un modèle prédictif pour un scénario basé sur une synthèse de divers problèmes métier du monde réel. Cet exemple regroupe des éléments de données communs observés dans de nombreux cas d’usage de maintenance prédictive.

Le problème métier pour ces données simulées consiste à prédire les problèmes causés par les échecs de composants. La question métier est par conséquent la suivante : « *Quelle est la probabilité qu’une machine tombe en panne en raison d’un échec de composant* ? » Ce problème est formulé sous forme de problème de classification multiclasse (plusieurs composants par machine) et un algorithme Machine Learning est utilisé pour créer le modèle prédictif. L’apprentissage du modèle est effectué à partir de données d’historique collectées sur des machines. Dans ce scénario, l’utilisateur réalise les différentes étapes d’implémentation d’un modèle de ce type dans l’environnement Azure Machine Learning Workbench.

## <a name="prerequisites"></a>Composants requis

* Un [compte Azure](https://azure.microsoft.com/en-us/free/) (des comptes d’essai gratuit sont disponibles).
* Une copie [d’Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installée selon les instructions du [guide d’installation et de démarrage rapide](./quickstart-installation.md) pour installer le programme et créer un espace de travail.
* L’opérationnalisation d’Azure Machine Learning requiert un environnement de déploiement local et un [compte de gestion des modèles](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-overview).

Cet exemple peut être exécuté sur n’importe quel contexte de calcul AML Workbench. Toutefois, il est recommandé de l’exécuter avec 16 Go de mémoire au minimum. Ce scénario a été conçu et testé sur une machine Windows 10 exécutant une [machine virtuelle de science des données pour Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft-ads.linux-data-science-vm-ubuntu) standard DS4_V2 distante.

L’opérationnalisation du modèle a été effectuée à l’aide de la version 0.1.0a22 de l’interface de ligne de commande Azure ML.

## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Azure Machine Learning Workbench
2.  Dans la page **Projets**, cliquez sur le signe **+**, puis sélectionnez **Nouveau projet**
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Maintenance prédictive » et sélectionnez le modèle
5.  Cliquez sur **Créer**

## <a name="prepare-the-notebook-server-computation-target"></a>Préparer la cible de calcul du serveur de notebooks

Pour une exécution sur votre machine locale, à partir du menu `File` d’AML Workbench, sélectionnez `Open Command Prompt` ou `Open PowerShell CLI`. Dans les fenêtres de l’interface de ligne de commande, exécutez les commandes suivantes :

`az ml experiment prepare --target docker --run-configuration docker`

Nous vous suggérons d’exécuter une machine virtuelle de science des données pour Linux (Ubuntu). Une fois cette machine virtuelle configurée, exécutez les deux commandes suivantes :

`az ml computetarget attach remotedocker --name [Desired_Connection_Name] --address [VM_IP_Address] --username [VM_Username] --password [VM_UserPassword]`

`az ml experiment prepare --target [Desired_Connection_Name] --run-configuration [Desired_Connection_Name]`

Une fois les images Docker préparées, ouvrez le serveur de notebooks Jupyter dans l’onglet des notebooks AML Workbench, ou si vous souhaitez exécuter un serveur basé sur un navigateur, exécutez : `az ml notebook start`.

Les notebooks sont stockés dans le répertoire `Code` de l’environnement Jupyter. Nous exécutons les notebooks dans l’ordre séquentiel, en commençant par le premier notebook (`Code\1_data_ingestion.ipynb`). Lorsque vous ouvrez chaque notebook, vous êtes invité à sélectionner le noyau de calcul. Choisissez [nom_du_projet]_Template [nom_de_connexion_souhaité], puis cliquez sur Set Kernel (Définir le noyau).

## <a name="data-description"></a>Description des données

Les [données simulées](https://github.com/Microsoft/SQL-Server-R-Services-Samples/tree/master/PredictiveMaintanenceModelingGuide/Data) sont constituées de cinq fichiers de valeurs séparées par une virgule (.csv). Pour obtenir une description détaillée des jeux de données, cliquez sur les liens correspondants.

* [Machines](https://pdmmodelingguide.blob.core.windows.net/pdmdata/machines.csv) : fonctionnalités qui différencient chaque machine. Par exemple, l’âge et le modèle.
* [Erreur](https://pdmmodelingguide.blob.core.windows.net/pdmdata/errors.csv) : le journal des erreurs contient des erreurs sans rupture levées alors que l’ordinateur est toujours opérationnel. Ces erreurs ne sont pas considérées comme des échecs, même si elles peuvent prédire un événement d’échec futur. La date et l’heure de l’erreur sont arrondies à l’heure la plus proche étant donné que les données de télémétrie sont collectées toutes les heures.
* [Maintenance](https://pdmmodelingguide.blob.core.windows.net/pdmdata/maint.csv) : le journal de maintenance contient à la fois les enregistrements de la maintenance planifiée et de la maintenance non planifiée. La maintenance planifiée correspond à l’inspection régulière des composants, tandis que la maintenance non planifiée peut être due à une défaillance mécanique ou autre dégradation des performances. La date et l’heure de la maintenance sont arrondies à l’heure la plus proche étant donné que les données de télémétrie sont collectées toutes les heures.
* [Télémétrie](https://pdmmodelingguide.blob.core.windows.net/pdmdata/telemetry.csv) : les données de série chronologique de télémétrie sont constituées de mesures de capteur de tension, rotation, pression et vibration collectées sur chaque machine en temps réel. Le système effectue la moyenne par heure des données et stocke ces dernières dans les journaux de télémétrie.
* [Échecs](https://pdmmodelingguide.blob.core.windows.net/pdmdata/failures.csv) : les échecs correspondent aux remplacements de composants dans le journal de maintenance. Chaque enregistrement contient l’ID de machine, le type de composant et les date et heure de remplacement. Ces enregistrements sont utilisés pour créer les étiquettes Machine Learning que le modèle tente de prédire.

Consultez le scénario d’[ingestion des données](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) à l’aide de bloc-notes Jupyter dans la section Code pour télécharger les jeux de données brutes à partir du dépôt GitHub et créer les jeux de données PySpark pour cette analyse.

## <a name="scenario-structure"></a>Structure du scénario
Le contenu pour le scénario est disponible dans le [dépôt GitHub](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance). 

Le référentiel contient un fichier [Lisez-moi](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/README.md) qui décrit les processus, de la préparation des données à la création de plusieurs modèles et l’opérationnalisation de l’un des meilleurs modèles. Les quatre blocs-notes Jupyter sont disponibles dans le dossier [Code](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/tree/master/Code) du référentiel.   

Décrivons à présent le flux de travail du scénario étape par étape. Le scénario de bout en bout est écrit dans PySpark et divisé en quatre notebooks :

[`Code\1_data_ingestion.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/1_data_ingestion.ipynb) : ce notebook télécharge les cinq fichiers .csv d’entrée et procède à un nettoyage préliminaire des données et à leur visualisation. Le notebook convertit les données au format PySpark et stocke les résultats dans un conteneur d’objets blob Azure en vue de leur utilisation dans la tâche d’ingénierie des fonctionnalités.

[`Code\2_feature_engineering.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/2_feature_engineering.ipynb) : le jeu de données nettoyé à l’étape précédente permet de créer les fonctionnalités de décalage et agrégées pour les capteurs de télémétrie, et les nombres d’erreurs, les remplacements de composants et les informations machine sont joints aux données de télémétrie. Les remplacements de composants associés à une défaillance sont utilisés pour la conception des étiquettes qui spécifient le composant défaillant. Les données de fonctionnalités étiquetées sont enregistrées dans un objet blob Azure pour la tâche de génération du modèle.

[`Code\3_model_building.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/3_model_building.ipynb) : à l’aide du jeu de données de fonctionnalités étiqueté, le notebook de modélisation fractionne les données en deux jeux, un jeu de données d’apprentissage et un jeu de données de développement, basés sur un horodatage. Le notebook est une expérience de configuration avec des modèles `pyspark.ml.classification`. Les données d’apprentissage sont vectorisées, et l’utilisateur peut effectuer un test avec un modèle `DecisionTreeClassifier` ou `RandomForestClassifier`, en manipulant des hyperparamètres afin de déduire le modèle le plus performant. Les performances sont déterminées par l’évaluation de statistiques de mesure sur le jeu de données de développement. Ces statistiques sont reconsignées dans l’écran du temps d’exécution AML Workbench à des fins de suivi. À chaque exécution, le notebook enregistre le modèle résultant sur le disque local exécutant le noyau Jupyter Notebook. 

[`Code\4_operationalization.ipynb`](https://github.com/Azure/MachineLearningSamples-PredictiveMaintenance/blob/master/Code/4_operationalization.ipynb) : à l’aide du dernier modèle enregistré sur le disque (noyau Jupyter Notebook) local, ce notebook génère les composants pour l’opérationnalisation du modèle dans un service web Azure. Les ressources entièrement opérationnelles sont compressées dans le fichier `o16n.zip` stocké dans un autre conteneur d’objets blob Azure. Le fichier compressé contient les éléments suivants :

* `service_schema.json` : fichier de définition de schéma pour le déploiement 
* `pdmscore.py` : fonctions init() et run() requises par le service web Azure
* `pdmrfull.model` : répertoire de définition de modèle
    
 Le notebook teste les fonctions avec la définition de modèle avant d’empaqueter les ressources d’opérationnalisation pour le déploiement. Des instructions de déploiement sont fournies à la fin du notebook.

## <a name="conclusion"></a>Conclusion

Ce scénario offre au lecteur une vue d’ensemble de la création d’une solution de maintenance prédictive de bout en bout à l’aide de PySpark dans l’environnement de blocs-notes Jupyter dans Azure ML Workbench. Le scénario oriente également le lecteur sur la façon d’opérationnaliser et de déployer le meilleur modèle à l’aide de l’environnement Gestion des modèles Azure Machine Learning en vue de son utilisation dans un environnement de production pour effectuer des prédictions de défaillance en temps réel. Le lecteur peut ensuite modifier les parties pertinentes du scénario pour l’adapter à ses besoins métier.  

## <a name="references"></a>Références

Ce cas d’usage a été précédemment développé sur plusieurs plateformes :

* [Modèle de solution de maintenance prédictive](https://docs.microsoft.com/en-us/azure/machine-learning/cortana-analytics-playbook-predictive-maintenance)
* [Guide de modélisation de maintenance prédictive](https://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Modelling-Guide-1)
* [Guide de modélisation de maintenance prédictive avec les services SQL R](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-Modeling-Guide-using-SQL-R-Services-1)
* [Guide de modélisation de maintenance prédictive - Bloc-notes Python](https://gallery.cortanaintelligence.com/Notebook/Predictive-Maintenance-Modelling-Guide-Python-Notebook-1)
* [Maintenance prédictive avec PySpark](https://gallery.cortanaintelligence.com/Tutorial/Predictive-Maintenance-using-PySpark)

# <a name="next-steps"></a>Étapes suivantes

Azure Machine Learning Workbench inclut de nombreux autres exemples de scénarios qui illustrent des fonctionnalités supplémentaires du produit. 