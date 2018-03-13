---
title: "Mise en correspondance des questions et réponses à l’aide d’Azure Machine Learning Workbench | Microsoft Docs"
description: "Décrit comment utiliser différentes méthodes d’apprentissage automatique performantes pour mettre en correspondance les questions ouvertes avec des paires Questions et réponses FAQ préexistantes."
services: machine-learning
documentationcenter: 
author: mezmicrosoft
editor: mezmicrosoft
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: mez
manager: tihazen
ms.openlocfilehash: 33f807a4a0bbc4afd1f2fbe017f8913eccacc34b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
#  <a name="q--a-matching-using-azure-machine-learning-workbench"></a>Mise en correspondance des questions et réponses à l’aide d’Azure Machine Learning Workbench
Répondre à des questions ouvertes est difficile et oblige souvent les experts techniques (SME) à effectuer des opérations manuelles. Pour alléger le travail des SME internes, les entreprises créent couramment des listes de questions fréquentes (FAQ) destinées à aider les utilisateurs. Cet exemple présente différentes méthodes d’apprentissage automatique performantes pour mettre en correspondance les questions ouvertes avec des paires Questions et réponses FAQ préexistantes. Il montre un processus de développement simple pour créer une solution d’apprentissage automatique à l’aide d’Azure Machine Learning Workbench. 

