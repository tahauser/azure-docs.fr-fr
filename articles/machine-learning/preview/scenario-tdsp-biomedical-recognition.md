---
title: "Reconnaissance d’entités biomédicales - Team Data Science Process - Azure Machine Learning | Microsoft Docs"
description: "Démarrage rapide d’un projet Team Data Science Process utilisant un apprentissage approfondi pour la reconnaissance d’entités biomédicales dans Azure Machine Learning Workbench."
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
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 7de3a30e477fcec66ce703b6c3fec7d17d79d3ab
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Reconnaissance d’entités biomédicales à l’aide du modèle Team Data Science Process (TDSP)

L’extraction d’entités est une sous-tâche de l’extraction d’informations (également appelée [reconnaissance d’entités nommées (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), segmentation d’entités et identification d’entités). L’objectif de ce scénario réel est d’expliquer comment utiliser Azure Machine Learning Workbench pour résoudre une tâche de traitement de langage naturel (NLP) complexe, telle que l’extraction d’entités à partir d’un texte non structuré. Pour ce faire, nous allons :

1. Expliquer comment effectuer l’apprentissage d’un modèle d’incorporation de mots neuronaux sur un corpus de texte d’environ 18 millions d’extraits de PubMed à l’aide d’une [implémentation de Word2Vec sur Spark](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Expliquer comment créer un modèle de réseau neuronal récurrent et profond Long Short-Term Memory (LSTM) pour l’extraction d’entités sur une machine virtuelle de science des données Azure GPU sur Azure.
2. Démontrer que ce modèle d’incorporation de mots propre au domaine peut être plus efficace que les modèles d’incorporation de mots génériques pour la tâche de reconnaissance d’entités. 
3. Montrer comment effectuer l’apprentissage et l’opérationnalisation des modèles d’apprentissage approfondi à l’aide d’Azure Machine Learning Workbench.

4. Décrire les fonctionnalités suivantes dans Azure Machine Learning Workbench :

    * Instanciation de [la structure et des modèles Team Data Science Process (TDSP)](how-to-use-tdsp-in-azure-ml.md)
    * Gestion automatisée des dépendances de votre projet, y compris le téléchargement et l’installation
    * Exécution de scripts Python dans différents environnements de calcul
    * Suivi de l’historique des exécutions de scripts Python
    * Exécution de tâches dans un contexte de calcul Spark distant à l’aide de clusters HDInsight Spark 2.1
    * Exécution de tâches sur des machines virtuelles GPU distantes sur Azure
    * Opérationnalisation simple de modèles d’apprentissage approfondi en tant que services web sur Azure Container Service (ACS)

## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage
La reconnaissance d’entités dites biomédicales est une étape essentielle pour les tâches NLP biomédicales complexes telles que les suivantes : 
* Extraction des mentions d’entités nommées comme des maladies, médicaments, produits chimiques et symptômes dans des dossiers médicaux ou de soins électroniques.
* Détection de médicaments
* Compréhension des interactions entre les différents types d’entité, par exemple l’interaction médicament-médicament, la relation médicament-maladie et la relation de gène-protéine.

Notre scénario d’utilisation porte principalement sur la façon dont un grand corpus de données non structurées, comme des extraits de Medline PubMed, peut être analysé pour l’apprentissage d’un modèle d’incorporation de mots. Les incorporations de sortie sont ensuite considérées comme des fonctionnalités générées automatiquement pour l’apprentissage d’un extracteur d’entités neuronales.

Nos résultats montrent que l’apprentissage du modèle d’extraction d’entités médicales sur les fonctionnalités d’incorporation de mots propres au domaine est plus efficace que le modèle formé sur le type de fonctionnalités génériques. Le modèle propre au domaine peut détecter correctement 7 012 entités (sur 9 475) avec un score F1 de 0,73 par rapport aux 5 274 entités avec un score F1 de 0,61 pour le modèle générique.

La figure suivante montre l’architecture qui a été utilisée pour traiter les données et effectuer l’apprentissage des modèles.

![Architecture](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Description des données

### <a name="1-word2vec-model-training-data"></a>1. Données d’apprentissage de modèles Word2Vec
Nous avons d’abord téléchargé les données brutes d’extraits de MEDLINE à partir du site de [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Les données sont accessibles publiquement sous forme de fichiers XML sur leur [serveur FTP](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Un total de 892 fichiers XML sont disponibles sur le serveur et chacun d’eux contient les informations de 30 000 articles. Vous trouverez plus de détails sur l’étape de collecte de données dans la section Structure du projet. Les champs présents dans chaque fichier sont les suivants : 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. Données d’apprentissage de modèles LSTM

L’apprentissage du modèle d’extraction d’entités neuronales a été effectué et ce dernier a été évalué sur des jeux de données accessibles publiquement. Pour obtenir une description détaillée de ces jeux de données, vous pouvez consulter les sources suivantes :
 * [Tâche de reconnaissance d’entités biomédicales au BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Corpus de tâche CDR de BioCreative V](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - Tâche 9.1 (reconnaissance de médicaments)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie Azure
Le lien vers le dépôt GitHub public du scénario réel qui contient le code et une description plus détaillée est le suivant : 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Prérequis 

* Un abonnement [Azure](https://azure.microsoft.com/free/)
* Azure Machine Learning Workbench. Consultez le [guide d’installation](quickstart-installation.md). Actuellement, Azure Machine Learning Workbench peut uniquement être installé sur les systèmes d’exploitation suivants : 
    * Windows 10 ou Windows Server 2016
    * macOS Sierra (ou une version ultérieure)


### <a name="azure-services"></a>Services Azure
* [Cluster HDInsight Spark](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) version 2.1 de Spark sur Linux (HDI 3.6) pour le calcul de la montée en puissance parallèle. Pour traiter l’ensemble des extraits de MEDLINE mentionnés ci-dessous, la configuration minimale suivante est requise : 
    * Nœud principal : taille [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/).
    * Nœuds Worker : au moins 4 de taille [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). Pour notre travail, nous avons utilisé 11 nœuds Worker de taille D12_V2.
* [Machine virtuelle de science des données (DSVM) NC6](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) pour le calcul de la montée en puissance.

### <a name="python-packages"></a>Packages Python

Toutes les dépendances requises sont définies dans le fichier aml_config/conda_dependencies.yml sous le dossier de projet du scénario. Les dépendances définies dans ce fichier seront automatiquement configurées pour des exécutions en fonction du Docker, de la machine virtuelle et du cluster HDI cibles. Vous trouverez plus d’informations sur le format de fichier d’environnement Conda [ici](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Instructions de base pour Azure Machine Learning (AML) Workbench
* [Vue d’ensemble](overview-what-is-azure-ml.md)
* [Installation](quickstart-installation.md)
* [Utilisation de TDSP](how-to-use-tdsp-in-azure-ml.md)
* [Lecture et écriture de fichiers](how-to-read-write-files.md)
* [Utilisation de blocs-notes Jupyter](how-to-use-jupyter-notebooks.md)
* [Utilisation de GPU](how-to-use-gpu.md)

## <a name="scenario-structure"></a>Structure du scénario
Pour ce scénario, nous utilisons la structure de projet et les modèles de documentation TDSP (Figure 1), qui suivent le [cycle de vie du processus TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Le projet est créé conformément aux instructions fournies [ici](./how-to-use-tdsp-in-azure-ml.md).


![Indication des informations sur le projet](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

Le flux de travail de science des données étape par étape est le suivant :

### <a name="1-data-acquisition-and-understanding"></a>1. Acquisition de données et compréhension

Consultez [Acquisition de données et compréhension](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

Le corpus de données brutes de MEDLINE inclut un total de 27 millions d’extraits dont environ 10 millions d’articles ont un champ d’extrait vide. Azure HDInsight Spark est utilisé pour traiter le Big Data qui ne peut pas être chargé dans la mémoire d’une seule machine en tant que [cadre de données Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Les données sont d’abord téléchargées dans le cluster Spark. Les étapes suivantes sont ensuite effectuées sur le [cadre de données Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) : 
* Analyse des fichiers XML à l’aide de l’analyseur XML de Medline
* Prétraitement du texte extrait, y compris la division des phrases, la segmentation du texte en unités lexicales et la normalisation des cas
* Exclusion des articles où le champ d’extrait est vide ou contient un texte court 
* Création du vocabulaire à partir des extraits d’apprentissage
* Apprentissage du modèle d’incorporation de mots neuronaux Pour plus d’informations, accédez au [lien vers les codes GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) pour bien démarrer.


Après l’analyse des fichiers XML, les données ont le format suivant : 

![Exemple de données](./media/scenario-tdsp-biomedical-recognition/datasample.png)

L’apprentissage du modèle d’extraction d’entités neuronales a été effectué et ce dernier a été évalué sur des jeux de données accessibles publiquement. Pour obtenir une description détaillée de ces jeux de données, vous pouvez consulter les sources suivantes :
 * [Tâche de reconnaissance d’entités biomédicales au BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [Corpus de tâche CDR de BioCreative V](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - Tâche 9.1 (reconnaissance de médicaments)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modélisation

Consultez [Modélisation](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

La modélisation est l’étape où nous expliquerons comment vous pouvez utiliser les données téléchargées dans la section précédente pour effectuer l’apprentissage de votre propre modèle d’incorporation de mots et l’utiliser pour d’autres tâches en aval. Bien que nous utilisions les données PubMed, le pipeline pour générer les incorporations est générique et peut être réutilisé pour l’apprentissage d’incorporations de mots pour tout autre domaine. Pour que les incorporations soient une représentation exacte des données, il est essentiel d’effectuer l’apprentissage du word2vec sur une grande quantité de données.
Une fois les incorporations de mots prêtes, nous pouvons effectuer l’apprentissage d’un modèle de réseau neuronal profond qui utilise les incorporations apprises pour initialiser la couche d’incorporation. Nous marquons la couche d’incorporation comme non apte à l’apprentissage, mais ce n’est pas obligatoire. L’apprentissage du modèle d’incorporation de mots n’étant pas supervisé, nous pouvons tirer parti des textes sans étiquette. En revanche, l’apprentissage du modèle de reconnaissance d’entités est une tâche d’apprentissage supervisée et sa précision dépend de la quantité et de la qualité des données annotées manuellement. 


#### <a name="21-feature-generation"></a>2.1. Génération de fonctionnalités

Consultez [Génération de fonctionnalités](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec est l’algorithme d’apprentissage libre d’incorporation de mots qui entraîne un modèle de réseau neuronal à partir d’un corpus d’apprentissage sans étiquette. Il produit un vecteur continu pour chaque mot du corpus qui représente ses informations sémantiques. Ces modèles sont des réseaux neuronaux simples avec une seule couche masquée. Les vecteurs/incorporations de mots sont appris par rétropropagation et SGD (Stochastic Gradient Descent). Il existe deux types de modèles word2vec, à savoir Skip-Gram et continuous-bag-of-words (cliquez [ici](https://arxiv.org/pdf/1301.3781.pdf) pour plus d’informations). Étant donné que nous utilisons l’implémentation de word2vec de MLlib, qui prend en charge le modèle de Skip-Gram, nous décrirons brièvement ce dernier ici (image extraite de ce [lien](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)) : 

![Modèle Skip-Gram](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

Le modèle utilise un échantillonnage de Hierarchical Softmax et Negative pour optimiser les performances. SoftMax hiérarchique (H-SoftMax) est une approximation qui s’inspire des arbres binaires. H-SoftMax remplace essentiellement la couche SoftMax plane par une couche hiérarchique où les mots apparaissent comme des nœuds terminaux. Cela permet de décomposer le calcul de la probabilité d’un mot dans une séquence de calculs de probabilité, ce qui évite d’avoir à calculer la normalisation coûteuse sur tous les mots. Étant donné qu’un arbre binaire a une profondeur de log2(|V|) (où V est le vocabulaire), nous avons uniquement besoin d’évaluer tout au plus les nœuds de log2(|V|) pour obtenir la probabilité finale d’un mot. La probabilité d’un mot w en fonction de son contexte c correspond alors simplement au produit des probabilités de prendre des déviations à droite et à gauche respectivement qui mènent à son nœud terminal. Nous pouvons créer un arbre de Huffman en fonction de la fréquence des mots dans le jeu de données pour s’assurer que les mots les plus fréquents aient des représentations plus courtes. Pour plus d’informations, consultez [ce lien](http://sebastianruder.com/word-embeddings-softmax/).
Image extraite de [cette page](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Visualisation

Une fois les incorporations de mots effectuées, nous souhaiterions les afficher et voir la relation qui existe entre les mots similaires d’un point de vue sémantique. 

![Similarité W2V](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Nous avons décrit deux manières différentes d’afficher les incorporations. La première utilise PCA pour projeter le vecteur à haute dimensionnalité dans un espace vectoriel 2D. Cela entraîne une perte significative d’informations et l’affichage n’est pas aussi précis. La seconde consiste à utiliser PCA avec [t-SNE](https://distill.pub/2016/misread-tsne/). t-SNE est une technique de réduction de dimensionnalité non linéaire idéale pour l’incorporation de données à haute dimensionnalité dans un espace en deux ou trois dimensions, qui peuvent ensuite être affichées dans un nuage de points.  Cette technique modélise chaque objet à haute dimensionnalité par un point en deux ou trois dimensions de manière à ce que les objets similaires soient modélisés par des points proches et que les objets différents soient modélisés par des points distants. Elle fonctionne en deux parties. Dans un premier temps, elle crée une distribution de probabilité sur les paires dans l’espace à plus haute dimensionnalité de manière à ce que la probabilité de sélectionner des objets similaires soit élevée et que celle de sélectionner des points différents soit faible. Elle définit ensuite une distribution de probabilité similaire sur les points dans un mappage à faible dimensionnalité et réduit la divergence KL entre ces deux distributions au niveau de l’emplacement des points sur le mappage. L’emplacement des points dans le mappage à faible dimensionnalité est obtenu en réduisant la divergence KL à l’aide de la descente de gradient (Gradient Descent). Mais t-SNE peut ne pas toujours être fiable. Pour des informations détaillées sur l’implémentation, cliquez [ici](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


Comme le montre la figure suivante, l’affichage de t-SNE présente une plus grande séparation des vecteurs de mots et des modèles de clustering potentiels. 


* Affichage avec PCA

![PCA](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Affichage avec t-SNE

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* Points les plus proches de « cancer » (ce sont tous des sous-types de cancer)

![Points les plus proches de cancer](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. Effectuer l’apprentissage de l’extracteur d’entités neuronales

Consultez [Effectuer l’apprentissage de l’extracteur d’entités neuronales](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

L’architecture de réseau neuronal en aval présente l’inconvénient de traiter chaque entrée et chaque sortie indépendamment des autres entrées et sorties. Cette architecture ne peut pas modéliser les tâches d’étiquetage séquence-à-séquence, telles que la traduction automatique et l’extraction d’entités. Les modèles de réseau neuronal récurrent surmontent ce problème, car ils peuvent passer des informations calculées jusqu’à maintenant au nœud suivant. Cette propriété n’est autre qu’avoir de la mémoire réseau, car elle est capable d’utiliser les informations précédemment calculées, comme le montre la figure suivante :

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Les RNN vanille présentent en fait le [problème de disparition de gradient](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) dû au fait qu’ils ne sont pas capables d’utiliser toutes les informations auxquelles ils ont eu accès auparavant. Ce problème est uniquement évident quand un contexte important est requis pour effectuer une prédiction. Mais les modèles comme LSTM ne sont pas affectés par ce problème, car ils sont en fait conçus pour mémoriser les dépendances à long terme. Contrairement aux RNN vanille qui ont un seul réseau neuronal, les LSTM ont les interactions entre quatre réseaux neuronaux pour chaque cellule. Pour une description détaillée du fonctionnement des LSTM, consultez [cette publication](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![Cellule LSTM](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Essayons de concevoir notre propre réseau neuronal récurrent LSTM et d’extraire des types d’entité comme des références à des médicaments, des maladies et des symptômes à partir de données de PubMed. La première étape consiste à obtenir une grande quantité de données étiquetées, et comme vous pouvez l’imaginer, ce n’est pas une tâche facile ! La plupart des données médicales contiennent un grand nombre d’informations sensibles sur les individus qui ne sont donc pas accessibles publiquement. Nous travaillons à partir d’une combinaison de deux jeux de données différents accessibles publiquement. Le premier jeu de données provient de Semeval 2013 - Tâche 9.1 (reconnaissance de médicaments) et le deuxième provient de la tâche CDR de BioCreative V. Nous combinons et effectuons l’étiquetage automatique de ces deux jeux de données pour pouvoir détecter à la fois les références à des médicaments et des maladies dans des textes médicaux et évaluer nos incorporations de mots. Pour des informations détaillées sur l’implémentation, accédez au [lien vers les codes GitHub](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

L’architecture de modèle que nous avons utilisée pour tous les codes et pour la comparaison est présentée ci-dessous. Le paramètre qui change pour les différents jeux de données est la longueur maximale de la séquence (613 ici).

![Modèle LSTM](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Évaluation de modèle
Voir [Évaluation de modèle](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

Nous utilisons le script d’évaluation de la tâche partagée [Tâche de reconnaissance d’entités biomédicales au BioNLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) pour évaluer la précision, le rappel et le score F1 du modèle. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>Modèle d’incorporation de mots propre au domaine versus modèle générique

Comparons à présent la précision de deux types de fonctionnalité : (1) les incorporations de mots formées sur les extraits de PubMed et (2) les incorporations de mots formées sur Google Actualités. Nous pouvons clairement observer que le modèle propre au domaine est plus précis que le modèle générique. Il est par conséquent plus utile d’avoir un modèle d’incorporation de mots spécifique plutôt qu’un modèle générique. 

* Tâche 1 : Détection de médicaments et maladies

![Comparaison des modèles 1](./media/scenario-tdsp-biomedical-recognition/mc1.png)

Nous procédons à l’évaluation des incorporations de mots sur d’autres jeux de données de manière similaire et observons que le modèle propre au domaine est toujours mieux.

* Tâche 2 : Détection de protéines, lignes de cellules, types de cellules, ADN et ARN

![Comparaison des modèles 2](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* Tâche 3 : Détection de produits chimiques et maladies

![Comparaison des modèles 3](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* Tâche 4 : Détection de médicaments

![Comparaison des modèles 4](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* Tâche 5 : Détection de gènes

![Comparaison des modèles 5](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow versus CNTK
Tous les modèles mentionnés sont formés à l’aide de Keras avec TensorFlow comme serveur principal. Keras avec le serveur principal CNTK ne prend pas en charge « reverse » au moment de la réalisation de ce travail. C’est pourquoi, à titre de comparaison, nous avons effectué l’apprentissage d’un modèle LSTM unidirectionnel avec le serveur principal CNTK et l’avons comparé à un modèle LSTM unidirectionnel avec le serveur principal TensorFlow. Installez CNTK 2.0 pour Keras à partir de [ce lien](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![Comparaison des modèles 6](./media/scenario-tdsp-biomedical-recognition/mc6.png)

Nous en avons tiré la conclusion que CNTK fonctionne aussi bien que Tensorflow à la fois en termes de durée d’apprentissage par époque (60 secondes pour CNTK et 75 secondes pour Tensorflow) et de nombre d’entités de test détectées. Nous utilisons les couches unidirectionnelles pour l’évaluation.


### <a name="3-deployment"></a>3. Déploiement

Consultez [Déploiement](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Nous avons déployé un service web sur un cluster dans [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). L’environnement d’opérationnalisation configure Docker et Kubernetes dans le cluster pour gérer le déploiement du service web. Vous trouverez plus d’informations sur le processus d’opérationnalisation [ici](model-management-service-deploy.md ).


## <a name="conclusion"></a>Conclusion

Nous avons décrit en détail comment vous pouvez effectuer l’apprentissage d’un modèle d’incorporation de mots à l’aide de l’algorithme Word2Vec sur Spark, puis utiliser les incorporations extraites en tant que fonctionnalités pour effectuer l’apprentissage d’un réseau neuronal profond pour l’extraction d’entités. Nous avons appliqué le pipeline d’apprentissage au domaine biomédical. Néanmoins, le pipeline est assez générique pour être appliqué pour détecter les types d’entité personnalisés de tout autre domaine. Vous devez simplement disposer de données suffisantes pour pouvoir adapter facilement le flux de travail présenté ici à un autre domaine.

## <a name="references"></a>Références

* Tomas Mikolov, Kai Chen, Greg Corrado et Jeffrey Dean. 2013a. Efficient estimation of word representations in vector space. In Proceedings of ICLR.
* Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S Corrado et Jeff Dean. 2013b. Distributed representations of words and phrases and their compositionality. In Proceedings of NIPS, pages 3111–3119.
* Billy Chiu, Gamal Crichton, Anna Korhonen et Sampo Pyysalo. 2016. [How to Train Good Word Embeddings for Biomedical NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), In Proceedings of the 15th Workshop on Biomedical Natural Language Processing, pages 166–174.
* [Vector Representations of Words](https://www.tensorflow.org/tutorials/word2vec)
* [Recurrent Neural Networks](https://www.tensorflow.org/tutorials/recurrent)
* [Problems encountered with Spark ml Word2Vec](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: lessons learned](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)

