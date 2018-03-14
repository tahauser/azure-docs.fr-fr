---
title: Classification des revenus - Team Data Science Process - Azure Machine Learning | Microsoft Docs
description: "Découvrez comment utiliser le modèle Team Data Science Process pour créer un projet dans Azure Machine Learning qui classifie les revenus aux États-Unis."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.openlocfilehash: dc6279a1bac230146f4f0cebf2fbdbb6333cb7a1
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="income-classification-with-team-data-science-process-tdsp-project"></a>Classification des revenus avec un projet Team Data Science Process (TDSP)

## <a name="introduction"></a>Introduction

La normalisation de la structure et de la documentation des projets de science des données, qui est ancrée à un [cycle de vie de la science des données](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) établi, est primordiale pour favoriser une collaboration efficace au sein des équipes de science des données. La création de projets Azure Machine Learning avec le modèle [Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) permet de fournir l’infrastructure nécessaire pour cette normalisation.

Nous avions précédemment publié un [dépôt GitHub pour la structure et les modèles du projet TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Mais il n’était pas possible, avant aujourd’hui, d’instancier la structure et les modèles TDSP dans un outil de science des données. Nous permettons à présent la création de projets Azure Machine Learning qui sont instanciés avec [la structure et les modèles de documentation TDSP pour Azure Machine Learning](https://github.com/amlsamples/tdsp). Vous trouverez des instructions sur l’utilisation de la structure et des modèles TDSP dans Azure Machine Learning [ici](https://aka.ms/how-to-use-tdsp-in-aml). Nous proposons ici un exemple décrivant comment un projet Machine Learning réel peut être créé à l’aide de la structure TDSP, renseigné avec un code spécifique du projet, des artefacts et des documents, puis exécuté dans Azure Machine Learning.

## <a name="link-to-github-repository"></a>Lien vers le dépôt GitHub
Vous trouverez des documents récapitulatifs sur l’exemple [ici](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome). Une documentation plus complète est également disponible sur le site GitHub.

### <a name="purpose"></a>Objectif
L’objectif principal de cet exemple est de montrer comment instancier et exécuter un projet Machine Learning à l’aide de la structure et des modèles [Team Data Science Process (TDSP)](https://github.com/Azure/Microsoft-TDSP) dans Azure Machine Learning. Pour ce faire, nous utilisons les fameuses [données de recensement des États-Unis de 1994 disponibles dans le référentiel Machine Learning de l’UCI](https://archive.ics.uci.edu/ml/datasets/adult). La tâche de modélisation consiste à prédire les classes de revenus annuels des États-Unis à partir des informations de recensement des États-Unis (ex. âge, race, niveau d’éducation, pays d’origine, etc.).

### <a name="scope"></a>Étendue
 * Exploration de données, apprentissage et déploiement d’un modèle Machine Learning solutionnant le problème de prédiction décrit dans la vue d’ensemble du cas d’usage. 
 * Exécution du projet dans Azure Machine Learning à l’aide du modèle Team Data Science Process (TDSP) dans Azure Machine Learning pour ce projet. Pour l’exécution du projet et la génération des rapports correspondants, nous allons utiliser le cycle de vie TDSP.
 * Opérationnalisation de la solution directement à partir d’Azure Machine Learning dans Azure Container Service.

 Le projet met en avant plusieurs fonctionnalités d’Azure Machine Learning, telles que l’instanciation et l’utilisation de la structure TDSP, l’exécution de code dans des blocs-notes Jupyter et des fichiers Python, et une facile opérationnalisation dans Azure Container Service à l’aide de Docker et Kubernetes.


## <a name="team-data-science-process-tdsp-lifecycle"></a>Cycle de vie du processus TDSP (Team Data Science Process)
Consultez [Cycle de vie du processus TDSP (Team Data Science Process)](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md).

![](./media/scenario-tdsp-classifying-us-incomes/tdsp-lifecycle.jpg)

## <a name="prerequisites"></a>Prérequis
### <a name="required-subscription-hardware-software"></a>Obligatoire : abonnement, matériel, logiciels
1. Un [abonnement](https://azure.microsoft.com) Azure. Vous pouvez également obtenir un [abonnement gratuit](https://azure.microsoft.com/free/?v=17.16&WT.srch=1&WT.mc_id=AID559320_SEM_cZGgGOIg) pour exécuter cet exemple.
2. Une [machine virtuelle de science des données (DSVM) Azure Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.windows-data-science-vm) (taille de la machine virtuelle : [DS3_V2](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), avec 4 processeurs virtuels et 14 Go de RAM). Bien que testé sur une machine virtuelle de science des données Azure, cet exemple devrait fonctionner sur n’importe quelle machine Windows 10.
3. Passez en revue la documentation sur Azure Machine Learning et ses services associés (voir les liens ci-dessous).
4. Assurez-vous que vous avez correctement installé Azure Machine Learning conformément aux instructions du [guide d’installation de démarrage rapide](quickstart-installation.md).

Le jeu de données pour cet exemple est extrait du référentiel ML de l’UCI [[lien]](https://archive.ics.uci.edu/ml/datasets/adult). Il provient de la base de données de recensement des États-Unis de 1994 et contient des données de recensement et des informations sur les revenus concernant environ 50 000 personnes. Il s’agit d’un jeu de données structuré avec des caractéristiques numériques et catégorielles, et une cible catégorielle constituée de deux catégories de revenus (« > 50 K » ou « < = 50 K »). 

### <a name="optional-version-control-repository"></a>Facultatif : référentiel de gestion de version
Si vous souhaitez enregistrer et gérer les versions de votre projet et son contenu, vous devez disposer d’un référentiel de gestion de versions le permettant. Vous pouvez accéder à l’emplacement du dépôt Git durant la création du nouveau projet à l’aide du modèle TDSP dans Azure Machine Learning. Consultez [Guide pratique pour utiliser Git dans Azure Machine Learning](using-git-ml-project.md) pour plus d’informations.

### <a name="informational-about-azure-machine-learning"></a>Informations sur Azure Machine Learning
* [FAQ - Guide pratique pour bien démarrer](frequently-asked-questions.md)
* [Vue d’ensemble](overview-what-is-azure-ml.md)
* [Installation](quickstart-installation.md)
* [Exécution](experimentation-service-configuration.md)
* [Utilisation de TDSP](https://aka.ms/how-to-use-tdsp-in-aml)
* [Lire et écrire des fichiers](how-to-read-write-files.md)
* [Utilisation de Git avec Azure Machine Learning](using-git-ml-project.md)
* [Déploiement d’un modèle ML en tant que service web](model-management-service-deploy.md)

### <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Azure Machine Learning Workbench
2.  Dans la page **Projets**, cliquez sur le signe **+**, puis sélectionnez **Nouveau projet**
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Projet TDSP de classification des revenus des États-Unis » et sélectionnez le modèle
5.  Cliquez sur **Créer**

Si vous indiquez l’emplacement d’un dépôt Git vide pendant la création du projet (dans la zone appropriée), ce dépôt est renseigné avec la structure de projet et le contenu une fois le projet créé.

## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage
Le problème est de comprendre comment les données socioéconomiques de recensement des États-Unis peuvent aider à prédire les revenus annuels des individus aux États-Unis. À partir de ces caractéristiques de recensement, la tâche de Machine Learning consiste à prédire si les revenus d’un individu sont supérieurs ou non à 50 000 $ (tâche de classification binaire).

## <a name="data-description"></a>Description des données
Pour plus d’informations sur les données, consultez la [description](https://archive.ics.uci.edu/ml/machine-learning-databases/adult/adult.names) dans le référentiel de l’UCI. 

Ces données ont été extraites de la base de données du Bureau de recensement disponible sur le site https://www.census.gov/en.html. 


* Il existe en tout 48 842 instances (avant tout filtrage), à la fois continues et discrètes (apprentissage = 32 561, test = 16 281)
* Probabilité de la catégorie « >50 K » : 23,93 % / 24,78% (sans inconnues)
* Probabilité de la catégorie « <=50 K » : 76,07 % / 75,22 % (sans inconnues)  

* **CIBLE** : classe de revenus « >50 K », « <=50 K ». Ces classes sont remplacées respectivement par 1 et 0 durant la phase de préparation des données.
* **CARACTÉRISTIQUES** : âge, classe de travail, niveau d’éducation, race, sexe, heures de travail hebdomadaires, etc.


## <a name="project-structure-execution-and-reporting"></a>Structure, exécution et génération de rapports du projet

### <a name="structure"></a>Structure
Pour ce projet, nous utilisons la structure de dossiers et les modèles de documentation TDSP (ci-dessous), qui suivent le [cycle de vie du processus TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). 

Le projet est créé conformément aux instructions fournies [ici](https://aka.ms/how-to-use-tdsp-in-aml). Une fois renseignée avec le code et les artefacts du projet, la structure se présente comme suit (voir la structure de projet indiquée par l’encadré rouge dans la figure ci-dessous).


<img src="./media/scenario-tdsp-classifying-us-incomes/instantiation-4.png" width="900" height="700">

### <a name="execution"></a>Exécution
Dans cet exemple, nous exécutons le code dans l’**environnement de calcul local**. Consultez les documents relatifs à Azure Machine Learning pour plus d’informations sur les [options d’exécution](experimentation-service-configuration.md).

L’exécution d’un script Python dans un runtime Python local est simple :

    az ml experiment submit -c local my_script.py

Vous pouvez double-cliquer sur les fichiers de bloc-notes IPython à partir de la structure du projet dans le volet gauche de l’interface utilisateur d’Azure Machine Learning et les exécuter sur le serveur de blocs-notes Jupyter.


Le flux de travail de science des données étape par étape était le suivant :

* [**Acquisition de données et compréhension**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/01_data_acquisition_and_understanding)

Les données ont été téléchargées au format .csv à partir des URL du référentiel ML de l’UCI [[lien]](https://archive.ics.uci.edu/ml/datasets/adult). Les caractéristiques, la cible et leurs transformations sont décrites en détail dans le fichier ProjectReport.md.

Le code pour l’acquisition et la compréhension de données se trouve sous : /code/01_data_acquisition_and_understanding.

L’exploration de données s’effectue à l’aide de l’[utilitaire IDEAR (Interactive Data Exploration and Reporting)](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils/Python) de Python 3 publié en tant que composant de la [suite TDSP d’outils de science des données](https://github.com/Azure/Azure-TDSP-Utilities). Cet utilitaire permet de générer des rapports d’exploration de données normalisés pour les données contenant la cible et des caractéristiques numériques et catégorielles. Des détails sur la façon dont l’utilitaire IDEAR de Python 3 a été utilisé sont fournis ci-dessous. 

L’emplacement du rapport d’exploration de données final est [IDEAR.html](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/docs/deliverable_docs). Le rapport IDEAR se présente comme suit :

![](./media/scenario-tdsp-classifying-us-incomes/idear.png)

* [**Modélisation**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/02_modeling)

Nous avons créé deux modèles avec une validation croisée à 3 plis : un modèle de réseau élastique et un modèle de forêt aléatoire. Nous avons utilisé un [échantillonnage à 59 points](http://www.jmlr.org/papers/volume13/bergstra12a/bergstra12a.pdf) pour la recherche de grilles aléatoires comme stratégie pour la validation croisée et l’optimisation des paramètres des modèles. La précision des modèles a été mesurée à l’aide de l’AUC (aire sous la courbe) sur le jeu de données de test. 

Le code pour la modélisation se trouve sous : /code/02_modeling.

L’AUC du modèle de réseau élastique et du modèle de forêt aléatoire était > 0,85. Nous enregistrons à présent les deux modèles dans des fichiers pickled.pkl et générons la sortie des tracés ROC pour les deux modèles. L’AUC du modèle de forêt aléatoire était de 0,92 et celui du modèle de réseau élastique de 0,90. De plus, pour permettre l’interprétation des modèles, l’importance des différentes caractéristiques pour le modèle de forêt aléatoire est enregistrée dans un fichier .csv et représentée graphiquement dans un fichier pdf (les 20 premières caractéristiques prédictives uniquement).

La courbe ROC du **modèle de forêt aléatoire** sur les données de test est illustrée ci-dessous. Voici le modèle qui a été déployé :

![](./media/scenario-tdsp-classifying-us-incomes/rf-auc.png)

L’importance des caractéristiques (les 20 premières) du modèle de forêt aléatoire est illustrée ci-dessous. Le graphique montre que le gain en capital, l’éducation et l’état civil sont les caractéristiques de plus grande importance.

![](./media/scenario-tdsp-classifying-us-incomes/featImportance.png)

* [**Déploiement**](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/tree/master/code/03_deployment)

Nous avons déployé le modèle de forêt aléatoire en tant que service web sur un cluster dans [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). L’environnement d’opérationnalisation configure Docker et Kubernetes dans le cluster pour gérer le déploiement du service web. Vous trouverez plus d’informations sur le processus d’opérationnalisation [ici](model-management-service-deploy.md). 

Le code pour le déploiement se trouve sous : /code/03_deployment.


### <a name="final-project-reporthttpsgithubcomazuremachinelearningsamples-tdspuciadultincomeblobmasterdocsdeliverabledocsprojectreportmd"></a>[Rapport de projet final](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md)
Des informations détaillées sur chacune des sections précédentes sont fournies dans le rapport de projet final compilé [ProjectReport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Le rapport de projet contient également des informations supplémentaires sur les cas d’usage, les mesures de performances des modèles, le déploiement et l’infrastructure sur laquelle le projet a été développé et déployé.

Le rapport de projet, ainsi que le contenu de l’ensemble du dossier du projet et le référentiel de la gestion de versions peuvent être remis au client.


## <a name="conclusion"></a>Conclusion

Dans cet exemple, nous avons montré comment utiliser la structure et les modèles TDSP dans Azure Machine Learning. Les modèles de documents et d’artefacts vous aident à :
1. Définir correctement l’objectif et la portée d’un projet
2. Créer une équipe de projet avec une répartition des rôles et responsabilités
3. Structurer et exécuter des projets en fonction des étapes du cycle de vie du processus TDSP
4. Développer des rapports normalisés à l’aide d’utilitaires de science des données TDSP (par exemple, le rapport d’exploration et d’affichage de données IDEAR).
5. Préparer un rapport de projet de science des données final qui peut être remis à un client

Nous espérons que vous exploiterez cette fonctionnalité d’Azure Machine Learning pour faciliter la normalisation et la collaboration au sein de vos équipes de science des données.

## <a name="next-steps"></a>Étapes suivantes

Consultez les références ci-dessous pour bien démarrer :

[Guide pratique pour utiliser Team Data Science Process (TDSP) dans Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)

[TDSP (Team Data Science Process)](https://github.com/Azure/Microsoft-TDSP)

[Modèle de projet TDSP pour Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)

[Jeu de données de revenus des États-Unis du référentiel ML de l’UCI](https://archive.ics.uci.edu/ml/datasets/adult)