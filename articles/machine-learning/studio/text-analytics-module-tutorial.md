---
title: Créer des modèles d’analyse de texte dans Azure Machine Learning Studio | Microsoft Docs
description: Création de modèles d’analyse de texte dans Azure Machine Learning Studio à l’aide de modules pour le pré-traitement de texte, les N-grammes ou le hachage de fonction
services: machine-learning
documentationcenter: ''
author: rastala
ms.author: roastala
manager: cgronlun
editor: ''
ms.assetid: 08cd6723-3ae6-4e99-a924-e650942e461b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.openlocfilehash: 6a89d77c84f7a7dd15b9d73252ac7373a7f1faf9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="create-text-analytics-models-in-azure-machine-learning-studio"></a>Créer des modèles d’analyse de texte dans Azure Machine Learning Studio
Vous pouvez utiliser Azure Machine Learning pour créer des modèles d’analyse de texte et les rendre opérationnels. Ces modèles peuvent, par exemple, vous aider à résoudre des problèmes d’analyse de sentiments ou de classification de document.

Dans une expérience d’analyse de texte, vous devez généralement :

1. nettoyer et pré-traiter un jeu de données de texte ;
2. extraire des vecteurs de fonctions numériques du texte pré-traité ;
3. former le modèle de classification ou de régression ;
4. noter et valider le modèle ;
5. déployer le modèle en production.

Dans ce didacticiel, vous allez vous familiariser avec ces étapes au cours de l’exploration d’un modèle d’analyse de sentiments à l’aide d’un jeu de données Amazon Book Reviews (consultez l’article scientifique « Biographies, Bollywood, Boom-boxes and Blenders: Domain Adaptation for Sentiment Classification » de John Blitzer, Mark Dredze et Fernando Pereira ; Association of Computational Linguistics (ACL), 2007). Ce jeu de données se compose de notes de critiques de livres (1-2 ou 4-5) et d’un texte de forme libre. L’objectif est de prédire la note de la critique : faible (1-2) ou élevée (4-5).

Vous trouverez les expériences traitées dans ce didacticiel dans la galerie Azure AI :

[Predict Book Reviews](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-1)

