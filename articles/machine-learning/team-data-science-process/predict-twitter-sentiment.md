---
title: Prédire le sentiment Twitter avec incorporations des mots à l’aide du processus TDSP (Team Data Science Process) dans Azure | Microsoft Docs
description: Étapes nécessaires pour exécuter vos projets de science des données.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev
ms.openlocfilehash: f22da892868a10ac18fdcd703249eaa172f8bf65
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Prédire le sentiment Twitter avec incorporations des mots à l’aide du processus TDSP (Team Data Science Process)

Cet article explique comment collaborer efficacement à l’aide de l’algorithme d’incorporation de mots _Word2Vec_ et de l’algorithme _SSWE (Sentiment-Specific Word Embedding)_ pour prédire le sentiment Twitter avec [Azure Machine Learning](../preview/index.yml). Pour plus d’informations sur la prédiction de la polarité du sentiment Twitter, consultez le [dépôt MachineLearningSamples-TwitterSentimentPrediction](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) sur GitHub. La clé pour favoriser une collaboration efficace des équipes sur les projets de science des données consiste à normaliser la structure et la documentation des projets avec un cycle de vie de science des données établi. Le [processus TDSP (Team Data Science Process)](overview.md) fournit ce type de [cycle de vie](lifecycle.md) structuré. 

