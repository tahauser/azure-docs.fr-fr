---
title: Comment choisir les algorithmes d’apprentissage automatique | Microsoft Docs
description: Comment choisir les algorithmes Azure Machine Learning pour l'apprentissage supervisé et non supervisé dans les expériences de clustering, classification ou régression.
services: machine-learning
documentationcenter: ''
author: pakalra
ms.author: pakalra
manager: cgronlun
editor: cgronlun
tags: ''
ms.assetid: a3b23d7f-f083-49c4-b6b1-3911cd69f1b4
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 12/18/2017
ms.openlocfilehash: e1c7030a5f0c6e13653b302fcb48e7d4efa232c7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="how-to-choose-algorithms-for-microsoft-azure-machine-learning"></a>Comment choisir les algorithmes dans Microsoft Azure Machine Learning
La réponse à la question « Quel algorithme d’apprentissage automatique dois-je utiliser ? » est toujours « Cela dépend. ». Cela dépend de la taille, de la qualité et de la nature des données. Cela dépend de ce que vous voulez faire avec la réponse. Cela dépend de la conversion des calculs de l'algorithme en instructions pour l'ordinateur que vous utilisez. Et cela dépend du temps que vous avez. Même les scientifiques de données les plus expérimentés ne peuvent pas savoir quel algorithme fonctionnera le mieux avant d’en essayer.

## <a name="the-machine-learning-algorithm-cheat-sheet"></a>Aide-mémoire d’algorithme Machine Learning
L' **aide-mémoire d'algorithme Microsoft Azure Machine Learning** vous aide à choisir l'algorithme d'apprentissage automatique adapté à vos solutions d'analyse prédictive dans la bibliothèque d'algorithmes Microsoft Azure Machine Learning.
Cet article vous guide tout au long de son utilisation.

