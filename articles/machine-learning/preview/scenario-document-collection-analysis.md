---
title: "Analyse d’une collection de documents - Azure | Microsoft Docs"
description: "Décrit comment effectuer la synthèse et l’analyse d’une grande collection de documents, en utilisant des techniques telles que l’apprentissage des expressions, la modélisation des thèmes et l’analyse du modèle thématique à l’aide d’Azure ML Workbench."
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: a6034652f27765bb20db4dbbb4c25741b261e50a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="document-collection-analysis"></a>Analyse d’une collection de documents

Ce scénario montre comment effectuer la synthèse et l’analyse d’une grande collection de documents, en utilisant des techniques telles que l’apprentissage des expressions, la modélisation des thèmes et l’analyse du modèle thématique à l’aide d’Azure ML Workbench. Azure Machine Learning Workbench facilite la mise à l’échelle de grandes collections de documents et fournit les mécanismes nécessaires pour former et optimiser des modèles dans divers contextes de calcul, allant du calcul local aux machines virtuelles DSVM et au cluster Spark. Le développement s’effectue simplement par le biais des notebooks Jupyter dans Azure Machine Learning Workbench.

## <a name="link-to-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie

Le dépôt GitHub public correspondant à ce scénario réel contient tous les supports, y compris les exemples de code, nécessaires pour cet exemple :

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Vue d’ensemble

La quantité de données collectées quotidiennement est énorme (en particulier, les données texte non structurées). L’organisation, la recherche et l’analyse de grandes collections de documents sont aujourd’hui un enjeu majeur pour les entreprises. Ce scénario d’analyse d’une collection de documents illustre un workflow de bout en bout performant et automatisé pour analyser une grande collection de documents et effectuer des tâches NLP en aval.

Ce scénario expose les principaux points suivants :

1. Apprentissage des principales expressions multimots des documents.

1. Apprentissage des thèmes sous-jacents présentés dans la collection de documents.

1. Représentation des documents par la distribution thématique.

1. Présentation des méthodes d’organisation, de recherche et de synthèse des documents en fonction du contenu thématique.

Les méthodes présentées dans ce scénario peuvent s’appliquer à diverses tâches d’entreprise critiques, telles que la détection d’anomalie et des tendances thématiques, la synthèse des collections de documents et la recherche de documents similaires. Elles peuvent être utilisées pour de nombreux types d’analyse de document différents, comme les textes législatifs, les actualités, les revues de produit, les commentaires client et les articles de recherche scientifique.

Ce scénario utilise les techniques et algorithmes d’apprentissage automatique suivants :

1. Traitement et nettoyage du texte

1. Apprentissage des expressions

1. Modélisation de rubrique

1. Synthèse du corpus

1. Détection d’anomalie et des tendances thématiques

## <a name="prerequisites"></a>Prérequis

Cet exemple nécessite les prérequis suivants :

* Vérifiez que vous avez correctement installé [Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) selon les instructions du [Guide de démarrage rapide Installer et créer](quickstart-installation.md).

* Vous pouvez exécuter cet exemple dans n’importe quel contexte de calcul. Toutefois, nous vous recommandons d’utiliser une machine multicœur ayant au minimum 16 Go de mémoire et 5 Go d’espace disque.

## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Azure Machine Learning Workbench
2.  Dans la page **Projets**, cliquez sur le signe **+**, puis sélectionnez **Nouveau projet**
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « Analyse de collection de documents » et sélectionnez le modèle
5.  Cliquez sur **Créer**

## <a name="data-description"></a>Description des données

