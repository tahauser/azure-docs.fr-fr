---
title: 'Étape 4 : formation et évaluation des modèles d’analyse prédictive | Microsoft Docs'
description: 'Étape 4 de la procédure pas à pas du développement d’une solution prédictive : formation, notation et évaluation des multiples modèles dans Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: d905f6b3-9201-4117-b769-5f9ed5ee1cac
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.openlocfilehash: 3f722fa314b42501e0f6144f71b9d74c0a9127e9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-4-train-and-evaluate-the-predictive-analytic-models"></a>Étape 4 de la procédure pas à pas : formation et évaluation des modèles d’analyse prédictive
Cette rubrique décrit la quatrième étape de la procédure pas à pas [Développement d’une solution d’analyse prédictive avec Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Créer un espace de travail Machine Learning](walkthrough-1-create-ml-workspace.md)
2. [Télécharger des données existantes](walkthrough-2-upload-data.md)
3. [Créer une expérience](walkthrough-3-create-new-experiment.md)
4. **Former et évaluer les modèles**
5. [Déployer le service web](walkthrough-5-publish-web-service.md)
6. [Accéder au service web](walkthrough-6-access-web-service.md)

- - -
Un des avantages de l’utilisation d’Azure Machine Learning Studio pour créer des modèles d’apprentissage automatique est la possibilité d’essayer simultanément plusieurs types de modèle dans une expérience et de comparer les résultats. Ce type d’expérimentation vous aide à trouver la meilleure solution à votre problème.

Dans l’expérience développée au fil de cette procédure pas à pas, nous allons créer deux types de modèles, puis comparer les résultats d’évaluation afin de choisir l’algorithme que nous utiliserons dans notre expérience finale.  

Nous avons le choix entre différents modèles. Pour connaître les modèles disponibles, développez le nœud **Machine Learning** dans la palette des modules, puis développez **Initialiser le modèle** et les nœuds inférieurs. Pour cette expérience, nous allons sélectionner les modules [Machines à vecteurs de support à deux classes][two-class-support-vector-machine] et [Arbres de décision optimisé à deux classes][two-class-boosted-decision-tree].    

> [!TIP]
> Pour déterminer facilement l’algorithme Machine Learning le mieux adapté au problème à résoudre, consultez [Comment choisir les algorithmes dans Microsoft Azure Machine Learning](algorithm-choice.md).
> 
> 

## <a name="train-the-models"></a>Formation des modèles

Nous allons ajouter le module [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree] et le module [Machines à vecteurs de support à deux classes][two-class-support-vector-machine] dans cette expérience.

### <a name="two-class-boosted-decision-tree"></a>Arbre de décision optimisé à deux classes

Configurons d’abord le modèle Arbre de décision optimisé.

1. Recherchez le module [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree] dans la palette des modules et faites-le glisser sur la zone de dessin.

2. Recherchez le module [Former le modèle][train-model], faites-le glisser sur la zone de dessin et connectez la sortie du module [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree] au port d’entrée de gauche du module [Former le modèle][train-model].
   
   Le module [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree] initialise le modèle générique, tandis que le module [Former le modèle][train-model] utilise les données d’apprentissage pour former le modèle. 

3. Connectez la sortie de gauche du module [Exécuter le script R][execute-r-script] de gauche au port d’entrée de droite du module [Former le modèle][train-model] (à l’[étape 3](walkthrough-3-create-new-experiment.md) de cette procédure, nous avons décidé d’utiliser les données provenant du côté gauche du module Fractionner les données pour la formation).
   
   > [!TIP]
   > Pour cette expérience, nous n’avons pas besoin de deux des entrées et de l’une des sorties du module [Exécuter le script R][execute-r-script]. Nous pouvons donc ne les laisser sans liaison. 
   > 
   > 

Cette partie de l'expérience ressemble alors à ce qui suit :  

![Training a model][1]

