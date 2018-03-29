---
title: Optimiser les algorithmes dans Azure Machine Learning | Microsoft Docs
description: Explique comment choisir l’ensemble de paramètres optimal pour un algorithme dans Microsoft Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 6717e30e-b8d8-4cc1-ad0b-1d4727928d32
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 3e4423f0a92b08ff7fa7dd8edf430f27ff2d464e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning"></a>Sélection des paramètres permettant d’optimiser des algorithmes dans Microsoft Azure Machine Learning
Cette rubrique explique comment choisir le bon ensemble d’hyperparamètres pour un algorithme dans Microsoft Azure Machine Learning. La plupart des algorithmes Machine Learning ont des paramètres qui doivent être définis. Lorsque vous gérez l’apprentissage d’un modèle, vous devez fournir des valeurs pour ces paramètres. L’efficacité du modèle formé dépend des paramètres de modèle choisis. Le processus de recherche de l’ensemble optimal de paramètres est connu sous le nom de *sélection du modèle*.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

Il existe différentes manières d’effectuer une sélection de modèle. Dans ML, la méthode de validation croisée est l’une des plus largement utilisées pour la sélection de modèle. Il s’agit du mécanisme par défaut utilisé à cette fin dans Azure Machine Learning. Comme les langages R et Python sont pris en charge par Azure Machine Learning, vous pouvez toujours implémenter votre propre mécanisme de sélection de modèle, via l’un ou l’autre de ces langages.

Le processus de recherche de l’ensemble de paramètres idéal comprend quatre étapes :

1. **Définir l’espace de paramètre** : pour l’algorithme, vous devez d’abord déterminer les valeurs de paramètres exactes que vous souhaitez prendre en compte.
2. **Définir les paramètres de validation croisée** : déterminez comment choisir les plis de validation croisée pour le jeu de données.
3. **Définir la mesure** : déterminez la mesure à utiliser pour évaluer l’ensemble de paramètres le plus approprié (exactitude, erreur quadratique moyenne, précision, rappel ou f-score).
4. **Apprentissage, évaluation et comparaison** : pour chaque combinaison unique de valeurs de paramètres, la validation croisée est effectuée selon la mesure d’erreur que vous définissez. Après évaluation et comparaison, vous pouvez choisir le modèle le plus performant.

L’image ci-dessous illustre cette opération dans Azure Machine Learning.

![Trouver l’ensemble de paramètres idéal](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Définir l’espace de paramètre
L’ensemble de paramètres peut être défini lors de l’étape d’initialisation du modèle. Le volet de paramètres de l’ensemble des algorithmes ML propose deux modes de formation : *Paramètre unique* et *Plage de paramètres*. Choisissez le mode Plage de paramètres. En mode Plage de paramètres, vous pouvez entrer plusieurs valeurs pour chaque paramètre. Vous pouvez entrer des valeurs séparées par des virgules dans la zone de texte.

![Arbre de décision optimisé à deux classes, paramètre unique](./media/algorithm-parameters-optimize/fig2.png)

 Vous pouvez également définir les points minimum et maximum de la grille et le nombre total de points à générer à l’aide de l’option **Utiliser le générateur de plage**. Par défaut, les valeurs de paramètre sont générées sur une échelle linéaire. Cependant, si l’option **Échelle logarithmique** est activée, les valeurs sont générées sur une échelle logarithmique (selon laquelle le rapport entre les points adjacents est constant, et non leur différence). Pour les paramètres entiers, vous pouvez définir une plage à l’aide d’un trait d’union. Par exemple, « 1-10 » signifie que tous les entiers compris entre 1 et 10 (tous deux inclus) forment le jeu de paramètres. Un mode mixte est également pris en charge. Par exemple, l’ensemble de paramètres « 1-10, 20, 50 » inclut les entiers 1-10, 20 et 50.

![Arbre de décision optimisé à deux classes, plage de paramètres](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Définir les plis de validation croisée
Le module [Partition et échantillon][partition-and-sample] peut être utilisé pour affecter des plis aux données, de manière aléatoire. Dans l’exemple de configuration suivant pour ce module, nous allons définir cinq plis et affecter au hasard le nombre de plis aux exemples d’instances.

![Partition et échantillon](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Définir la mesure
Le module [Tune Model Hyperparameters][tune-model-hyperparameters] assure la prise en charge de la sélection empirique du meilleur ensemble de paramètres pour un algorithme et un jeu de données spécifiques. En plus d’autres informations concernant l’apprentissage du modèle, le volet des **propriétés** de ce module comprend la mesure à utiliser pour déterminer le meilleur ensemble de paramètres. Il présente deux listes déroulantes différentes pour les algorithmes de classification et de régression, respectivement. Si l’algorithme en question est un algorithme de classification, la mesure de régression est ignorée, et vice versa. Dans cet exemple, la mesure est **Exactitude** .   

![Paramètres de balayage](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Apprentissage, évaluation et comparaison
Le même module [Tune Model Hyperparameters][tune-model-hyperparameters] gère l’apprentissage de tous les modèles correspondant à l’ensemble de paramètres, évalue diverses mesures et crée ensuite le meilleur modèle formé en fonction de la mesure choisie. Ce module dispose de deux entrées obligatoires :

* Apprenant non formé
* Jeu de données

Le module dispose également d’un jeu de données d’entrée facultatif. Connectez le jeu de données incluant des informations sur les plis au jeu de données en entrée obligatoire. Si le jeu de données n’est associé à aucune information sur les plis, une validation croisée de 10 plis est exécutée automatiquement, par défaut. Si l’affectation de plis n’est pas effectuée et si un jeu de données de validation est fourni au port de jeu de données facultatif, un mode de test de formation est sélectionné et le premier jeu de données est utilisé pour gérer l’apprentissage du modèle pour chaque combinaison de paramètres.

![Classifieur d’arbre de décision optimisé](./media/algorithm-parameters-optimize/fig6a.png)

Le modèle est ensuite évalué sur le jeu de données de validation. Le port de sortie de gauche du module affiche des mesures différentes comme fonctions des valeurs de paramètres. Le port de sortie de droite indique le modèle formé correspondant au modèle le plus performant, en fonction de la mesure choisie (dans ce cas, l’**exactitude**).  

![Jeu de données de validation](./media/algorithm-parameters-optimize/fig6b.png)

Vous pouvez voir les paramètres exacts choisis en visualisant le port de sortie de droite. Ce modèle peut être utilisé lors du calcul de la notation d’un ensemble de test ou dans un service web mis en œuvre après l’enregistrement en tant que modèle formé.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