[Predict Book Reviews - Predictive Experiment](https://gallery.cortanaintelligence.com/Experiment/Predict-Book-Reviews-Predictive-Experiment-1)

## <a name="step-1-clean-and-preprocess-text-dataset"></a>Étape 1 : Nettoyer et pré-traiter un jeu de données de texte
Nous commençons l’expérience en divisant les notes de critiques de livres en compartiments faible et élevé pour formuler le problème sous forme de classification à deux classes. Nous utilisons les modules [Modifier les métadonnées](https://msdn.microsoft.com/library/azure/dn905986.aspx) et [Regrouper les valeurs catégoriques](https://msdn.microsoft.com/library/azure/dn906014.aspx).

![Créer une étiquette](./media/text-analytics-module-tutorial/create-label.png)

Ensuite, nous nettoyons le texte à l’aide du module [Pré-traiter le texte](https://msdn.microsoft.com/library/azure/mt762915.aspx) . Le nettoyage permet de réduire le bruit dans le jeu de données, vous aide à trouver les fonctionnalités les plus importantes et à améliorer la précision du modèle final. Nous supprimons les mots vides (mots courants tels que « le » ou « un ») et les chiffres, caractères spéciaux, caractères en double, adresses de messagerie et URL. Nous convertissons également le texte en minuscules, effectuons la lemmatisation des mots et détectons les limites des phrases qui sont ensuite indiquées par le symbole « ||| » dans le texte pré-traité.

![Pré-traiter le texte](./media/text-analytics-module-tutorial/preprocess-text.png)

Que se passe-t-il si vous souhaitez utiliser une liste de mots vides personnalisée ? Vous pouvez la transmettre en tant qu’entrée facultative. Vous pouvez également utiliser une expression régulière personnalisée en syntaxe C# pour remplacer des sous-chaînes et supprimer des mots en fonction de la partie du message : noms, verbes ou adjectifs.

Une fois le pré-traitement terminé, nous fractionnons les données en jeux d’apprentissage et de test.

## <a name="step-2-extract-numeric-feature-vectors-from-pre-processed-text"></a>Étape 2 : Extraire des vecteurs de fonctions numériques du texte pré-traité
Pour générer un modèle de données de texte, vous devez généralement convertir du texte de forme libre en vecteurs de fonctions numériques. Dans cet exemple, nous utilisons le module [Extract N-Gram Features from Text](https://msdn.microsoft.com/library/azure/mt762916.aspx) (Extraire des fonctionnalités N-grammes du texte) pour transformer les données texte dans ce format. Ce module prend une colonne de mots séparés par des espaces blancs et compile un dictionnaire de mots, ou N-grammes de mots, qui apparaissent dans votre jeu de données. Ensuite, il compte le nombre de fois où chaque mot, ou N-gramme, apparaît dans chaque enregistrement et crée des vecteurs de fonction à partir de ce décompte. Dans ce didacticiel, nous définissons la taille N-gramme sur 2, donc nos vecteurs de fonctions incluent des mots uniques ou des combinaisons de deux mots qui se suivent.

![Extraire les N-grammes](./media/text-analytics-module-tutorial/extract-ngrams.png)

Nous appliquons la pondération TF*IDF (Term Frequency Inverse Document Frequency) aux décomptes N-gramme. Cette approche ajoute la pondération des mots qui apparaissent fréquemment dans un enregistrement unique, mais qui sont rares dans l’ensemble du jeu de données. Les autres options incluent la pondération binaire, TF et graphique.

Ces fonctionnalités de texte ont souvent une dimensionnalité élevée. Par exemple, si votre corpus comporte 100 000 mots uniques, votre espace de fonctionnalité compte 100 000 dimensions, ou plus si des N-grammes sont utilisés. Le module Extraire des fonctionnalités N-grammes vous fournit un ensemble d’options permettant de réduire la dimensionnalité. Vous pouvez choisir d’exclure des mots courts ou longs, ou trop rares ou trop fréquents, afin d’obtenir une valeur prédictive significative. Dans ce didacticiel, nous excluons les N-grammes qui apparaissent dans moins de 5 enregistrements ou dans plus de 80 % des enregistrements.

En outre, vous pouvez utiliser la sélection de fonctionnalités pour sélectionner uniquement les fonctionnalités qui ont la meilleure corrélation avec votre cible de prévision. Nous utilisons la sélection de fonctionnalités Khi-deux pour sélectionner 1 000 fonctionnalités. Vous pouvez afficher le vocabulaire des mots sélectionnés ou N-grammes en cliquant sur le résultat à droite du module d’extraction N-grammes.

En guise d’alternative à l’utilisation du module d’extraction des fonctionnalités N-grammes, vous pouvez utiliser le module de hachage de fonctionnalité. Notez cependant que le module [Hachage de fonctionnalité](https://msdn.microsoft.com/library/azure/dn906018.aspx) ne possède pas de capacité de sélection de fonctionnalité intégrée, ou pondération TF*IDF.

## <a name="step-3-train-classification-or-regression-model"></a>Étape 3 : Former le modèle de classification ou de régression
À présent, le texte a été transformé en colonnes de fonctions numériques. Le jeu de données contient toujours des colonnes de type chaîne issues des étapes précédentes, donc nous utilisons Sélectionner des colonnes dans le jeu de données pour les exclure.

Nous utilisons ensuite [Régression logistique à deux classes](https://msdn.microsoft.com/library/azure/dn905994.aspx) pour prédire notre cible : note de critique élevée ou faible. À ce stade, le problème d’analyse de texte a été transformé en un problème de classification standard. Vous pouvez utiliser les outils disponibles dans Azure Machine Learning pour améliorer le modèle. Par exemple, vous pouvez faire des essais avec des classifieurs différents pour déterminer la précision des résultats qu’ils donnent, ou vous pouvez utiliser le réglage d’hyperparamètre pour améliorer la précision.

![Apprentissage et Notation](./media/text-analytics-module-tutorial/scoring-text.png)

## <a name="step-4-score-and-validate-the-model"></a>Étape 4 : Noter et valider le modèle
Comment valider le modèle formé ? Nous le notons en fonction du jeu de données de test et nous en évaluons la précision. Toutefois, le modèle a appris le vocabulaire des N-grammes et leur pondération à partir du jeu de données d’apprentissage. Par conséquent, nous devons utiliser ce vocabulaire et ces pondérations lors de l’extraction de fonctionnalités à partir des données de test, plutôt que de créer de nouveau le vocabulaire. Par conséquent, nous ajoutons le module d’extraction de fonctionnalités N-grammes à la branche de notation de l’expérience, nous connectons le vocabulaire de sortie à partir de la branche d’apprentissage et nous définissons le mode du vocabulaire en lecture seule. Nous désactivons également le filtrage de N-grammes en fonction de la fréquence en définissant le minimum sur 1 instance et le maximum sur 100 %, et en désactivant la sélection de fonctionnalités.

Une fois que la colonne de texte dans les données de test a été transformée en colonnes de fonctions numériques, nous excluons les colonnes de type chaîne des étapes précédentes, comme dans la branche d’apprentissage. Nous utilisons ensuite le module Noter le modèle pour effectuer des prévisions et le module Évaluer un modèle pour évaluer la précision.

## <a name="step-5-deploy-the-model-to-production"></a>Étape 5 : Déployer le modèle en production
Le modèle est presque prêt à être déployé en production. S’il est déployé en tant que service web, il accepte la chaîne de texte de forme libre comme entrée et retourne une prévision « élevée » ou « faible ». Il utilise le vocabulaire N-gramme appris pour transformer le texte en fonctionnalités et le modèle de régression logistique formé pour établir une prévision à partir de ces fonctionnalités. 

Pour configurer l’expérience de prévision, nous enregistrons tout d’abord le vocabulaire N-gramme en tant que jeu de données et le modèle de régression logistique formé à partir de la branche d’apprentissage de l’expérience. Ensuite, nous enregistrons l’expérience à l’aide de l’option « Enregistrer sous » pour créer un graphique expérimental pour l’expérience prédictive. Nous supprimons le module Fractionner les données et la branche d’apprentissage de l’expérience. Nous connectons ensuite le vocabulaire N-gramme et le modèle précédemment enregistrés aux modules Extraire des fonctionnalités N-grammes et Noter le modèle, respectivement. Nous supprimons également le module Évaluer un modèle.

Nous insérons le module Sélectionner des colonnes dans le jeu de données avant le module Pré-traiter le texte pour supprimer la colonne d’étiquette, et désélectionnons l’option « Ajouter la colonne de score au jeu de données » dans le module Noter. De cette façon, le service web ne demande pas l’étiquette qu'il tente de prédire et ne répète pas les fonctionnalités d’entrée dans la réponse.

![Expérience prédictive](./media/text-analytics-module-tutorial/predictive-text.png)

Nous avons à présent une expérience qui peut être publiée en tant que service web et appelée à l’aide des API requête-réponse ou exécution de lot.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les modules d’analyse de texte à partir de la [documentation MSDN](https://msdn.microsoft.com/library/azure/dn905886.aspx).