Maintenant, nous devons indiquer au module [Former le modèle][train-model] que nous voulons utiliser le modèle pour prédire la valeur Risque de crédit.

1. Sélectionnez le module [Former le modèle][train-model]. Dans le volet **Propriétés**, cliquez sur **Lancer le sélecteur de colonne**.

2. Dans la boîte de dialogue **Sélectionner une seule colonne**, tapez « risque de crédit » dans le champ de recherche sous **Colonnes disponibles**, sélectionnez « Risque de crédit » ci-dessous et cliquez sur la flèche droite (**>**) pour déplacer « Risque de crédit » vers **Colonnes sélectionnées**. 

    ![Sélectionnez la colonne Risque de crédit pour le module Former le modèle][0]

3. Cliquez sur la coche **OK**.

### <a name="two-class-support-vector-machine"></a>Machine à vecteurs de support à deux classes

Nous configurons ensuite le modèle SVM.  

Tout d’abord, une petite explication sur SVM. Les arbres de décision optimisés fonctionnent bien avec tout type de caractéristique. Toutefois, le module SVM générant un classifieur linéaire, le modèle qu'il génère obtient la meilleure erreur de test quand toutes les caractéristiques numériques sont à la même échelle. Pour convertir toutes les fonctions numériques à la même échelle, nous utilisons une transformation « Tanh » (avec le module [Normaliser les données][normalize-data]). Cette opération transforme les nombres en plage [0,1]. Le module SVM convertit les fonctions de chaîne en fonctions catégorielles, puis en fonctions 0/1 binaires. Il est donc inutile de les transformer manuellement. De même, nous ne voulons pas transformer la colonne Risque du crédit (colonne 21). Elle est numérique et contient la valeur que nous apprenons à prédire au modèle ; nous devons donc la laisser seule.  

Pour configurer le modèle SVM, procédez comme suit :

1. Recherchez le module [Machine à vecteurs de support à deux classes][two-class-support-vector-machine] dans la palette des modules et faites-le glisser sur la zone de dessin.

2. Cliquez avec le bouton droit sur le module [Former le modèle][train-model], sélectionnez **Copier**, puis cliquez avec le bouton droit sur la zone de dessin et sélectionnez **Coller**. La copie du module [Former le modèle][train-model] contient les mêmes colonnes que l’original.

3. Connectez la sortie du module [Machines à vecteurs de support à deux classes][two-class-support-vector-machine] au port d’entrée de gauche du second module [Former le modèle][train-model].

4. Trouvez le module [Normaliser les données][normalize-data] et faites-le glisser vers la zone de dessin.

5. Connectez la sortie de gauche du module [Exécuter le script R][execute-r-script] de gauche à l’entrée de ce module (notez que le port de sortie d’un module peut être connecté à plusieurs autres modules).

6. Connectez le port de sortie de gauche du module [Normaliser les données][normalize-data] au port d’entrée de droite du deuxième module [Former le modèle][train-model].

Cette partie de l'expérience ressemble alors à ceci :  

![Training the second model][2]  

Configurez maintenant le module [Normaliser les données][normalize-data] :

1. Cliquez pour sélectionner le module [Normaliser les données][normalize-data]. Dans le volet **Propriétés**, sélectionnez **Tanh** comme paramètre de la **Méthode de transformation**.

2. Cliquez sur **Lancer le sélecteur de colonne**, sélectionnez « Aucune colonne » pour **Commencer par**, sélectionnez **Inclure** dans la première liste déroulante, **Type de colonne** dans la deuxième, puis **Numérique** dans la troisième. Cette action spécifie que toutes les colonnes numériques (et elles seules) sont transformées.

3. Cliquez sur le signe plus (+) à droite de cette ligne. Cette opération crée une ligne de listes déroulantes. Sélectionnez **Exclure** dans la première liste déroulante, sélectionnez des **noms de colonne** dans la deuxième liste déroulante et entrez « Risque de crédit » dans le champ textuel. Cette opération indique que la colonne Risque de crédit doit être ignorée (en effet, celle-ci étant numérique, elle serait transformée).