La création de projets de science des données avec le _modèle TDSP_ fournit l’infrastructure normalisée pour les projets Azure Machine Learning. L’équipe TDSP a déjà publié un [dépôt GitHub pour la structure et les modèles de projet TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Désormais, les projets Machine Learning qui sont instanciés avec des [modèles TDSP pour Azure Machine Learning](https://github.com/amlsamples/tdsp) sont activés. Pour obtenir des instructions, consultez l’article qui explique comment utiliser des [projets de structure TDSP avec le modèle TDSP](../preview/how-to-use-tdsp-in-azure-ml.md) dans Azure Machine Learning. 


## <a name="twitter-sentiment-polarity-sample"></a>Exemple de polarité de sentiment Twitter

Cet article utilise un exemple pour illustrer comment instancier et exécuter un projet Machine Learning. Cet exemple utilise la structure et les modèles TDSP dans Azure Machine Learning Workbench. L’exemple complet est fourni dans [cette procédure pas à pas](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). La tâche de modélisation prédit la polarité de sentiment (positive ou négative) en utilisant le texte des tweets. Cet article présente les tâches de modélisation des données décrites dans la procédure pas à pas. La procédure pas à pas couvre les tâches suivantes :

- Exploration de données, apprentissage et déploiement d’un modèle d’apprentissage automatique solutionnant le problème de prédiction décrit dans la vue d’ensemble du cas d’usage. Les [données de sentiment Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) sont utilisées pour ces tâches.
- Exécution du projet à l’aide du modèle TDSP à partir d’Azure Machine Learning pour ce projet. Pour l’exécution du projet et la génération de rapports, le cycle de vie TDSP est utilisé.
- Opérationnalisation de la solution directement à partir d’Azure Machine Learning dans Azure Container Service.

Le projet met en évidence les fonctionnalités suivantes d’Azure Machine Learning :

- Instanciation et utilisation de la structure TDSP
- Exécution de code dans Azure Machine Learning Workbench
- Opérationnalisation simplifiée dans Container Service à l’aide de Docker et Kubernetes

## <a name="team-data-science-process"></a>TDSP (Team Data Science Process)
Pour exécuter cet exemple, vous utilisez les modèles de documentation et la structure de projet TDSP dans Azure Machine Learning Workbench. L’exemple implémente le [cycle de vie TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), comme illustré dans la figure suivante :

![Cycle de vie TDSP](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

Le projet TDSP est créé dans Azure Machine Learning Workbench en fonction de [ces instructions](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), comme illustré dans la figure suivante :

![Créer un processus TDSP dans Azure Machine Learning Workbench](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Préparation et acquisition de données](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
La première étape de cet exemple consiste à télécharger le jeu de données sentiment140 et à diviser les données en jeux de données d’apprentissage et de test. Le jeu de données sentiment140 contient le contenu réel du tweet (avec émoticônes supprimées). Il contient également la polarité de chaque tweet (négative = 0, positive = 4) avec les tweets neutres supprimés. Une fois les données divisées, les données d’apprentissage comportent 1,3 millions de lignes et les données de test comportent 320 000 lignes.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Développement d’un modèle](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

L’étape suivante de l’exemple consiste à développer un modèle pour les données. La tâche de modélisation est divisée en trois parties :

- Ingénierie des caractéristiques : générer des caractéristiques pour le modèle à l’aide de différents algorithmes d’incorporation de mots. 
- Création de modèle : former différents modèles pour prédire le sentiment du texte d’entrée. La _régression logistique_ et le _gradient Boosting_ sont des exemples de ces modèles.
- Évaluation de modèle : évaluer les modèles formés par rapport aux données de test.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Ingénierie des caractéristiques](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Les algorithmes Word2Vec et SSWE sont utilisés pour générer des incorporations de mots. 


#### <a name="word2vec-algorithm"></a>Algorithme Word2Vec

L’algorithme Word2Vec est utilisé dans le modèle Skip-Gram. Ce modèle est expliqué dans l’article de Tomas Mikolov et al., intitulé « [Distributed Representations of Words and Phrases and their Compositionality. Advances in neural information processing systems.](https://arxiv.org/abs/1310.4546) (Représentations distribuées des mots et des phrases dans leur compositionnalité. Avances dans le domaine des systèmes de traitement d’informations neuronales) » (2013).

Le modèle Skip-Gram est un réseau neuronal superficiel. L’entrée est le mot cible encodé sous la forme d’un vecteur à chaud, qui est utilisé pour prédire les mots à proximité. Si **V** est la taille du vocabulaire, alors la taille de la couche de sortie est __C*V__, où C est la taille de la fenêtre de contexte. La figure suivante illustre une architecture basée sur le modèle Skip-Gram :

![Modèle Skip-Gram](./media/predict-twitter-sentiment/skip-gram-model.PNG)

Les mécanismes détaillés de l’algorithme Word2Vec et du modèle Skip-Gram dépassent le cadre de cet exemple. Pour plus d’informations, consultez les références suivantes :

- [02_A_Word2Vec.py code with referenced TensorFlow examples (Code 02_A_Word2Vec.py avec exemples TensorFlow référencés)](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec)
- [How exactly does word2vec work? (Comment word2vec fonctionne-t-il exactement ?)](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Notes on Noise Contrastive Estimation and Negative Sampling (Remarques sur l’estimation contrastive du bruit et l’échantillonnage négatif)](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Algorithme SSWE (Sentiment-Specific Word Embedding)
L’algorithme SSWE tente d’atténuer une faiblesse de l’algorithme Word2Vec selon laquelle les mots avec des contextes similaires et une polarité inverse peuvent avoir des vecteurs de mots similaires. Les similitudes peuvent provoquer une dégradation des performances de l’algorithme Word2Vec pour des tâches telles que l’analyse des sentiments. L’algorithme SSWE essaie de gérer cette faiblesse en incorporant à la fois la polarité de la phrase et le contexte du mot dans sa fonction de perte.

L’exemple utilise une variante de l’algorithme SSWE comme suit :

- Le _ngram_ d’origine et le _ngram_ endommagé sont tous deux utilisés comme entrées.
- Une fonction de perte charnière de style de classement est utilisée pour la perte syntaxique et la perte sémantique.
- La dernière fonction de perte est la combinaison pondérée de la perte syntaxique et de la perte sémantique.
- Par souci de simplicité, seule l’entropie croisée sémantique est utilisée comme fonction de perte.

L’exemple montre que, même avec la fonction de perte plus simple, les performances de l’incorporation SSWE sont meilleures que celles de l’incorporation Word2Vec. La figure suivante présente le modèle convolutionnel utilisé pour générer l’incorporation de mot propre au sentiment :

![Modèle de réseau neuronal inspiré par SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

Une fois le processus d’apprentissage terminé, deux fichiers d’incorporation au format de valeurs séparées par une tabulation (TSV) sont générés pour la phase de modélisation.

Pour plus d’informations sur les algorithmes SSWE, consultez l’article de Duyu Tang, et al. intitulé « [Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification (Apprentissage de l’incorporation propre au sentiment pour la classification des sentiments Twitter](http://www.aclweb.org/anthology/P14-1146)) » Association for Computational Linguistics (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Création de modèle](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Une fois les vecteurs de mots générés à l’aide de l’algorithme SSWE ou Word2Vec, les modèles de classification sont formés pour prédire la polarité du sentiment. Deux types de caractéristiques, Word2Vec et SSWE, sont appliqués à deux modèles : le modèle Gradient Boosting et le modèle de régression logistique. Ainsi, quatre modèles différents sont formés.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Évaluation de modèle](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Une fois les modèles formés, ils sont utilisés pour tester des données de texte Twitter et évaluer les performances de chaque modèle. L’exemple évalue les quatre modèles suivants :

- Gradient Boosting par rapport à l’incorporation SSWE
- Régression logistique par rapport à l’incorporation SSWE
- Gradient Boosting par rapport à l’incorporation Word2Vec
- Régression logistique par rapport à l’incorporation Word2Vec

Une comparaison des performances des quatre modèles est indiquée dans l’illustration suivante :

![Comparaison des modèles ROC (Receiver Operating Characteristic)](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

Le modèle Gradient Boosting avec la caractéristique SSWE offre de meilleures performances si l’on compare les modèles à l’aide de la métrique AUC (Area Under Curve).


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Déploiement](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

L’étape finale est le déploiement du modèle de prédiction de sentiment formé sur un service web sur un cluster dans Azure Container Service. L’exemple utilise le modèle Gradient Boosting avec l’algorithme d’incorporation SSWE en tant que modèle formé. L’environnement d’opérationnalisation provisionne Docker et Kubernetes dans le cluster pour gérer le déploiement du service web, comme illustré ci-dessous : 

![Tableau de bord Kubernetes](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Pour plus d’informations sur le processus d’opérationnalisation, consultez [Déploiement d’un modèle d’apprentissage automatique comme service web](../preview/model-management-service-deploy.md).

## <a name="conclusion"></a>Conclusion

Dans cet article, vous avez appris comment former un modèle d’incorporation de mots en utilisant les algorithmes Word2Vec et SSWE. Les incorporations extraites ont été utilisées en tant que caractéristiques pour l’apprentissage de plusieurs modèles afin de prédire les scores de sentiment pour des données de texte Twitter. C’est la caractéristique SSWE utilisée avec le modèle Gradient Boosting qui a donné les meilleures performances. Le modèle a ensuite été déployé en tant que service web en temps réel dans Container Service à l’aide d’Azure Machine Learning Workbench.


## <a name="references"></a>Références

* [Processus Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Guide pratique pour utiliser Team Data Science Process (TDSP) dans Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Modèle de projet TDSP pour Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](../preview/index.yml)
* [Jeu de données de revenus des États-Unis du référentiel ML de l’UCI](https://archive.ics.uci.edu/ml/datasets/adult)
* [Reconnaissance d’entités biomédicales à l’aide du modèle Team Data Science Process (TDSP)](../preview/scenario-tdsp-biomedical-recognition.md)
* [Mikolov, Tomas, et al. « Distributed representations of words and phrases and their compositionality. Advances in neural information processing systems. (Représentations distribuées des mots et des phrases dans leur compositionnalité. Avances dans le domaine des systèmes de traitement d’informations neuronales) » 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu, et al. « Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification (Apprentissage de l’incorporation propre au sentiment pour la classification des sentiments Twitter) » ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
