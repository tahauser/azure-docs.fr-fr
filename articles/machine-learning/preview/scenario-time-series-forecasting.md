---
title: "Prévision de séries chronologiques sur la demande d’énergie | Microsoft Docs"
description: "Comment appliquer Machine Learning pour la prévision de séries chronologiques sur la demande d’énergie dans Azure Machine Learning Workbench."
services: machine-learning
documentationcenter: 
author: anta
manager: ireiter
editor: anta
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: anta
ms.openlocfilehash: 17903df93e11b8d1a5b9c6fbe5fd8e53302f45f4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="energy-demand-time-series-forecasting"></a>Prévision de séries chronologiques sur la demande d’énergie


La prévision de séries chronologiques consiste à prédire des valeurs futures dans une séquence chronologique d’observations. Il s’agit d’un problème courant avec des applications dans de nombreux secteurs d’activité. Par exemple, les entreprises de vente au détail ont besoin de prévoir les ventes de produits futures afin d’organiser efficacement leurs chaînes d’approvisionnement pour répondre à la demande. De même, les entreprises de livraison de colis doivent estimer la demande afin de planifier à l’avance les besoins en personnel et les itinéraires de livraison. Dans de nombreux cas, les risques financiers liés à des prévisions incorrectes peuvent être significatifs. Par conséquent, la prévision constitue souvent une activité critique pour l’entreprise.

Cet exemple montre comment la prévision de séries chronologiques peut être effectuée via l’application de techniques Machine Learning. Vous serez guidé tout au long du processus de modélisation, notamment :
- Préparation des données pour nettoyer et formater les données ;
- Création de fonctionnalités pour les modèles Machine Learning à partir de données de séries chronologiques brutes ;
- Apprentissage de différents modèles Machine Learning ;
- Évaluation des modèles en comparant leurs performances sur un jeu de données de test récupéré ; et,
- Mise en place du meilleur modèle, en le rendant disponible via un service web pour générer des prévisions à la demande.

Azure Machine Learning Workbench aide le processus de modélisation à chaque étape : 
- L’exemple montre comment l’environnement de bloc-notes Jupyter (disponible directement via Workbench) peut faciliter le développement de code Python. Le processus de développement du modèle peut être expliqué plus clairement à d’autres utilisateurs à l’aide d’annotations et de graphiques Markdown. Ces blocs-notes peuvent être affichés, modifiés et exécutés directement à partir de Workbench.
- Les modèles formés peuvent être conservés et chargés vers le Stockage Blob. Ceci aide les experts en mégadonnées à effectuer le suivi des objets de modèle formé et à s’assurer qu’ils sont conservés et récupérables en cas de besoin.
- Les métriques peuvent être journalisées lors de l’exécution d’un script Python, ce qui permet à l’expert en mégadonnées de conserver un enregistrement des scores de performances du modèle.
- Workbench produit des tables personnalisables des métriques journalisées, permettant ainsi à l’expert en mégadonnées de comparer aisément les métriques de performances du modèle. Les graphiques sont automatiquement affichés. Ainsi, le modèle le plus performant peut être facilement identifié.
- Enfin, l’exemple montre comment un modèle formé peut être mis en place en le déployant dans un service web en temps réel.

## <a name="link-to-the-gallery-github-repository"></a>Lien vers le référentiel GitHub de la galerie
Le référentiel GitHub public correspondant à ce scénario réel contient tous les supports, y compris les exemples de code, nécessaires pour cet exemple :