## <a name="link-to-the-gallery-github-repository"></a>Lien vers le dépôt GitHub de la galerie
[https://github.com/Azure/MachineLearningSamples-QnAMatching](https://github.com/Azure/MachineLearningSamples-QnAMatching)

## <a name="overview"></a>Vue d’ensemble

Cet exemple expose le problème de la mise en correspondance des questions des utilisateurs avec des paires Questions et réponses préexistantes, qui sont généralement fournies sous forme de liste de questions fréquentes (FAQ) ou répertoriées sur certains sites web tels que [Stack Overflow](https://stackoverflow.com/). Il existe diverses approches pour établir une correspondance entre une question et une réponse appropriée. L’une d’elles consiste à rechercher la réponse qui est la plus similaire à la question. Toutefois, dans cet exemple, les questions ouvertes sont mises en correspondance avec des questions déjà posées en partant du principe que chaque réponse donnée dans le FAQ peut correspondre à plusieurs questions sémantiquement équivalentes.

Les principales étapes à prévoir pour cette solution sont les suivantes :

1. Nettoyer et traiter les données texte.
2. Apprendre les expressions informatives. Ce sont des séquences multimots qui fournissent davantage d’informations quand elles sont affichées en séquence que traitées indépendamment les unes des autres.
3. Extraire les caractéristiques à partir des données texte.
4. Former des modèles de classification du texte et évaluer les performances du modèle.


## <a name="prerequisites"></a>configuration requise

Cet exemple nécessite les prérequis suivants :

1. Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles).
2. Une copie [d’Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installée selon les instructions du [guide d’installation et de démarrage rapide](./quickstart-installation.md) pour installer le programme et créer un espace de travail.
3. Vous pouvez exécuter cet exemple dans n’importe quel contexte de calcul. Toutefois, nous vous recommandons d’utiliser une machine multicœur ayant au minimum 16 Go de mémoire et 5 Go d’espace disque.

## <a name="create-a-new-workbench-project"></a>Créer un projet workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Azure Machine Learning Workbench
2.  Dans la page **Projets**, cliquez sur le signe **+**, puis sélectionnez **Nouveau projet**
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet
4.  Dans la zone de recherche **Rechercher dans les modèles de projet**, tapez « correspondance Questions et réponses » et sélectionnez le modèle
5.  Cliquez sur **Créer**

## <a name="data-description"></a>Description des données

Le jeu de données utilisé dans cet exemple est une image des données Stack Exchange stockées sur [archive.org](https://archive.org/details/stackexchange). Ces données sont une image rendue anonyme de tout le contenu publié par les utilisateurs sur le [réseau Stack Exchange](https://stackexchange.com/). Chaque site dans le réseau est formaté en tant qu’archive distincte composée de fichiers XML ayant été compressés en utilisant la compression bzip2 avec 7-zip. Chaque archive de site inclut les posts, les utilisateurs, les votes, les commentaires, l’historique des posts et les liens des posts (respectivement, Posts, Users, Votes, Comments, PostHistory et PostLinks dans le schéma). 

### <a name="data-source"></a>Source de données

Cet exemple utilise les [données Posts (10 Go)](https://archive.org/download/stackexchange/stackoverflow.com-Posts.7z) et les [données PostLinks (515 Mo)](https://archive.org/download/stackexchange/stackoverflow.com-PostLinks.7z) issues du site Stack Overflow. Pour obtenir des informations sur le schéma complet, consultez [readme.txt](https://ia800500.us.archive.org/22/items/stackexchange/readme.txt). 

Le champ `PostTypeId` dans les données Posts indique si un post est une question (`Question`) ou une réponse (`Answer`). Le champ `PostLinkTypeId` dans les données PostLinks indique si les deux posts sont liés ou dupliqués. Les posts de questions incluent généralement des balises, qui sont des mots clés servant à classer les questions avec d’autres questions similaires ou dupliquées. Certaines balises fréquemment utilisées, comme `javascript`, `java`, `c#`, `php`, etc., constituent un plus grand nombre de posts de questions. Cet exemple extrait une partie des paires Questions et réponses avec la balise `javascript`.

De plus, un post de question peut être lié à plusieurs posts de réponse ou à des posts de question dupliqués. La création d’une liste FAQ à partir de ces deux jeux de données prend en compte certains critères de collecte des données. Les trois jeux de données compilées sont sélectionnés à l’aide d’un script SQL (non fourni dans cet exemple). La description des données est donc la suivante :

- `Original Questions (Q)` : les posts de questions sont envoyés et répondus sur le site Stack Overflow.
- `Duplications (D)` : les posts de questions dupliquent d’autres questions préexistantes (`PostLinkTypeId = 3`), qui correspondent aux questions initiales. Les duplications sont considérées comme sémantiquement équivalentes aux questions initiales en ce sens que la réponse fournie à la question initiale répond également à la nouvelle question dupliquée.
- `Answers (A)` : chaque question initiale et ses duplications peuvent être liées à plusieurs posts de réponses. Cet exemple sélectionne uniquement le post de réponse qui est accepté par l’auteur d’origine (filtré par `AcceptedAnswerId`) ou qui a le score le plus élevé (classé par `Score`) dans les questions initiales. 

La combinaison de ces trois jeux de données crée des paires Questions et réponses où une réponse (A) est mise en correspondance avec une seule question initiale (Q) et plusieurs questions dupliquées (D), comme illustré dans le schéma de données ci-dessous :

<table><tr><td><img id ="data_diagram" src="media/scenario-qna-matching/data_diagram.png"></td></tr></table>

### <a name="data-structure"></a>Structure des données

Le schéma de données et les liens de téléchargement directs des trois jeux de données sont fournis dans le tableau suivant :

| Jeu de données | Champ | type | Description
| ----------|------------|------------|--------
| [questions](https://bostondata.blob.core.windows.net/stackoverflow/orig-q.tsv.gz) | ID | Chaîne | ID unique de la question (clé primaire)
|  | AnswerId | Chaîne | ID unique de la réponse à chaque question
|  | Text0 | Chaîne | Données de texte brut, comprenant le titre et le corps de la question
|  | CreationDate | Timestamp | Horodatage de la question posée
| [dupes](https://bostondata.blob.core.windows.net/stackoverflow/dup-q.tsv.gz) | ID | Chaîne | ID unique de la duplication (clé primaire)
|  | AnswerId | Chaîne | ID de la réponse associée à la duplication
|  | Text0 | Chaîne | Données de texte brut, comprenant le titre et le corps de la duplication
|  | CreationDate | Timestamp | Horodatage de la question dupliquée posée
| [answers](https://bostondata.blob.core.windows.net/stackoverflow/ans.tsv.gz)  | ID | Chaîne | ID unique de la réponse (clé primaire)
|  | text0 | Chaîne | Données de texte brut de la réponse


## <a name="scenario-structure"></a>Structure du scénario

L’exemple de mise en correspondance de questions et réponses est présenté à travers trois types de fichiers. Le premier type est une série de notebooks Jupyter qui montrent les descriptions pas à pas de l’intégralité du workflow. Le deuxième type est un ensemble de fichiers Python contenant des modules Python personnalisés pour l’apprentissage des expressions et l’extraction des caractéristiques. Ces modules Python sont génériques et peuvent donc servir à d’autres cas d’usage que cet exemple. Le troisième type est un ensemble de fichiers Python utilisés pour optimiser les hyperparamètres et suivre les performances du modèle à l’aide d’Azure Machine Learning Workbench.

Les fichiers utilisés dans cet exemple sont structurés comme suit.

| Nom de fichier | type | Description
| ----------|------------|--------
| `Image` | Dossier | Dossier utilisé pour enregistrer les images du fichier LISEZMOI
| `notebooks` | Dossier | Dossier des notebooks Jupyter
| `modules` | Dossier | Dossier des modules Python
| `scripts` | Dossier | Dossier des fichiers Python
| `notebooks/Part_1_Data_Preparation.ipynb` | Jupyter Notebook | Utilisé pour accéder à l’exemple de données, prétraiter le texte, préparer l’apprentissage et tester les jeux de données
| `notebooks/Part_2_Phrase_Learning.ipynb` | Jupyter Notebook | Utilisé pour apprendre les expressions informatives et découper (tokenize) le texte en représentations de sacs de mots
| `notebooks/Part_3_Model_Training_and_Evaluation.ipynb` | Jupyter Notebook | Utilisé pour extraire les caractéristiques, former des modèles de classification du texte et évaluer les performances du modèle
| `modules/__init__.py` | Fichier Python | Fichier init du package Python
| `modules/phrase_learning.py` | Fichier Python | Modules Python utilisés pour transformer les données brutes et apprendre les expressions informatives
| `modules/feature_extractor.py` | Fichier Python | Modules Python utilisés pour extraire les caractéristiques pour l’apprentissage du modèle
| `scripts/naive_bayes.py` | Fichier Python | Fichier Python utilisé pour optimiser les hyperparamètres dans le modèle Naive Bayes
| `scripts/random_forest.py` | Fichier Python | Fichier Python utilisé pour optimiser les hyperparamètres dans le modèle Random Forest
| `README.md` | Fichier Markdown | Fichier Markdown LISEZMOI

> [!NOTE]
> La série de notebooks est générée avec Python 3.5.
> 

### <a name="data-ingestion-and-transformation"></a>Ingestion et transformation des données

Les trois jeux de données compilées sont stockés dans un Stockage Blob et sont récupérés dans le notebook `Part_1_Data_Preparation.ipynb`.  

Avant d’effectuer l’apprentissage des modèles de classification du texte, le texte des questions est nettoyé et prétraité pour exclure les extraits de code. Un apprentissage non supervisé des expressions est effectué sur les supports d’apprentissage pour apprendre les séquences informatives multimots. Ces expressions sont représentées sous forme d’unités de mots composés uniques suite à la caractérisation de sacs de mots utilisée par les modèles de classification du texte.

Les descriptions pas à pas du prétraitement du texte et de l’apprentissage des expressions se trouvent dans les notebooks `Part_1_Data_Preparation.ipynb` et `Part_2_Phrase_Learning.ipynb`, respectivement.

### <a name="modeling"></a>Modélisation

Cet exemple permet de calculer le score des nouvelles questions par rapport aux paires Questions et réponses préexistantes en formant des modèles de classification du texte où chaque paire Questions et réponses préexistante est une classe unique et où une partie des questions dupliquées pour chaque paire Questions et réponses est fournie comme support d’apprentissage. Dans l’exemple, les questions initiales et 75 % des questions dupliquées sont conservées pour l’apprentissage, tandis que 25 % des questions dupliquées postées dernièrement sont récupérées comme données d’évaluation.

Le modèle de classification utilise une méthode d’ensemble pour agréger les trois classifieurs de base, **Naive Bayes**, **Support Vector Machine** et **Random Forest**. Dans chaque classifieur de base, `AnswerId` est utilisé comme étiquette de classe et les représentations de sacs de mots sont utilisées comme caractéristiques.

Le processus d’apprentissage du modèle est illustré dans `Part_3_Model_Training_and_Evaluation.ipynb`.

### <a name="evaluation"></a>Évaluation

Les performances sont évaluées à l’aide de deux métriques différentes. 
1. `Average Rank (AR)` : indique la position moyenne de la réponse appropriée dans la liste des paires Questions et réponses récupérées (sur la totalité des 103 classes de réponses). 
2. `Top 3 Percentage` : indique le pourcentage des questions de test parmi lesquelles la réponse appropriée peut être récupérée dans les trois premiers choix de la liste classée retournée. 

L’évaluation est illustrée dans `Part_3_Model_Training_and_Evaluation.ipynb`.


## <a name="conclusion"></a>Conclusion

Cet exemple présente l’utilisation de techniques d’analytique de texte connues, comme l’apprentissage des expressions et la classification du texte, pour produire un modèle fiable. Il montre également de quelle façon Azure Machine Learning Workbench facilite le développement de solutions interactives et le suivi des performances du modèle. 

Voici quelques points à retenir de cet exemple :

- Les modèles d’apprentissage des expressions et de classification du texte permettent de résoudre le problème de la mise en correspondance des questions et réponses.
- Azure Machine Learning Workbench et Jupyter Notebook permettent de développer des modèles interactifs.
- Azure Machine Learning Workbench gère l’historique des exécutions et les modèles appris avec la journalisation des métriques d’évaluation à des fins de comparaison. Ces fonctionnalités permettent d’optimiser rapidement les hyperparamètres et d’identifier plus facilement les modèles les plus performants.


## <a name="references"></a>Références

Timothy J. Hazen, Fred Richardson, [_Modeling Multiword Phrases with Constrained Phrases Tree for Improved Topic Modeling of Conversational Speech_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Spoken Language Technology Workshop (SLT), 2012 IEEE. IEEE, 2012.

Timothy J. Hazen, [_MCE Training Techniques for Topic Identification of Spoken Audio Documents_](http://ieeexplore.ieee.org/abstract/document/5742980/) dans l’IEEE Transactions on Audio, Speech, et Language Processing, vol. 19, no. 8, pp. 2451-2460, nov. 2011.