Le jeu de données utilisé dans ce scénario contient des synthèses de textes et les métadonnées associées pour chaque action législative présentée par le Congrès des États-Unis. Les données collectées proviennent du site [GovTrack.us](https://www.govtrack.us/), qui suit les activités du Congrès des États-Unis et offre aux américains un moyen de participer au processus législatif de leur pays. Vous pouvez télécharger ces données en bloc par le biais de [ce lien](https://www.govtrack.us/data/congress/) à l’aide d’un script manuel (non fourni dans ce scénario). Les instructions pour télécharger les données sont consultables dans la [documentation de l’API GovTrack](https://www.govtrack.us/developers/api).

### <a name="data-source"></a>Source de données

Dans ce scénario, les données brutes collectées concernent une série d’actions législatives (propositions de lois et résolutions) présentées par le Congrès américain entre 1973 et juin 2017 (entre les 93e et 115e sessions du Congrès). Les données collectées au format JSON comportent de nombreuses informations sur les actions législatives. Pour obtenir une description détaillée des champs de données, consultez [ce lien GitHub](https://github.com/unitedstates/congress/wiki/bills). Pour les besoins de la démonstration dans ce scénario, nous avons seulement extrait certains champs de données contenus dans les fichiers JSON. Ce scénario fournit un fichier TSV précompilé, `CongressionalDataAll_Jun_2017.tsv`, qui contient les enregistrements des actions législatives. Ce fichier TSV peut être téléchargé automatiquement par les notebooks `1_Preprocess_Text.ipynb` dans le dossier des notebooks ou par `preprocessText.py` dans le package Python.

### <a name="data-structure"></a>Structure des données

Le fichier de données contient neuf champs de données, répertoriés et décrits dans le tableau ci-dessous.

| Nom du champ | type | Description | Contient une valeur manquante |
|------------|------|-------------|---------------|
| `ID` | Chaîne | ID de la loi ou résolution. Format de ce champ : [type_loi][numéro]-[congrès]. Par exemple, dans « hconres1-93 », le type de loi est « hconres » (pour House Concurrent Resolution, comme indiqué dans [ce document](https://github.com/unitedstates/congress/wiki/bills#basic-information)), le numéro de loi est « 1 » et le numéro de congrès est « 93 ». | Non  |
| `Text` | Chaîne | Texte de la loi ou résolution. | Non  |
| `Date` | Chaîne | Date à laquelle la loi ou résolution a été initialement proposée. Format : « aaaa-mm-jj ». | Non  |
| `SponsorName` | Chaîne | Nom du promoteur principal ayant proposé la loi ou résolution. | OUI |
| `Type` | Chaîne | Titre du promoteur principal : « rep » (représentant) ou « sen » (sénateur). | OUI |
| `State` | Chaîne | État du promoteur principal. | OUI |
| `District` | Entier  | Numéro du district du promoteur principal si le promoteur a le titre de représentant. | OUI |
| `Party` | Chaîne | Parti du promoteur principal. | OUI |
| `Subjects` | Chaîne | Termes des sujets de la loi successivement ajoutés dans la Bibliothèque du Congrès. Les termes sont séparés par des virgules. Ces termes sont ajoutés manuellement dans la Bibliothèque du Congrès. Ils ne figurent généralement pas dans les informations sur la loi initialement publiées. Ils peuvent être ajoutés à tout moment. Ils peuvent aussi ne plus être pertinents à un moment du cycle de vie de la loi. | OUI |

## <a name="scenario-structure"></a>Structure du scénario

L’exemple d’analyse de collection de documents est structuré en deux types de livrables. Le premier type est une série de notebooks iPython qui décrivent pas à pas l’intégralité du workflow. Le deuxième type est un package Python, accompagné de quelques exemples de code montrant comment l’utiliser. Le package Python est suffisamment générique pour servir dans de nombreux cas d’usage.

Les fichiers utilisés dans cet exemple sont structurés comme suit.

| Nom de fichier | type | Description |
|-----------|------|-------------|
| `aml_config` | Dossier | Dossier de configuration d’Azure Machine Learning Workbench (consultez [cette documentation](./experimentation-service-configuration-reference.md) pour plus de détails sur la configuration de l’exécution de l’expérimentation) |
| `Code` | Dossier | Dossier de code utilisé pour enregistrer les scripts Python et le package Python |
| `Data` | Dossier | Dossier de données utilisé pour enregistrer les fichiers intermédiaires |
| `notebooks` | Dossier | Dossier des notebooks Jupyter |
| `Code/documentAnalysis/__init__.py` | Fichier Python | Fichier init du package Python |
| `Code/documentAnalysis/configs.py` | Fichier Python | Fichier de configuration utilisé par le package Python d’analyse de document, contenant les constantes prédéfinies |
| `Code/documentAnalysis/preprocessText.py` | Fichier Python | Fichier Python utilisé pour prétraiter les données nécessaires aux tâches en aval |
| `Code/documentAnalysis/phraseLearning.py` | Fichier Python | Fichier Python utilisé pour apprendre les expressions à partir des données et transformer les données brutes |
| `Code/documentAnalysis/topicModeling.py` | Fichier Python | Fichier Python utilisé pour former un modèle de thèmes LDA (Latent Dirichlet Allocation) |
| `Code/step1.py` | Fichier Python | Étape 1 de l’analyse d’une collection de documents : prétraiter le texte |
| `Code/step2.py` | Fichier Python | Étape 2 de l’analyse d’une collection de documents : apprendre les expressions |
| `Code/step3.py` | Fichier Python | Étape 3 de l’analyse d’une collection de documents : former et évaluer le modèle de thèmes LDA |
| `Code/runme.py` | Fichier Python | Exemple d’exécution de toutes les étapes dans un seul fichier |
| `notebooks/1_Preprocess_Text.ipynb` | Notebook iPython | Utilisé pour prétraiter le texte et transformer les données brutes |
| `notebooks/2_Phrase_Learning.ipynb` | Notebook iPython | Utilisé pour apprendre les expressions à partir des données texte (après la transformation des données) |
| `notebooks/3_Topic_Model_Training.ipynb` | Notebook iPython | Utilisé pour former le modèle de thèmes LDA |
| `notebooks/4_Topic_Model_Summarization.ipynb` | Notebook iPython | Utilisé pour faire la synthèse du contenu de la collection de documents sur la base d’un modèle de thèmes LDA formé |
| `notebooks/5_Topic_Model_Analysis.ipynb` | Notebook iPython | Utilisé pour analyser le contenu thématique d’une collection de documents texte et mettre en corrélation les informations thématiques avec d’autres métadonnées associées à cette collection |
| `notebooks/6_Interactive_Visualization.ipynb` | Notebook iPython | Visualisation interactive du modèle de rubriques apprises |
| `notebooks/winprocess.py` | Fichier Python | Script Python utilisé par les notebooks pour le traitement multiprocesseur |
| `README.md` | Fichier Markdown | Fichier Markdown LISEZ-MOI |

### <a name="data-ingestion-and-transformation"></a>Ingestion et transformation des données

Le jeu de données compilé `CongressionalDataAll_Jun_2017.tsv` est enregistré dans le Stockage Blob, et est accessible à partir des notebooks et des scripts Python. L’ingestion et la transformation des données se font en deux étapes : le prétraitement des données texte et l’apprentissage des expressions.

#### <a name="preprocess-text-data"></a>Prétraitement des données texte

L’étape de prétraitement applique des techniques de traitement en langage naturel pour nettoyer et préparer les données de texte brut. Elle précède l’apprentissage non supervisé des expressions et la modélisation des thèmes latents. La description pas à pas est détaillée dans le notebook `1_Preprocess_Text.ipynb`. Ce notebook est également associé à un script Python `step1.py`.

Dans cette étape, le fichier de données TSV est téléchargé du Stockage Blob Azure et importé sous la forme d’un DataFrame Pandas. Chaque élément de ligne dans le DataFrame correspond à une seule longue chaîne de texte cohésive, appelée « document ». Chaque document est ensuite découpé en blocs de texte s’apparentant à des phrases, des expressions ou des éléments d’expression. Le découpage est conçu pour empêcher le processus d’apprentissage des expressions d’utiliser des chaînes de mots réparties entre plusieurs phrases ou expressions.

De nombreuses fonctions pour l’étape de prétraitement sont définies à la fois dans le notebook et le package Python. La majeure partie du travail peut être effectuée en appelant ces deux lignes de code.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Apprentissage des expressions

L’étape d’apprentissage des expressions implémente une infrastructure de base pour apprendre les principales expressions utilisées dans une grande collection de documents. Cette étape est décrite dans le document « [Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf) », qui a été initialement présenté lors de la conférence de l’IEEE « Workshop on Spoken Language Technology » en 2012. L’implémentation de l’étape d’apprentissage des expressions est détaillée dans le notebook iPython `2_Phrase_Learning.ipynb` et le script Python `step2.py`.

Cette étape utilise le texte nettoyé comme entrée et apprend les expressions les plus présentes dans une grande collection de documents. L’apprentissage des expressions est un processus itératif qui peut être divisé en trois tâches : compter les n-grammes, classer les expressions potentielles en appliquant la pondération PMI (Pointwise Mutual Information) sur les mots qui les constituent et réécrire les expressions en texte. Ces trois tâches sont exécutées séquentiellement en plusieurs itérations jusqu’à ce que les expressions spécifiées aient été apprises.

Dans le package Python d’analyse de documents, une classe Python `PhraseLearner` est définie dans le fichier `phraseLearning.py`. L’extrait de code ci-dessous permet d’apprendre des expressions.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> L’étape d’apprentissage des expressions est implémentée avec le traitement multiprocesseur. Toutefois, le fait d’avoir davantage de cœurs de processeur ne signifie **PAS** forcément que l’exécution sera plus rapide. Nos tests ont montré que les performances ne s’améliorent pas avec l’utilisation de plus de huit cœurs en raison de la surcharge du traitement multiprocesseur. L’apprentissage de 25 000 expressions sur une machine à huit cœurs (3,6 GHz) a pris environ deux heures et demie.
>

### <a name="topic-modeling"></a>Modélisation de rubrique

L’apprentissage d’un modèle de thèmes latents LDA est la troisième étape de ce scénario. Le package Python [gensim](https://radimrehurek.com/gensim/) est nécessaire dans cette étape d’apprentissage d’un [modèle de thèmes LDA](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Cette étape est décrite dans le notebook `3_Topic_Model_Training.ipynb`. Vous pouvez également consulter `step3.py` pour savoir comment utiliser le package d’analyse de document.

Dans cette étape, la tâche principale consiste à apprendre un modèle de thèmes LDA et à optimiser les hyperparamètres. Parmi les paramètres devant être optimisés pour la formation d’un modèle LDA, le nombre de thèmes est le paramètre le plus important. Un nombre insuffisant de thèmes risque de ne pas donner un insight suffisant de la collection de documents. Au contraire, un nombre excessif de thèmes entraîne la « sursegmentation » d’un corpus en de nombreux petits thèmes très similaires. L’objectif de ce scénario est de montrer comment déterminer le nombre optimal de thèmes. Avec Azure Machine Learning Workbench, vous pouvez facilement effectuer plusieurs expérimentations avec des configurations de paramètres différentes dans des contextes de calcul différents.

Dans le package Python d’analyse de document, certaines fonctions ont été définies pour aider les utilisateurs à déterminer le nombre optimal de thèmes. La première approche consiste à évaluer la cohérence du modèle de thèmes. Il existe quatre métriques d’évaluation prises en charge : `u_mass`, `c_v`, `c_uci` et `c_npmi`. Ces métriques sont toutes décrites dans [ce document](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). La deuxième approche consiste à évaluer la perplexité d’un corpus récupéré.

Pour l’évaluation de la perplexité, une courbe en U est attendue pour déterminer le nombre optimal de thèmes. La position au niveau du coude est le meilleur choix. Nous vous recommandons d’effectuer plusieurs évaluations avec des valeurs de départ aléatoires différentes pour obtenir une moyenne. L’évaluation de la cohérence est censée être une courbe en « n », où la cohérence augmente au départ en même temps que le nombre de thèmes, puis diminue. Voici un exemple de tracé de la perplexité et de la cohérence `c_v`.

![Perplexité](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![Cohérence c_v](./media/scenario-document-collection-analysis/c_v_Coherence.png)

Dans ce scénario, la perplexité augmente considérablement après 200 thèmes, alors que la cohérence diminue fortement après 200 thèmes également. En tenant compte de ces graphes, et du souhait d’avoir des thèmes généraux plutôt que des thèmes très segmentés, le choix de 200 thèmes semble une bonne option.

Vous pouvez former un seul modèle de thèmes LDA en une seule expérimentation, ou former et évaluer plusieurs modèles LDA configurés avec un nombre de thèmes différent en une seule expérimentation. Nous vous recommandons de faire plusieurs expérimentations avec la même configuration, pour obtenir une moyenne des évaluations de la perplexité et/ou de la cohérence. L’utilisation du package d’analyse de document est décrite en détail dans le fichier `step3.py`. Voici un exemple d’extrait de code.

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> La durée d’exécution de la formation d’un modèle de thèmes LDA dépend de plusieurs facteurs, parmi lesquels la taille du corpus, la configuration des hyperparamètres et le nombre de cœurs sur la machine. L’apprentissage d’un modèle est plus rapide avec plusieurs cœurs de processeur. Toutefois, avec la même configuration d’hyperparamètres, un plus grand nombre de cœurs réduit le nombre de mises à jour pendant l’apprentissage. Nous vous recommandons d’avoir **au moins 100 mises à jour pour former un modèle LDA convergé**. La relation entre le nombre de mises à jour et les hyperparamètres est décrite dans [ce post](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) et [ce post](http://miningthedetails.com/blog/python/lda/GensimLDA/). Dans nos tests, il a fallu environ trois heures pour former un modèle LDA contenant 200 thèmes avec la configuration `workers=15`, `passes=10`, `chunksize=1000` sur une machine à 16 cœurs (2,0 GHz).
>

### <a name="topic-summarization-and-analysis"></a>Analyse et synthèse des thèmes

L’étape d’analyse et de synthèse des thèmes est décrite dans deux notebooks. Il n’y a pas de fonctions correspondantes dans le package d’analyse de document.

Le notebook `4_Topic_Model_Summarization.ipynb` montre comment faire la synthèse du contenu du document sur la base d’un modèle de thèmes LDA formé. La synthèse est appliquée à un modèle de thèmes LDA formé à l’étape 3. Le notebook montre comment mesurer l’importance ou la qualité d’un thème à l’aide de la mesure de pureté entre le thème et le document. Cette mesure de pureté suppose que les thèmes latents qui sont les plus présents dans les documents sont sémantiquement plus importants que ceux qui sont utilisés peu de fois dans de nombreux documents. Ce concept a été introduit dans le document « [Latent Topic Modeling for Audio Corpus Summarization](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf) ».

Le notebook `5_Topic_Model_Analysis.ipynb` montre comment analyser le contenu thématique d’une collection de documents et comment mettre en corrélation les informations thématiques avec d’autres métadonnées associées à cette collection. Ce notebook contient quelques tracés pour aider les utilisateurs à mieux comprendre le concept d’apprentissage et de collection de documents.

Le notebook `6_Interactive_Visualization.ipynb` montre comment visualiser de façon interactive le modèle de rubriques apprises. Il comprend quatre tâches de visualisation interactive.

## <a name="conclusion"></a>Conclusion

Ce scénario réel met en évidence l’utilisation de techniques d’analyse de texte connues (dans cet exemple, l’apprentissage des expressions et la modélisation des thèmes LDA) pour produire un modèle fiable. Il montre également de quelle façon Azure Machine Learning Workbench facilite le suivi des performances du modèle et contribue à optimiser progressivement l’apprentissage. Plus précisément :

* L’apprentissage des expressions et la modélisation des thèmes permettent de traiter une collection de documents et de créer un modèle fiable. Si la collection de documents est très volumineuse, Azure Machine Learning Workbench la met à l’échelle facilement. En outre, Azure Machine Learning Workbench donne aux utilisateurs la possibilité d’effectuer facilement des expérimentations dans différents contextes de calcul.

* Azure Machine Learning Workbench offre deux options pour exécuter les notebooks pas à pas et un script Python pour effectuer une expérimentation complète.

* L’optimisation des hyperparamètres avec Azure Machine Learning Workbench permet de déterminer le nombre optimal de thèmes à utiliser pour l’apprentissage. Azure Machine Learning Workbench facilite le suivi des performances du modèle et contribue à optimiser progressivement l’apprentissage.

* Azure Machine Learning Workbench peut gérer l’historique des exécutions et les modèles formés. Il permet aux chercheurs de données d’identifier rapidement les modèles les plus performants, et d’obtenir les scripts et les données nécessaires pour créer ces modèles.

## <a name="references"></a>Références

* **Timothy J. Hazen, Fred Richardson**, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Spoken Language Technology Workshop (SLT), 2012 IEEE. IEEE, 2012.

* **Timothy J. Hazen**, [_Latent Topic Modeling for Audio Corpus Summarization_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). 12e conférence annuelle de l’International Speech Communication Association. 2011.

* **Michael Roder, Andreas Both, Alexander Hinneburg**, [_Exploring the Space of Topic Coherence Measures_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). Rapport de la 8e conférence internationale ACM sur l’exploration de données et la recherche web. ACM, 2015.