[https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting](https://github.com/Azure/MachineLearningSamples-EnergyDemandTimeSeriesForecasting)


## <a name="use-case-overview"></a>Vue d’ensemble d’un cas d’usage

Ce scénario se concentre sur la prévision de la demande d’énergie où l’objectif consiste à prédire la charge future d’un réseau électrique. Il s’agit d’une opération métier critique pour les entreprises du secteur énergétique car les opérateurs doivent maintenir l’équilibre entre l’énergie consommée sur un réseau et l’énergie fournie à celui-ci. Une puissance trop importante fournie au réseau peut entraîner un gaspillage de l’énergie ou des défaillances techniques. Toutefois, si la puissance fournie n’est pas suffisante, cela peut entraîner des pannes et laisser les clients sans électricité. En règle générale, les opérateurs de réseau peuvent prendre des décisions à court terme pour gérer l’alimentation en énergie du réseau et maintenir l’équilibre de la charge. Une prévision précise à court terme de la demande d’énergie est par conséquent essentielle pour que l’opérateur puisse prendre ces décisions en toute confiance.

Ce scénario décrit en détail la construction d’une solution de prévision de la demande d’énergie Machine Learning. La solution est formée sur un jeu de données public issu de [New York Independent System Operator (NYISO)](http://www3.dps.ny.gov/W/PSCWeb.nsf/All/298372E2CE4764E885257687006F39DF?OpenDocument), qui exploite le réseau électrique de l’état de New York. Le jeu de données comprend des données horaires de la demande d’énergie pour New York City sur une période de 5 ans. Un jeu de données supplémentaire contenant des conditions météorologiques horaires pour New York City au cours de la même période a été extrait de [darksky.net](https://darksky.net).

## <a name="prerequisites"></a>Prérequis

- Un [compte Azure](https://azure.microsoft.com/free/) (des comptes d’essai gratuit sont disponibles).
- Une copie [d’Azure Machine Learning Workbench](./overview-what-is-azure-ml.md) installée selon les instructions du [guide d’installation et de démarrage rapide](./quickstart-installation.md) pour installer le programme et créer un espace de travail.
- Cet exemple part du principe que vous exécutez Azure ML Workbench sur Windows 10 avec le [moteur Docker](https://www.docker.com/) installé localement. Si vous utilisez macOS, la procédure à suivre est en grande partie la même.
- Azure Machine Learning Operationalization installé avec un environnement de déploiement local configuré et un compte de gestion de modèle créé comme décrit dans ce [guide](./model-management-configuration.md).
- Cet exemple requiert la mise à jour de l’installation Pandas vers la version 0.20.3 ou une version ultérieure, et l’installation de matplotlib. Cliquez sur *Ouvrir l’invite de commandes* à partir du menu *Fichier* dans Workbench et exécutez les commandes suivantes pour installer ces dépendances :

    ```
    conda install "pandas>=0.21.1"
    ```
    
## <a name="create-a-new-workbench-project"></a>Créer un projet Workbench

Créez un projet en utilisant cet exemple comme modèle :
1.  Ouvrez Azure Machine Learning Workbench
2.  Dans la page **Projets**, cliquez sur le signe **+**, puis sélectionnez **Nouveau projet**
3.  Dans le volet **Créer un projet**, entrez les informations relatives à votre nouveau projet
4.  Dans la zone de recherche **Rechercher des modèles de projet**, tapez « Prévision de séries chronologiques sur la demande d’énergie » et sélectionnez le modèle
5.  Cliquez sur **Créer**


## <a name="data-description"></a>Description des données

Deux jeux de données sont fournis avec cet exemple et sont téléchargés à l’aide du bloc-notes `1-data-preparation.ipynb` : `nyc_demand.csv` et `nyc_weather.csv`.

**nyc_demand.csv** contient des valeurs horaires de la demande d’énergie pour New York City pour les années 2012 à 2017. Les données ont la structure simple suivante :

| timeStamp | demand |
| --- | --- |
| 2012-01-01 00:00:00 | 4937.5 |
| 2012-01-01 01:00:00 | 4752.1 |
| 2012-01-01 02:00:00 | 4542.6 |
| 2012-01-01 03:00:00 | 4357.7 |

Les valeurs de la demande sont en Mégawatt-heures (MWh). Vous trouverez ci-dessous un graphique de la demande d’énergie sur une période de 7 jours en juillet 2017 :

![Demande d’énergie](./media/scenario-time-series-forecasting/energy_demand.png  "Demande d’énergie")

**nyc_weather.csv** contient des valeurs horaires météorologiques pour New York City pour les années 2012 à 2017 :

| timeStamp | precip | temp
| --- | --- | --- |
| 2012-01-01 00:00:00 | 0.0 | 46.13 |
| 2012-01-01 01:00:00 | 0.01 | 45.89 |
| 2012-01-01 02:00:00 | 0.05 | 45.04 |
| 2012-01-01 03:00:00 | 0.02 | 45.03 |

*precip* est une mesure en pourcentage du niveau de précipitations. Les valeurs *temp* (température) ont été mises à l’échelle de façon à ce que toutes les valeurs se situent dans l’intervalle [0, 100].

## <a name="scenario-structure"></a>Structure du scénario

Lorsque vous ouvrez ce projet pour la première fois dans Azure Machine Learning Workbench, accédez au volet *Fichiers* sur le côté gauche. Les fichiers de code suivants sont fournis avec cet exemple :
- `1-data-preparation.ipynb` : ce bloc-notes Jupyter télécharge et traite les données afin de les préparer pour la modélisation. Il s’agit du premier bloc-notes que vous allez exécuter.
- `2-linear-regression.ipynb` : ce bloc-notes effectue l’apprentissage d’un modèle de régression linéaire sur les données d’apprentissage.
- `3-ridge.ipynb` : effectue l’apprentissage d’un modèle de régression ridge.
- `4-ridge-poly2.ipynb` : effectue l’apprentissage d’un modèle de régression ridge sur les fonctionnalités polynomiales du second degré.
- `5-mlp.ipynb` : effectue l’apprentissage d’un réseau neuronal de type perceptron multicouche.
- `6-dtree.ipynb` : effectue l’apprentissage d’un modèle d’arbre de décision.
- `7-gbm.ipynb` : effectue l’apprentissage d’un modèle de machine gradient boosted.
- `8-evaluate-model.py` : ce script charge un modèle formé et l’utilise pour effectuer des prédictions sur un jeu de données de test récupéré. Il génère des métriques d’évaluation de modèle afin de pouvoir comparer les performances de différents modèles.
- `9-forecast-output-exploration.ipynb` : ce bloc-notes génère des visualisations des prévisions générées par les modèles Machine Learning.
- `10-deploy-model.ipynb` : ce bloc-notes montre comment un modèle de prévision formé peut être mis en place dans un service web en temps réel.
- `evaluate-all-models.py` : ce script évalue tous les modèles formés. Il fournit une alternative à l’exécution de `8-evaluate-model.py` pour chaque modèle formé individuellement.

### <a name="1-data-preparation-and-cleaning"></a>1. Préparation et nettoyage des données

Pour démarrer l’exécution de l’exemple, cliquez tout d’abord sur `1-data-preparation.ipynb` pour ouvrir le bloc-notes en mode d’aperçu. Cliquez sur ***Start Notebook Server*** (Démarrer le serveur de bloc-notes) pour lancer un serveur de bloc-notes Jupyter et le noyau Python sur votre ordinateur. Vous pouvez exécuter les blocs-notes à partir de Workbench ou vous pouvez utiliser votre navigateur en accédant à [http://localhost:8888](http://localhost:8888). Notez que vous devez modifier le noyau sur *PROJECT_NAME local*. Pour ce faire, accédez au menu *Kernel* (Noyau) dans le bloc-notes sous *Change kernel* (Modifier le noyau). Appuyez sur ***Maj + Entrée*** pour exécuter le code dans des cellules individuelles du bloc-notes, ou cliquez sur *Run All* (Tout exécuter) dans le menu *Cell* (Cellule) pour exécuter la totalité du bloc-notes.

Le bloc-notes télécharge tout d’abord les données à partir d’un conteneur de stockage Blob hébergé sur Azure. Les données sont alors stockées sur votre ordinateur dans `AZUREML_NATIVE_SHARE_DIRECTORY`. Cet emplacement est accessible à partir de n’importe quel bloc-notes ou script que vous exécutez à partir de Workbench. C’est donc un bon emplacement pour stocker des données et des modèles formés.

Une fois que les données ont été téléchargées, le bloc-notes nettoie les données en remplissant les espaces laissés vides dans la série chronologique et en remplissant les valeurs manquantes à l’aide de l’interpolation. Le fait de nettoyer les données de la série chronologique de cette façon optimise la quantité de données disponibles pour l’apprentissage des modèles.

Plusieurs fonctionnalités de modèle sont créées à partir des données brutes nettoyées. Ces fonctionnalités peuvent être classées en deux groupes :

- **Fonctions pilotées par heure** : ces fonctionnalités sont dérivées de la variable *timestamp*, par exemple *month*, *dayofweek* et *hour*.
- **Fonctions décalées** : ce sont les valeurs horaires corrigées de la demande ou de la température réelle. L’objectif de ces fonctionnalités est de capturer les dépendances conditionnelles entre des périodes chronologiques consécutives dans le modèle.

Le jeu de données résultant peut ensuite servir lors du développement de modèles de prévision.

### <a name="2-model-development"></a>2. Développement d’un modèle

Un modèle de régression linéaire simple peut constituer une première approche de la modélisation. Le bloc-notes `2-linear-regression.ipynb` montre comment effectuer l’apprentissage d’un modèle de prévision de régression linéaire pour la demande d’énergie future. Plus spécifiquement, le modèle sera formé pour prédire la demande d’énergie une heure (*t+1*) avant l’heure actuelle (*t*). Toutefois, nous pouvons appliquer ce modèle « en une étape » de manière récursive au moment du test afin de générer des prévisions pour des périodes horaires futures, *t+n*.

Pour former un modèle, un pipeline prédictif avec trois étapes distinctes est créé :

- **Une transformation des données** : les formats requis pour les données d’entrée peuvent varier en fonction de l’algorithme Machine Learning. Dans ce cas, le modèle de régression linéaire requiert des variables catégorielles encodées à chaud.
- **Une routine de validation croisée** : souvent, un modèle Machine Learning a un ou plusieurs hyperparamètres qui doivent être ajustés par le biais de l’expérimentation. La validation croisée peut être utilisée pour trouver le jeu optimal des valeurs des paramètres. Le modèle est formé et évalué de manière répétée sur des plis différents du jeu de données. Les meilleurs paramètres sont ceux qui obtiennent les meilleures performances de modèle lorsque leur moyenne est appliquée sur les plis de la validation croisée. Ce processus est expliqué en détail dans `2-linear-regression.ipynb`.
- **Apprentissage du modèle final** : le modèle est formé sur l’ensemble du jeu de données, à l’aide du meilleur jeu d’hyperparamètres.

Nous avons inclus une série de 6 modèles différents dans les blocs-notes numérotés de 2 à 7. Chaque bloc-notes effectue l’apprentissage d’un modèle différent et le stocke à l’emplacement `AZUREML_NATIVE_SHARE_DIRECTORY`. Une fois que vous avez formé tous les modèles développés pour ce scénario, nous pouvons les évaluer et les comparer comme décrit dans la section suivante.

### <a name="3-model-evaluation-and-comparison"></a>3. Évaluation et comparaison de modèle

Nous pouvons utiliser un modèle formé pour effectuer des prévisions pour les périodes horaires futures. Il est préférable d’évaluer ces modèles sur un jeu de données de test récupéré. En évaluant les différents modèles sur le même jeu de données non consulté, nous pouvons comparer leurs performances et identifier le meilleur modèle. Dans ce scénario, nous appliquons le modèle formé de manière récursive pour effectuer une prévision sur plusieurs tranches horaires futures. Plus spécifiquement, nous générons des prévisions pour une plage horaire ayant jusqu’à six heures *t+6* d’avance sur l’heure actuelle, *t*. Ces prédictions sont évaluées par rapport à la demande réelle observée pour chaque période de temps.

Pour évaluer un modèle, ouvrez le script `8-evaluate-model.py` à partir du volet *Fichiers* dans Workbench. Vérifiez que l’option *Run Configuration* (Exécuter la configuration) est définie sur la valeur **local**, puis tapez le nom du modèle dans le champ *Arguments*. Le nom du modèle doit correspondre exactement à la variable *model_name* définie au début du bloc-notes dans lequel l’apprentissage du modèle est effectué. Par exemple, entrez « linear_regression » pour évaluer le modèle de régression linéaire formé. Une fois que tous les modèles ont été formés, vous pouvez également tous les évaluer avec une seule commande en exécutant `evaluate-all-models.py`. Pour exécuter ce script, ouvrez une invite de commandes à partir de Workbench et exécutez la commande suivante :

```
python evaluate-all-models.py
```
Le script `8-evaluate-model.py` effectue les opérations suivantes :

- Charge un pipeline de modèle formé à partir du disque
- Effectue des prédictions sur le jeu de données de test
- Calcule les métriques de performance du modèle et les journalise
- Enregistre les prédictions du jeu de données de test dans `AZUREML_NATIVE_SHARE_DIRECTORY` à des fins d’inspection et d’analyse ultérieures
- Enregistre le pipeline de modèle formé dans le dossier *outputs*.

> [!Note]
> L’enregistrement du modèle dans le dossier *outputs* copie automatiquement l’objet de modèle dans le compte Stockage Blob Azure associé à votre compte Expérimentation. Cela signifie que vous serez toujours en mesure de récupérer l’objet de modèle enregistré à partir de l’objet blob, même si l’ordinateur ou le contexte de calcul change.

Accédez au volet *Historique d’exécution* et cliquez sur `8-evaluate-model.py`. Vous verrez des graphiques affichant plusieurs métriques de performance de modèle :

- **ME** : erreur moyenne de la prévision. Ceci peut être interprété comme l’écart moyen de la prévision.
- **MPE** : [erreur de pourcentage moyenne](https://en.wikipedia.org/wiki/Mean_percentage_error) (valeur ME exprimée sous forme de pourcentage de la demande réelle)
- **MSE** : [erreur quadratique moyenne](https://en.wikipedia.org/wiki/Mean_squared_error)
- **RMSE** : racine carrée de l’erreur quadratique (racine carrée de MSE)
- **MAPE** : [erreur de pourcentage absolue moyenne](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
- **sMAPE** : [erreur de pourcentage absolue moyenne symétrique](https://en.wikipedia.org/wiki/Symmetric_mean_absolute_percentage_error)
- **MAPE_base** : MAPE d’une prévision de référence dans laquelle la prédiction est égale à la dernière valeur connue de la demande.
- **MdRAE** : erreur relative absolue moyenne. Le ratio médian de l’erreur de prévision par rapport à l’erreur de prévision de référence. Une valeur inférieure à 1 signifie que la prévision est plus performante que la référence.

Toutes les métriques ci-dessus font référence à la prévision *t+1*. Outre les métriques ci-dessus, vous verrez également un ensemble de métriques correspondant avec le suffixe *_horizon*. Il s’agit de la métrique moyenne calculée sur toutes les périodes dans la plage de la prévision, de la période *t+1* à la période *t+6*.

Si les métriques ne s’affichent pas dans la zone de graphique, cliquez sur le symbole d’engrenage dans le coin supérieur droit. Assurez-vous que les métriques de performance de modèle qui vous intéressent sont sélectionnées. Les métriques s’affichent également dans une table sous les graphiques. Là encore, cliquez sur le symbole d’engrenage pour personnaliser cette table. Triez la table sur une métrique de performance pour identifier le meilleur modèle. Si vous voulez voir comment les performances de la prévision varient entre la période *t+1* et la période *t+6*, cliquez sur l’entrée du modèle dans la table. Les graphiques qui affichent les métriques MAPE, MPE et MdRAE sur la période de la prévision sont affichés sous *Visualisations*.

Lors de l’évaluation de modèles de prévision, il peut être très utile de visualiser les prédictions de sortie. Ceci aide l’expert en mégadonnées à déterminer si la prévision produite apparaît réaliste. Cela peut également aider à identifier des problèmes dans la prévision si, par exemple, elle fonctionne mal à certaines périodes de temps. Le bloc-notes `9-forecast-output-exploration.ipynb` produit des visualisations des prévisions générées pour le jeu de données de test.

### <a name="4-deployment"></a>4. Déploiement

Le meilleur modèle peut être mis en place en le déployant en tant que service web en temps réel. Ce service web peut ensuite être appelé pour générer des prévisions à la demande à mesure que de nouvelles données sont disponibles. Dans ce scénario, une nouvelle prévision doit être générée toutes les heures pour prédire la demande d’énergie dans l’heure suivante. Pour effectuer cette tâche, il est possible de déployer un service web qui prend un ensemble de fonctionnalités pour une période horaire spécifique en tant qu’entrée et retourne la demande prédite en tant que sortie.

Dans cet exemple, un service web est déployé sur un ordinateur Windows 10. Assurez-vous d’avoir effectué les étapes requises pour le déploiement local indiquées dans ce [guide de mise en route](https://github.com/Azure/Machine-Learning-Operationalization/blob/master/documentation/getting-started.md) pour l’interface Operationalization CLI. Une fois que vous avez configuré votre environnement local et le compte de gestion du modèle, exécutez le bloc-notes `10-deploy-model.ipynb` pour déployer le service web.

## <a name="conclusion"></a>Conclusion

Cet exemple montre comment générer une solution de prévision de séries chronologiques de bout en bout à des fins de prévision de la demande d’énergie. La plupart des principes explorés dans cet exemple peuvent être étendus à d’autres scénarios de prévision et d’autres secteurs d’activité.

Ce scénario montre comment Azure Machine Learning Workbench peut aider un expert en mégadonnées à développer des solutions réelles avec des fonctionnalités utiles comme l’environnement de bloc-notes Jupyter et les fonctionnalités de journalisation de métriques. L’exemple présente également au lecteur comment un modèle peut être mis en place et déployé à l’aide d’Azure Machine Learning Operationalization CLI. Une fois déployé, l’API de service web permet aux développeurs ou aux ingénieurs de données d’intégrer le modèle de prévision dans un pipeline de données plus large.