4. Cliquez sur la coche **OK**.  

    ![Sélectionner des colonnes pour le module Normaliser les données][5]

Le module [Normaliser les données][normalize-data] est maintenant configuré pour effectuer une transformation Tanh sur toutes les colonnes numériques, à l’exception de la colonne Risque de crédit.  

## <a name="score-and-evaluate-the-models"></a>Notation et évaluation des modèles

Nous utilisons les données de test exclues par le module [Fractionner les données][split] pour noter nos modèles formés. Nous pouvons ensuite comparer les résultats des deux modèles pour savoir lequel donne les meilleurs résultats.  

### <a name="add-the-score-model-modules"></a>Ajouter les modules Noter le modèle

1. Recherchez le module [Noter le modèle][score-model] et faites-le glisser sur la zone de dessin.

2. Connectez le module [Former le modèle][train-model] relié au module [Arbre de décision optimisé à deux classes][two-class-boosted-decision-tree], au port d’entrée de gauche du module [Noter le modèle][score-model].

3. Connectez le module [Exécuter le script R][execute-r-script] (nos données de test) au port d’entrée de droite du module [Noter le modèle][score-model].

    ![Module Noter le modèle connecté][6]
   
   Le module [Noter le modèle][score-model] peut maintenant récupérer les informations sur le crédit dans les données de test, les traiter à l’aide du modèle et comparer les prévisions générées par le modèle à la colonne Risque de crédit dans les données de test.

4. Copiez et collez le module [Noter le modèle][score-model] pour créer une deuxième copie.

5. Connectez la sortie du modèle SVM (c’est-à-dire le port de sortie du module [Former le modèle][train-model] relié au module [Arbre de décision optimisé à deux classes][two-class-support-vector-machine]) au port d’entrée du deuxième module [Noter le modèle][score-model].

6. Pour le modèle SVM, nous devons effectuer la même transformation pour tester les données comme nous l’avons fait pour les données de formation. Donc, copiez et collez le module [Normaliser les données][normalize-data] pour créer une autre copie et connectez-la au module [Exécuter le script R][execute-r-script] de droite.

7. Connectez la sortie de gauche du deuxième module [Noter le modèle][normalize-data] au port d’entrée de droite du deuxième module [Noter le modèle][score-model].

    ![Deux modules Noter le modèle connectés][7]

### <a name="add-the-evaluate-model-module"></a>Ajouter le module Évaluer le modèle

Pour évaluer les deux résultats et les comparer, nous utilisons un module [Évaluer le modèle][evaluate-model].  

1. Recherchez le module [Évaluer le modèle][evaluate-model] et faites-le glisser vers la zone de dessin.

2. Connectez le port de sortie du module [Noter le modèle][score-model] associé au modèle Arbre de décision optimisé, au port d’entrée gauche du module [Évaluer le modèle][evaluate-model].

3. Connectez l’autre module [Noter le modèle][score-model] au port d’entrée de droite.  

    ![Module Évaluer le modèle connecté][8]

### <a name="run-the-experiment-and-check-the-results"></a>Exécuter l’expérience et vérifier les résultats

Pour exécuter l’expérience, cliquez sur le bouton **EXÉCUTER** sous le canevas. Cette opération peut prendre quelques minutes. Un indicateur rotatif sur chaque module indique que l’exécution est en cours. Puis une coche verte s’affiche pour signaler que l’exécution du module est terminée. Lorsque tous les modules comportent une coche, l'exécution de l'expérience est terminée.

L'expérience doit ressembler à ceci :  

![Evaluating both models][3]

Pour vérifier les résultats, cliquez sur le port de sortie du module [Évaluer le modèle][evaluate-model] et sélectionnez **Visualiser**.  