> [!NOTE]
> Pour télécharger l’aide-mémoire et suivre cet article, accédez à [Aide-mémoire d'algorithme Machine Learning pour Microsoft Azure Machine Learning Studio](algorithm-cheat-sheet.md).
> 
> 

Cet aide-mémoire est pensé pour un public très spécifique : un scientifique des données débutant, avec une formation de premier cycle en apprentissage automatique, qui tente de choisir un algorithme pour prendre en main Azure Machine Learning Studio. Cela signifie qu’il comprend certaines généralisations et simplifie trop, mais vous guide en toute sécurité. Cela signifie également qu'il existe un grand nombre d'algorithmes non répertoriés ici. Nous ajouterons des méthodes au fur et à mesure de leur entrée dans Azure Machine Learning.

Ces recommandations sont des commentaires et des conseils compilés d’un grand nombre de scientifiques de données et d’experts en apprentissage automatique. Nous ne sommes pas d'accord avec tous les éléments, mais avons essayé d'harmoniser les avis en un consensus. Notre désaccord est généralement introduit par « Cela dépend... »

### <a name="how-to-use-the-cheat-sheet"></a>Utilisation de l’aide-mémoire
Lisez les chemins et les étiquettes d’algorithme sur l’aide-mémoire comme « Pour *&lt;étiquette de chemin&gt;* utilisez *&lt;algorithm&gt;* ». Par exemple, « Pour *Vitesse*, utilisez la *régression logistique à deux classes* ». Parfois, plusieurs branches sont utiles.
Parfois, aucune n’est parfaite. Ce sont des recommandations générales, donc ne vous inquiétez pas de leur exactitude.
Plusieurs scientifiques des données auxquels j’ai parlé m’ont affirmé que la seule façon de trouver le meilleur algorithme est de tous les essayer.

Voici un exemple d’une expérimentation de la [galerie Azure AI](http://gallery.cortanaintelligence.com/) qui essaie plusieurs algorithmes sur les mêmes données et compare les résultats : [Compare Multi-class Classifiers: Letter recognition](http://gallery.cortanaintelligence.com/Details/a635502fc98b402a890efe21cec65b92).

> [!TIP]
> Pour télécharger et imprimer un diagramme offrant une vue d’ensemble des fonctionnalités de Machine Learning Studio, consultez [Diagramme de vue d’ensemble des fonctionnalités d’Azure Machine Learning Studio](studio-overview-diagram.md).
> 
> 

## <a name="flavors-of-machine-learning"></a>Types d'apprentissage automatique
### <a name="supervised"></a>Supervisé
Les algorithmes d'apprentissage supervisés font des prédictions basées sur un ensemble d'exemples. Par exemple, des historiques des cours peuvent permettre d'estimer les prix futurs. Chaque exemple utilisé pour l'apprentissage est étiqueté avec la valeur d'intérêt : dans ce cas, le prix des actions. Un algorithme d'apprentissage supervisé recherche des modèles dans ces étiquettes de valeur. Il peut utiliser toutes les informations qui peuvent être pertinentes (le jour de la semaine, la saison, les données financières de l’entreprise, le type de secteur, l’existence d’événements géopolitiques perturbateurs), et chaque algorithme recherche différents types de modèles. Une fois que l'algorithme a trouvé le meilleur modèle possible, il l’utilise pour élaborer des prédictions pour les données de test sans étiquette : les prix futurs.

Il s’agit d’un type d’apprentissage automatique utile et apprécié. À une exception près, tous les modules d’Azure Machine Learning sont supervisés. Plusieurs types spécifiques d'apprentissage supervisé sont représentés dans Azure Machine Learning : la classification, la régression et la détection d’anomalies.

* **Classification**. Lorsque les données sont utilisées pour prédire une catégorie, l’apprentissage supervisé est également appelé classification. C'est le cas lors de l'affectation d'une image en tant que photo d'un « chat » ou d’un « chien ». Quand il n’y a que deux choix, on appelle cela la **classification à deux classes** ou **binomiale**. Lorsqu'il existe plusieurs catégories, comme lors de la prévision du gagnant du championnat NCAA, ce problème est connu sous le nom de **classification à classes multiples**.
* **Régression**. Lorsque l’on prédit une valeur, comme le cours de la Bourse, l’apprentissage surveillé est appelé régression.
* **Détection des anomalies**. Parfois, l'objectif est d'identifier les points de données qui sont simplement inhabituels. Dans le cas de la détection des fraudes par exemple, toute dépense très étrange par carte de crédit est suspecte. Les variations possibles sont si nombreuses et les exemples de formation si rares, qu'il n'est pas possible de savoir à quoi ressemble une activité frauduleuse. L'approche de la détection des anomalies consiste simplement à apprendre à quoi ressemble l’activité normale (à l'aide d'un historique de transactions non-frauduleuses) et d’identifier tout ce qui est très différent.

### <a name="unsupervised"></a>Non supervisé
Dans l’apprentissage non supervisé, les points de données n’ont aucune étiquette associée. En effet, l’objectif d’un algorithme d’apprentissage non supervisé est d’organiser les données d’une certaine façon ou de décrire sa structure. Cela peut signifier un regroupement en clusters ou la recherche de différentes manières de visualisation des données complexes afin d’en simplifier l’affichage ou de l’organiser plus efficacement.

### <a name="reinforcement-learning"></a>Apprentissage par renforcement
Dans l’apprentissage par renforcement, l’algorithme choisit une action en réponse à chaque point de données. L’algorithme d’apprentissage reçoit également un signal de récompense quelques instants plus tard, qui indique la qualité de la décision.
En fonction de ce signal, l’algorithme modifie sa stratégie pour atteindre la récompense la plus élevée. Il n’existe actuellement aucun module d’apprentissage de renforcement dans Azure Machine Learning. L’apprentissage par renforcement est une approche courante en robotique, où le jeu des lectures des capteurs à un moment donné est un point de données et où l’algorithme doit choisir l’action suivante du robot. Il est également adapté aux applications d’Internet des objets.

## <a name="considerations-when-choosing-an-algorithm"></a>Considérations lors du choix d'un algorithme
### <a name="accuracy"></a>Précision
L’obtention de la réponse la plus précise possible n'est pas toujours nécessaire.
Parfois, en fonction de votre utilisation, une approximation suffit. Si tel est le cas, vous pourrez peut-être réduire le temps de traitement considérablement en utilisant des méthodes plus approximatives. Un autre avantage des méthodes plus approximatives est qu’elles ont naturellement tendance à éviter [le surajustement](https://youtu.be/DQWI1kvmwRg).

### <a name="training-time"></a>Durée d’apprentissage
Le nombre de minutes ou d'heures nécessaires pour l'apprentissage d'un modèle varie beaucoup selon les algorithmes. La durée d’apprentissage est souvent étroitement liée à la précision : l’une accompagne généralement l'autre. En outre, certains algorithmes sont plus sensibles au nombre de points de données que d'autres.
Un temps limité peut guider le choix de l'algorithme, en particulier lorsque le jeu de données est vaste.

### <a name="linearity"></a>Linéarité
Un grand nombre d'algorithmes d'apprentissage automatique utilisent la linéarité. Les algorithmes de classification linéaire supposent que les classes peuvent être séparées par une ligne droite (ou son analogie de dimension supérieure). Ceux-ci incluent la régression logistique et les machines à vecteurs de support (comme implémentées dans Azure Machine Learning).
Les algorithmes de régression linéaire supposent que les tendances des données suivent une ligne droite. Ces hypothèses ne sont pas erronées pour certains problèmes, mais réduisent la précision pour d’autres.

![Frontière de classe non linéaire][1]

***Limite de classe non linéaire*** *: utiliser un algorithme de classification linéaire entraînerait une faible précision*

![Données avec une tendance non linéaire][2]

***Données avec une tendance non linéaire****: une méthode de régression linéaire entraînerait plus d’erreurs*

Malgré leurs limitations, les algorithmes linéaires sont très populaires comme première ligne d'attaque. Ils ont tendance à être des algorithmes simples et à apprentissage rapide.

### <a name="number-of-parameters"></a>Nombre de paramètres
Les paramètres sont les boutons que les scientifiques des données règlent lorsqu’ils configurent un algorithme. Ce sont des nombres qui affectent le comportement de l'algorithme, comme la tolérance aux erreurs ou le nombre d'itérations ou les variantes du comportement de l'algorithme. La durée d’apprentissage et la précision de l'algorithme peuvent parfois dépendre grandement du choix de paramètres appropriés. En règle générale, les algorithmes avec des paramètres à grands nombres nécessitent plus d’essais pour trouver la bonne combinaison.

Il existe également un bloc module de [balayage de paramètre](algorithm-parameters-optimize.md) dans Azure Machine Learning qui essaie automatiquement toutes les combinaisons de paramètres à la granularité que vous choisissez. Même si c'est un excellent moyen de vous assurer que vous avez examiné l'espace de paramétrage, le temps nécessaire pour former un modèle augmente de façon exponentielle avec le nombre de paramètres.

Avoir de nombreux paramètres indique généralement qu'un algorithme a une plus grande flexibilité. Il peut souvent obtenir une excellente précision. Si vous trouvez la bonne combinaison de paramètres.

### <a name="number-of-features"></a>Nombre de fonctionnalités
Pour certains types de données, le nombre de fonctionnalités peut être très important par rapport au nombre de points de données. C'est souvent le cas avec les données génétiques ou textuelles. Le grand nombre de fonctionnalités risque de ralentir certains algorithmes d'apprentissage et atteindre une durée d’apprentissage inutilisable. Les machines à vecteurs de support sont particulièrement bien adaptées à ce cas (voir ci-dessous).

### <a name="special-cases"></a>Cas particuliers
Certains algorithmes d'apprentissage effectuent des hypothèses particulières sur la structure des données ou les résultats souhaités. Si vous pouvez en trouver un qui répond à vos besoins, il peut vous donner des résultats plus pertinents, des prévisions plus précises ou des durées d'apprentissage plus courtes.

| **Algorithme** | **Précision** | **Durée d’apprentissage** | **Linéarité** | **Paramètres** | **Remarques** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Classification double classe.** | | | | | |
| [régression logique](https://msdn.microsoft.com/library/azure/dn905994.aspx) | |● |● |5. | |
| [forêt de décision](https://msdn.microsoft.com/library/azure/dn906008.aspx) |● |○ | |6. | |
| [jungle de décision](https://msdn.microsoft.com/library/azure/dn905976.aspx) |● |○ | |6. |Faible encombrement de mémoire |
| [arbre de décision optimisé](https://msdn.microsoft.com/library/azure/dn906025.aspx) |● |○ | |6. |Encombrement de mémoire important |
| [réseau neuronal](https://msdn.microsoft.com/library/azure/dn905947.aspx) |● | | |9. |[Personnalisation supplémentaire possible](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [perceptron moyenné](https://msdn.microsoft.com/library/azure/dn906036.aspx) |○ |○ |● |4 | |
| [machines à vecteurs de support](https://msdn.microsoft.com/library/azure/dn905835.aspx) | |○ |● |5. |Idéal pour les ensembles de fonctionnalités de grande taille |
| [Machine à vecteurs de support localement profonde](https://msdn.microsoft.com/library/azure/dn913070.aspx) |○ | | |8 |Idéal pour les ensembles de fonctionnalités de grande taille |
| [Machine de point de Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx) | |○ |● |3 | |
| **Classification multiclasse.** | | | | | |
| [régression logique](https://msdn.microsoft.com/library/azure/dn905853.aspx) | |● |● |5. | |
| [forêt de décision](https://msdn.microsoft.com/library/azure/dn906015.aspx) |● |○ | |6. | |
| [jungle de décision ](https://msdn.microsoft.com/library/azure/dn905963.aspx) |● |○ | |6. |Faible encombrement de mémoire |
| [réseau neuronal](https://msdn.microsoft.com/library/azure/dn906030.aspx) |● | | |9. |[Personnalisation supplémentaire possible](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [un contre tous](https://msdn.microsoft.com/library/azure/dn905887.aspx) |- |- |- |- |Consultez les propriétés de la méthode à deux classes sélectionnée |
| **Régression** | | | | | |
| [linéaire](https://msdn.microsoft.com/library/azure/dn905978.aspx) | |● |● |4 | |
| [linéaire bayésienne](https://msdn.microsoft.com/library/azure/dn906022.aspx) | |○ |● |2 | |
| [forêt de décision](https://msdn.microsoft.com/library/azure/dn905862.aspx) |● |○ | |6. | |
| [arbre de décision optimisé](https://msdn.microsoft.com/library/azure/dn905801.aspx) |● |○ | |5. |Encombrement de mémoire important |
| [quantile de forêt rapide](https://msdn.microsoft.com/library/azure/dn913093.aspx) |● |○ | |9. |Distributions plutôt que prédictions de points |
| [réseau neuronal](https://msdn.microsoft.com/library/azure/dn905924.aspx) |● | | |9. |[Personnalisation supplémentaire possible](http://go.microsoft.com/fwlink/?LinkId=402867) |
| [Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx) | | |● |5. |Techniquement journal linéaire. Pour les décomptes prévisionnels |
| [ordinal](https://msdn.microsoft.com/library/azure/dn906029.aspx) | | | |0 |Pour la prédiction de rang |
| **Détection des anomalies** | | | | | |
| [machines à vecteurs de support](https://msdn.microsoft.com/library/azure/dn913103.aspx) |○ |○ | |2 |Particulièrement adapté aux ensembles de caractéristiques de grande taille |
| [Détection des anomalies reposant sur le PCA](https://msdn.microsoft.com/library/azure/dn913102.aspx) | |○ |● |3 | |
| [K-moyennes](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/) | |○ |● |4 |Un algorithme de clustering |

**Propriétés de l'algorithme :**

**●** : excellente précision, durée d'apprentissage courte et utilisation de la linéarité

**○** : bonne précision et durée d'apprentissage modérée

## <a name="algorithm-notes"></a>Notes de l'algorithme
### <a name="linear-regression"></a>Régression linéaire
Comme mentionné précédemment, la [régression linéaire](https://msdn.microsoft.com/library/azure/dn905978.aspx) fait correspondre une ligne (ou plan ou hyperplan) au jeu de données. Elle est très efficace, simple et rapide, mais peut être trop simpliste pour certains problèmes.
Obtenez un [didacticiel relatif à la régression linéaire](linear-regression-in-azure.md)ici.

![Données avec une tendance linéaire][3]

***Données avec une tendance linéaire***

### <a name="logistic-regression"></a>Régression logique
En dépit du terme « régression », la régression logique est en fait un puissant outil pour la classification à [deux classes](https://msdn.microsoft.com/library/azure/dn905994.aspx) et à [classes multiples](https://msdn.microsoft.com/library/azure/dn905853.aspx). Elle est rapide et simple. Le fait qu'elle utilise une courbe en forme de S, et non une ligne droite, en fait une solution de choix pour diviser les données en groupes. La régression logique crée des limites de classes linéaires : vérifiez donc qu'une approximation linéaire vous convient.

![Régression logique pour les données à deux classes avec une seule caractéristique][4]

***Une régression logique pour les données à deux classes avec une seule caractéristique*** *: la limite de classe est le point auquel la courbe logistique est aussi proche des deux classes*

### <a name="trees-forests-and-jungles"></a>Arbres, forêts et jungles
Les forêts de décision ([régression](https://msdn.microsoft.com/library/azure/dn905862.aspx), [deux classes](https://msdn.microsoft.com/library/azure/dn906008.aspx) et [classes multiples](https://msdn.microsoft.com/library/azure/dn906015.aspx)), les jungles de décision ([deux classes](https://msdn.microsoft.com/library/azure/dn905976.aspx) et [classes multiples](https://msdn.microsoft.com/library/azure/dn905963.aspx)) et les arbres de décision renforcés ([régression](https://msdn.microsoft.com/library/azure/dn905801.aspx) et [deux classes](https://msdn.microsoft.com/library/azure/dn906025.aspx)) sont tous basés sur les arbres de décision, un concept fondamental pour l’apprentissage automatique. Il existe de nombreuses variantes d'arbres de décision, mais tous font la même chose : subdiviser l'espace de la fonctionnalité en régions avec la même étiquette. Il peut s'agir des régions de catégorie ou de valeur constante, si vous effectuez une classification ou une régression.

![Arbre de décision subdivisant un espace de caractéristiques][5]

***Un arbre de décision divise un espace de caractéristique en régions de valeurs à peu près uniformes***

Étant donné qu’un espace de fonctionnalité peut être subdivisé en petites régions de taille arbitraire, il est facile de concevoir que la division atteigne un point de données par région. Il s’agit d’un exemple extrême de dépassement. Pour éviter ce problème, un grand ensemble d'arbres est construit avec un soin mathématique pour que les arbres ne soient pas corrélés. La moyenne de cette « forêt de décision » est un arbre qui permet d'éviter un surajustement. Les forêts de décision peuvent utiliser beaucoup de mémoire. Les jungles de décision sont une variante qui consomme moins de mémoire, mais avec une durée d’apprentissage légèrement plus longue.

Les arbres de décision améliorée évitent le surajustement en limitant le nombre de subdivisions et le nombre minimum de points de données autorisés dans chaque région. L'algorithme construit une séquence d'arbres, chacun d'entre eux apprenant à compenser l'erreur de l'arbre précédent. Cela fournit un apprenant très précis qui a tendance à utiliser beaucoup de mémoire. Pour une description complète, consultez le [livre d'origine de Friedman](http://www-stat.stanford.edu/~jhf/ftp/trebst.pdf).

[La régression quantile de forêt rapide](https://msdn.microsoft.com/library/azure/dn913093.aspx) est une variante d'arbres de décision pour les cas où vous souhaitez savoir non seulement la valeur standard (médiane) des données au sein d'une région, mais également sa distribution sous la forme de quantiles.

### <a name="neural-networks-and-perceptrons"></a>Perceptrons et réseaux neuronaux
Les réseaux neuronaux sont des algorithmes d’apprentissage inspirés du cerveau couvrant les problèmes [de classes multiples](https://msdn.microsoft.com/library/azure/dn906030.aspx), [à deux classes](https://msdn.microsoft.com/library/azure/dn905947.aspx) et de [régression](https://msdn.microsoft.com/library/azure/dn905924.aspx). Il existe de nombreux réseaux neuronaux d’apprentissage, mais ceux d’Azure Machine Learning sont tous des graphiques acycliques dirigés. Cela signifie que les fonctionnalités d'entrée sont transmises vers l'avant (jamais vers l'arrière) via une séquence de couches, avant d'être transformées en sorties. Dans chaque couche, les entrées sont pondérées dans diverses combinaisons, additionnées et transmises à la couche suivante. Cette combinaison de calculs simples permet d'apprendre les tendances de données et les limites de classe sophistiquées, en toute simplicité. Les réseaux à plusieurs couches de ce type effectuent « l’apprentissage approfondi » qui alimente tant d’articles technologiques et de science-fiction.

Ces performances élevées ont toutefois un prix. L’apprentissage des réseaux neuronaux peut prendre beaucoup de temps, en particulier pour les grands jeux de données avec un grand nombre de fonctionnalités. Ils ont également plus de paramètres que la plupart des algorithmes, ce qui signifie que le balayage de paramètres allonge grandement la durée d’apprentissage.
Et pour les perfectionnistes qui souhaitent [spécifier leur propre structure de réseau](http://go.microsoft.com/fwlink/?LinkId=402867), les possibilités sont infinies.

![Limites apprises par les réseaux neuronaux][6]
*** Les limites apprises par les réseaux neuronaux peuvent être complexes et irrégulières***

Le [perceptron moyenné à deux classes](https://msdn.microsoft.com/library/azure/dn906036.aspx) est le réseau neuronal idéal pour répondre à l’explosion des durées d'apprentissage. Il utilise une structure de réseau qui fournit des limites de la classe linéaire. Il est presque primitif au vu des normes actuelles, mais il a un long historique d'utilisation fiable et est suffisamment petit pour apprendre rapidement.

### <a name="svms"></a>Machines à vecteurs de support (SVM)
Les machines à vecteurs de support (SVM) recherchent la limite qui sépare les classes par une marge aussi grande que possible. Lorsque les deux classes ne peuvent pas être clairement séparées, les algorithmes trouvent la meilleure limite possible. Comme présenté dans Azure Machine Learning, la [SVM à deux classes](https://msdn.microsoft.com/library/azure/dn905835.aspx) le fait uniquement avec une ligne droite. (Dans le jargon SVM, elle utilise un noyau linéaire). Étant donné qu’elle utilise cette approximation linéaire, elle est capable de s'exécuter assez rapidement. Elle est particulièrement utile pour les données avec de nombreuses fonctionnalités comme les données textuelles ou de génome. Dans ces cas, les SVM sont en mesure de séparer les classes plus rapidement et avec moins de surajustement que la plupart des autres algorithmes, tout en utilisant une petite quantité de mémoire.

![Frontière de classe de machine à vecteurs de support][7]

***Une frontière de classe de machine à vecteurs de support standard optimise la marge séparant deux classes***

Un autre produit de Microsoft Research, la [SVM localement approfondi à deux classes](https://msdn.microsoft.com/library/azure/dn913070.aspx) est une variante non linéaire de SVM qui conserve la plupart de l'efficacité de la mémoire et de la vitesse de la version linéaire. Elle est idéale pour les cas où l'approche linéaire ne donne pas de réponses suffisamment précises. Les développeurs ont conservé sa rapidité en décomposant le problème en une série de petits problèmes SVM linéaires. Lisez la [description complète](http://proceedings.mlr.press/v28/jose13.html) pour plus d'informations sur la méthode utilisée.

À l'aide d'une extension intelligente de SVM non linéaires, la [SVM à une classe](https://msdn.microsoft.com/library/azure/dn913103.aspx) dessine une limite qui encadre étroitement l'ensemble de données. Elle est utile pour la détection des anomalies. Les nouveaux points de données qui se situent bien à l'extérieur de cette limite sont assez inhabituels pour être remarqués.

### <a name="bayesian-methods"></a>Méthodes bayésiennes
Les méthodes bayésiennes ont une qualité très intéressante : elles évitent le surajustement. Pour cela, elles émettent quelques hypothèses préalables sur la distribution probable de la réponse. Un autre avantage de cette approche est qu'elle a très peu de paramètres. Azure Machine Learning a des algorithmes bayésiens pour la classification ([machine de points à deux classes de Bayes](https://msdn.microsoft.com/library/azure/dn905930.aspx)) et la régression ([régression linéaire bayésienne](https://msdn.microsoft.com/library/azure/dn906022.aspx)).
Notez qu’ils supposent que les données peuvent être fractionnées ou ajustées avec une ligne droite.

Anecdote historique, les machines de point de Bayes ont été développées par Microsoft Research. Elles reposent sur un travail théorique exceptionnel. Pour plus d’informations, consultez [l’article d’origine dans JMLR](http://jmlr.org/papers/volume1/herbrich01a/herbrich01a.pdf) et le [blog de Chris Bishop](http://blogs.technet.com/b/machinelearning/archive/2014/10/30/embracing-uncertainty-probabilistic-inference.aspx).

### <a name="specialized-algorithms"></a>Algorithmes spécialisés
Avoir un objectif très spécifique peut être bénéfique. La collection Azure Machine Learning comprend des algorithmes spécialisés dans les domaines suivants :

- Prédiction de classement ([régression ordinale](https://msdn.microsoft.com/library/azure/dn906029.aspx))
- Prédiction de nombres ([régression Poisson](https://msdn.microsoft.com/library/azure/dn905988.aspx))
- Détection des anomalies (un reposant sur [l’analyse des principaux composants](https://msdn.microsoft.com/library/azure/dn913102.aspx) et un autre sur les [machines à vecteurs de support](https://msdn.microsoft.com/library/azure/dn913103.aspx))
- Clustering ([K-moyennes](https://msdn.microsoft.com/library/azure/5049a09b-bd90-4c4e-9b46-7c87e3a36810/))

![Détection des anomalies reposant sur le PCA][8]

***Détection des anomalies reposant sur le PCA****: la grande majorité des données correspond à une distribution typique ; les points déviant considérablement de cette distribution sont suspects*

![Jeu de données regroupé à l'aide de K-moyennes][9]

***Un jeu de données est regroupé dans cinq clusters à l’aide de K-moyennes.***

Il existe également un ensemble [de classifieur à classes multiples one-v-all](https://msdn.microsoft.com/library/azure/dn905887.aspx), qui subdivise le problème de classification de classe N en problèmes de classification à deux classes N-1. La précision, la durée d’apprentissage et les propriétés de linéarité sont déterminées par les classifieurs à deux classes utilisés.

![Deux classifieurs à deux classes combinés pour former un classifieur à trois classes][10]

***Deux classifieurs à deux classes combinés pour former un classifieur à trois classes***

Azure Machine Learning inclut également l'accès à une puissante infrastructure d'apprentissage automatique appelée [Vowpal Wabbit](https://msdn.microsoft.com/library/azure/8383eb49-c0a3-45db-95c8-eb56a1fef5bf).
VW défie la catégorisation ici, puisqu'elle peut apprendre des problèmes de classification et de régression et même utiliser des données partiellement sans étiquette. Vous pouvez la configurer pour utiliser l'un des nombreux algorithmes d’apprentissage, les fonctions de perte et les algorithmes d'optimisation. Elle a été conçue dès le départ pour être efficace, parallèle et extrêmement rapide. Elle gère d’immenses ensembles de fonctionnalités avec peu d'effort.
Lancée et dirigée par John Langford de Microsoft Research, VW est une Formule 1 dans la course des algorithmes. VW ne convient pas à tous les problèmes, mais si c’est le cas pour le vôtre, il est avantageux de l’utiliser. Elle est également disponible en tant que [code open source autonome](https://github.com/JohnLangford/vowpal_wabbit) dans plusieurs langues.

## <a name="more-help-with-algorithms"></a>Aide supplémentaire sur les algorithmes
* Pour obtenir une infographie téléchargeable décrivant les algorithmes et fournissant des exemples, consultez [Infographie téléchargeable : Principes de base de l’apprentissage automatique avec exemples d’algorithmes](basics-infographic-with-algorithm-examples.md).
* Pour obtenir une liste par catégorie de tous les algorithmes d’apprentissage automatique disponibles dans Azure Machine Learning Studio, consultez [Initialiser le modèle][initialize-model] dans Machine Learning Studio : aide sur les algorithmes et les modules.
* Pour obtenir la liste alphabétique complète des algorithmes et des modules de Azure Machine Learning Studio, consultez [Liste alphabétique des modules de Machine Learning Studio][a-z-list] dans Machine Learning Studio : aide sur les algorithmes et les modules.
* Pour télécharger et imprimer un diagramme offrant une vue d’ensemble des fonctionnalités d’Azure Machine Learning Studio, consultez [Diagramme de vue d’ensemble des fonctionnalités d’Azure Machine Learning Studio](studio-overview-diagram.md).


<!-- Reference links -->
[initialize-model]: https://msdn.microsoft.com/library/azure/dn905812.aspx
[a-z-list]: https://msdn.microsoft.com/library/azure/dn906033.aspx

<!-- Media -->

[1]: ./media/algorithm-choice/image1.png
[2]: ./media/algorithm-choice/image2.png
[3]: ./media/algorithm-choice/image3.png
[4]: ./media/algorithm-choice/image4.png
[5]: ./media/algorithm-choice/image5.png
[6]: ./media/algorithm-choice/image6.png
[7]: ./media/algorithm-choice/image7.png
[8]: ./media/algorithm-choice/image8.png
[9]: ./media/algorithm-choice/image9.png
[10]: ./media/algorithm-choice/image10.png