Le module [Évaluer le modèle][evaluate-model] produit une paire de courbes et de mesures qui vous permettent de comparer les résultats des deux modèles notés. Vous pouvez afficher les résultats sous forme de courbes Receiver Operator Characteristic (ROC), Precision/Recall ou Lift. Les données supplémentaires affichées comprennent une matrice de confusion, les valeurs cumulées pour l’aire sous la courbe (ASC) et d’autres mesures. Vous pouvez modifier la valeur du seuil en déplaçant le curseur vers la gauche ou la droite et voir son influence sur l'ensemble des mesures.  

À droite du graphique, cliquez sur **Jeu de données noté** ou **Jeu de données noté à comparer** pour mettre en surbrillance la courbe associée et afficher les mesures associées en dessous. Dans la légende des courbes, « Jeu de données noté » correspond au port d’entrée de gauche du module [Évaluer le modèle][evaluate-model] (en l’occurrence, le modèle Arbre de décision optimisé). « Jeu de données noté à comparer » correspond au port d’entrée de droite, le modèle SVM dans notre cas. Lorsque vous cliquez sur une de ces étiquettes, la courbe de ce modèle apparaît en surbrillance ainsi que les mesures correspondantes, comme dans le graphique suivant.  

![ROC curves for models][4]

En examinant ces valeurs, vous pouvez déterminer quel modèle est le plus susceptible de fournir les résultats que vous recherchez. Vous pouvez revenir en arrière et relancer votre expérience en modifiant les valeurs des différents modèles. 

Cette procédure n’explique pas comment interpréter ces résultats et optimiser les performances du modèle. Pour en savoir plus, consultez les articles suivants :
- [Évaluation des performances d’un modèle dans Azure Machine Learning](evaluate-model-performance.md)
- [Sélection des paramètres permettant d’optimiser des algorithmes dans Microsoft Azure Machine Learning](algorithm-parameters-optimize.md)
- [Interprétation des résultats de modèle dans Azure Machine Learning](interpret-model-results.md)

> [!TIP]
> À chaque exécution de l’expérience, un enregistrement de cet essai est conservé dans l’historique d’exécution. Vous pouvez afficher tous les essais de votre expérience (et y revenir) en cliquant sur **AFFICHER L'HISTORIQUE D'EXÉCUTION** , sous le canevas. Vous pouvez également cliquer sur **Exécution précédente** dans le volet **Propriétés** pour revenir à l’exécution précédant immédiatement celle que vous avez ouverte.
> 
> Vous pouvez faire une copie d’un essai de votre expérience en cliquant sur **ENREGISTRER SOUS** sous le canevas. 
> Utilisez les propriétés **Résumé** et **Description** de l’expérience pour conserver un enregistrement de vos essais dans les itérations de l’expérience.
> 
> Pour en savoir plus, consultez la section [Gérer les itérations des expériences dans Microsoft Azure Machine Learning Studio](manage-experiment-iterations.md).  
> 
> 

- - -
 **: [Déployer le service web](walkthrough-5-publish-web-service.md)**

[0]: ./media/walkthrough-4-train-and-evaluate-models/train-model-select-column.png
[1]: ./media/walkthrough-4-train-and-evaluate-models/experiment-with-train-model.png
[2]: ./media/walkthrough-4-train-and-evaluate-models/svm-model-added.png
[3]: ./media/walkthrough-4-train-and-evaluate-models/final-experiment.png
[4]: ./media/walkthrough-4-train-and-evaluate-models/roc-curves.png
[5]: ./media/walkthrough-4-train-and-evaluate-models/normalize-data-select-column.png
[6]: ./media/walkthrough-4-train-and-evaluate-models/score-model-connected.png
[7]: ./media/walkthrough-4-train-and-evaluate-models/both-score-models-added.png
[8]: ./media/walkthrough-4-train-and-evaluate-models/evaluate-model-added.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
