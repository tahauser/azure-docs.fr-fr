---
title: Expérience simple dans Machine Learning Studio | Microsoft Docs
description: Ce didacticiel sur l’apprentissage automatique vous guidera tout au long d’une expérience de science des données simple. Nous allons prédire le prix d’une voiture à l’aide d’un algorithme de régression.
keywords: expérience,régression linéaire,algorithmes d’apprentissage automatique,didacticiel d’apprentissage automatique,techniques de modélisation prédictive,expérience de science de données
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/20/2017
ms.openlocfilehash: 57461cf94898012619b865c455955f511e978995
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="machine-learning-tutorial-create-your-first-data-science-experiment-in-azure-machine-learning-studio"></a>Didacticiel sur l’apprentissage automatique : Création de votre première expérience de science des données dans Azure Machine Learning Studio

Si vous n’avez encore jamais utilisé **Azure Machine Learning Studio**, ce didacticiel est pour vous.

Dans ce didacticiel, nous allons découvrir comment utiliser Studio afin de créer une expérience de machine learning. Cette expérience permettra de tester un modèle analytique qui prédira le prix d’un véhicule automobile en fonction de différentes variables, comme la marque et les caractéristiques techniques.

> [!NOTE]
> Ce didacticiel vous montre les principes fondamentaux de glisser-déplacer des modules vers votre expérience, et il vous explique comment les connecter, réaliser votre expérience et étudier les résultats. Nous n’allons pas aborder le sujet général du machine learning ni expliquer comment sélectionner et utiliser la centaine de modules de manipulation des données et des algorithmes inclus dans Studio.
>
>Si vous ne connaissez pas le machine learning, la série de vidéos [Science des données pour les débutants](data-science-for-beginners-the-5-questions-data-science-answers.md) peut être un bon point de départ. Cette série de vidéos constitue une excellente introduction au machine learning, qui utilise le langage et les concepts courants.
>
>Si vous connaissez le Machine Learning et recherchez des informations plus générales sur Machine Learning Studio et sur les algorithmes de Machine Learning qu’il propose, voici quelques ressources utiles :
>
- [Qu'est-ce que Machine Learning Studio ?](what-is-ml-studio.md) : il s’agit d’une vue d’ensemble de Studio.
- [Principes de base de l’apprentissage automatique avec exemples d’algorithmes](basics-infographic-with-algorithm-examples.md) : cette infographie vous permet de découvrir les différents types d’algorithmes de machine learning inclus dans Machine Learning Studio.
- [Guide Machine Learning](https://gallery.cortanaintelligence.com/Tutorial/Machine-Learning-Guide-1) : ce guide offre des informations semblables à celles de l’infographie ci-avant, mais dans un format interactif.
- [Aide-mémoire d’algorithme Machine Learning](algorithm-cheat-sheet.md) et [Comment choisir les algorithmes dans Microsoft Azure Machine Learning](algorithm-choice.md) : cette affiche téléchargeable et l’article qui l’accompagne traitent des algorithmes Studio en détail.
- [Machine Learning Studio : aide sur les algorithmes et les modules](https://msdn.microsoft.com/library/azure/dn905974.aspx) : il s’agit du document de référence complet pour tous les modules Studio, y compris les algorithmes de machine learning

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="how-does-machine-learning-studio-help"></a>En quoi Machine Learning Studio est-il utile ?

Machine Learning Studio facilite la configuration d’une expérience à l’aide de modules à glisser-déplacer préprogrammés avec des techniques de modélisation prédictive.

Dans un espace de travail interactif visuel, vous effectuer un glisser-déplacer de ***jeux de données*** et de ***modules*** d’analyse vers un canevas interactif. Vous les associez afin de former une ***expérience*** que vous pouvez exécuter dans Machine Learning Studio.
Vous ***créez un modèle***, ***le formez***, puis ***lui attribuez une note et le testez***.

Vous pouvez affiner la conception de votre modèle en modifiant l’expérience et en l’exécutant jusqu’à ce que vous obteniez les résultats escomptés. Lorsque votre modèle est prêt, vous pouvez le publier en tant que ***service web*** afin que d’autres utilisateurs puissent envoyer de nouvelles données et recevoir des prédictions en retour.

## <a name="open-machine-learning-studio"></a>Ouverture de Machine Learning Studio

Pour prendre en main Studio, accédez à [https://studio.azureml.net](https://studio.azureml.net). Si vous vous êtes déjà connecté à Machine Learning Studio par le passé, cliquez sur **Sign In** (Se connecter). Sinon, cliquez sur **Sign up here** (S’inscrire) et faites votre choix parmi les options gratuites et payantes.

![Connexion à Machine Learning Studio][sign-in-to-studio]
<br/>
***Connexion à Machine Learning Studio***

## <a name="five-steps-to-create-an-experiment"></a>Cinq étapes pour créer une expérience

Dans ce didacticiel dédié au machine learning, vous allez suivre cinq étapes de base afin de concevoir une expérience dans Machine Learning Studio et ainsi créer, former et noter votre modèle :

- **Création d’un modèle**
    - [Étape 1 : obtention des données]
    - [Étape 2 : préparation des données]
    - [Étape 3 : définition des fonctionnalités]
- **Formation du modèle**
    - [Étape 4 : sélection et application d’un algorithme d’apprentissage]
- **Notation et test du modèle**
    - [Étape 5 : prédiction des nouveaux prix des voitures]

[Étape 1 : obtention des données]: #step-1-get-data
[Étape 2 : préparation des données]: #step-2-prepare-the-data
[Étape 3 : définition des fonctionnalités]: #step-3-define-features
[Étape 4 : sélection et application d’un algorithme d’apprentissage]: #step-4-choose-and-apply-a-learning-algorithm
[Étape 5 : prédiction des nouveaux prix des voitures]: #step-5-predict-new-automobile-prices

> [!TIP] 
> Vous pouvez obtenir une copie de travail de l’expérience suivante dans la [galerie Azure AI](https://gallery.cortanaintelligence.com). Accédez à **[Your first data science experiment - Automobile price prediction](https://gallery.cortanaintelligence.com/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** (Votre première expérience de science des données : prédiction sur les prix automobiles) et cliquez sur **Ouvrir dans Studio** pour télécharger une copie de l’expérience dans votre espace de travail Machine Learning Studio.


## <a name="step-1-get-data"></a>Étape 1 : obtention des données

Tout d’abord, vous devez obtenir les données.
Vous pouvez utiliser plusieurs exemples de jeux de données inclus dans Machine Learning Studio ou importer des données à partir de sources diverses. Pour les besoins de cet exemple, nous allons utiliser le jeu de données **Données sur le prix des véhicules automobiles (brutes)** inclus dans votre espace de travail.
Ce jeu de données comprend des entrées relatives à plusieurs véhicules, notamment des informations sur la marque, le modèle, les caractéristiques techniques et le prix.

Voici comment obtenir ce jeu de données dans le cadre de votre expérience.

1. Créez une expérience en cliquant sur l’option **+NOUVEAU** située en bas de la fenêtre Machine Learning Studio, sélectionnez **EXPÉRIENCE**, puis **Expérience vide**.

2. Un nom par défaut est attribué à l’expérience : il apparaît en haut du canevas. Sélectionnez le texte et remplacez-le par un nom plus significatif, par exemple **Prédiction sur les prix automobiles**. Le nom n’a pas besoin d’être unique.

    ![Renommer l’expérience][rename-experiment]

2. Sur la gauche de la zone de dessin de l’expérience se trouve une palette de jeux de données et de modules. Tapez la valeur **automobile** dans la zone de recherche se trouvant en haut de cette palette, afin de rechercher le jeu de données **Données sur le prix des véhicules automobiles (brutes)**. Faites glisser ce jeu de données vers le canevas de l’expérience.

    ![Recherchez le jeu de données d’automobile et faites-le glisser vers le canevas de l’expérience][type-automobile]
    <br/>
    ***Recherchez le jeu de données d’automobile et faites-le glisser vers le canevas de l’expérience***

Pour voir à quoi ressemblent ces données, cliquez sur le port de sortie situé en bas du jeu de données d’automobile, puis sélectionnez **Visualiser**.

![Cliquez sur le port de sortie et sélectionnez Visualiser][select-visualize]
<br/>
***Cliquez sur le port de sortie et sélectionnez Visualiser***

> [!TIP]
> Les jeux de données et les modules disposent de ports d’entrée et de sortie représentés par de petits cercles : les ports d’entrée se situent en haut, tandis que les ports de sortie se situent en bas.
Pour créer un flux de données dans votre expérience, connectez le port de sortie d’un module au port d’entrée d’un autre module.
À tout moment, vous pouvez cliquer sur le port de sortie d’un jeu de données ou d’un module afin de voir à quoi les données ressemblent à ce stade dans le flux de données.

Dans cet exemple de jeu de données, chaque instance de véhicule automobile apparaît sous la forme d’une ligne, et les variables associées à chaque véhicule automobile apparaissent dans des colonnes. Compte tenu des variables associées à un véhicule automobile spécifique, nous allons essayer de prédire le prix dans la colonne de droite (colonne 26, intitulée « price »).

![Affichez les données automobiles dans la fenêtre de visualisation des données][visualize-auto-data]
<br/>
***Affichez les données automobiles dans la fenêtre de visualisation des données***

Fermez la fenêtre de visualisation en cliquant sur le symbole «**x**» dans le coin supérieur droit.

## <a name="step-2-prepare-the-data"></a>Étape 2 : préparation des données

Pour pouvoir être analysé, un jeu de données nécessite généralement un traitement préalable. Vous avez peut-être remarqué l’absence de certaines valeurs dans les colonnes des différentes lignes. Pour que vous puissiez analyser les données correctement, ces valeurs manquantes doivent être nettoyées. Dans le cas qui nous occupe, nous allons supprimer toutes les lignes dans lesquelles des valeurs sont manquantes. De plus, la colonne **normalized-losses** contient une grande quantité de valeurs manquantes. Nous allons donc l’exclure du modèle.

> [!TIP]
> Le nettoyage des valeurs manquantes des données d’entrée est un prérequis pour l’utilisation de la plupart des modules.

Nous commençons par ajouter un module qui supprime la colonne **normalized-losses**, puis nous ajoutons un module qui supprime toute ligne dans laquelle des données manquent.

1. Dans la zone de recherche située sur la partie supérieure de la palette de modules, saisissez la chaîne **sélectionner des colonnes** afin de rechercher le module [Sélectionner des colonnes dans le jeu de données][select-columns], puis faites glisser ce module vers le canevas de l’expérience. Ce module permet de sélectionner les colonnes de données à inclure ou exclure du modèle.

2. Connectez le port de sortie du jeu de données **Données sur le prix des véhicules automobiles (brutes)** au port d’entrée du module [Sélectionner des colonnes dans le jeu de données][select-columns].

    ![Ajoutez le module Sélectionner des colonnes dans le jeu de données dans le canevas de l’expérience et connectez-le][type-select-columns]
    <br/>
    ***Ajoutez le module « Sélectionner des colonnes dans le jeu de données » dans le canevas de l’expérience et connectez-le***

3. Cliquez sur le module [Sélectionner des colonnes dans le jeu de données][select-columns], puis cliquez sur **Lancer le sélecteur de colonne** dans le volet **Propriétés**.

    - Sur la gauche, cliquez sur **With rules**
    - Sous **Commencer par**, cliquez sur **Toutes les colonnes**. Vous indiquez ainsi au module [Sélectionner des colonnes dans le jeu de données][select-columns] de transmettre toutes les colonnes, sauf celles que nous nous apprêtons à exclure.
    - Dans les listes déroulantes, sélectionnez **Exclure** et **Noms des colonnes**, puis cliquez dans la zone de texte. Une liste de colonnes s’affiche. Sélectionnez la colonne **normalized-losses**, qui est alors ajoutée à la zone de texte.
    - Cliquez sur le bouton en forme de coche (OK) pour fermer le sélecteur de colonne (en bas à droite).

    ![Lancez le sélecteur de colonne et excluez la colonne « normalized-losses »][launch-column-selector]
    <br/>
    ***Lancez le sélecteur de colonne et excluez la colonne « normalized-losses »***

    À présent, le volet de propriétés du module **Sélectionner des colonnes dans le jeu de données** indique qu’il transmettra toutes les colonnes du jeu de données, à l’exception de **normalized-losses**.

    ![Le volet Propriétés indique que la colonne « normalized-losses » est exclue][showing-excluded-column]
    <br/>
    ***Le volet Propriétés indique que la colonne « normalized-losses » est exclue***

    > [!TIP]
    Vous pouvez ajouter un commentaire dans un module en double-cliquant sur ce module, puis en saisissant du texte. Ceci peut vous aider à voir d'un seul coup d'œil ce que fait chaque module dans votre expérience. Dans ce cas, double-cliquez sur le module [Sélectionner des colonnes dans le jeu de données][select-columns] et saisissez le commentaire suivant : « Exclure les pertes normalisées ».


    ![Double-cliquez sur un module pour ajouter un commentaire][add-comment]
    <br/>
    ***Double-cliquez sur un module pour ajouter un commentaire***

3. Faites glisser le module [Nettoyer les données manquantes][clean-missing-data] vers la zone de dessin de l’expérience et connectez-le au module [Sélectionner des colonnes dans le jeu de données][select-columns]. Dans le volet **Propriétés**, sélectionnez **Supprimer toute la ligne** sous **Mode de nettoyage**. Cela amène le module [Nettoyage des données manquantes][clean-missing-data] à nettoyer les données en supprimant les lignes pour lesquelles des valeurs manquent. Double-cliquez sur le module et saisissez le commentaire suivant : « Supprimer les lignes de valeur manquantes ».

    ![Définissez le mode de nettoyage du module « Nettoyage des données manquantes » sur « Supprimer toute la ligne »][set-remove-entire-row]
    <br/>
    ***Définissez le mode de nettoyage du module « Nettoyage des données manquantes » sur « Supprimer toute la ligne »***

4. Exécutez l’expérience en cliquant sur **EXÉCUTER** au bas de la page.

    Une fois l’expérience terminée, une coche verte s’affiche en regard de chaque module pour indiquer la réussite de leurs opérations. Notez que le statut **Exécution terminée** s’affiche dans le coin supérieur droit de la fenêtre.

![Une fois l’exécution terminée, l’expérience doit ressembler à ceci :][early-experiment-run]
<br/>
***Une fois l’exécution terminée, l’expérience doit ressembler à ceci :***

> [!TIP]
> Pourquoi exécutons-nous l’expérience maintenant ? Lors de l’exécution de l’expérience, les définitions de colonne de nos données sont transmises à partir du jeu de données, par le biais des modules [Sélectionner des colonnes dans le jeu de données][select-columns] et [Nettoyage des données manquantes][clean-missing-data]. Autrement dit, tous les modules que nous connectons à [Nettoyage des données manquantes][clean-missing-data] disposent de ces mêmes informations.

Pour l’instant, nous n’avons exécuté que l’action de nettoyage dans l’expérience. Si vous souhaitez afficher le jeu de données nettoyé, cliquez sur le port de sortie gauche du module [Nettoyage des données manquantes][clean-missing-data] et sélectionnez **Visualiser**. Vous pouvez constater que la colonne **normalized-losses** n’est plus là et qu’il ne manque plus de données.

Maintenant que les données sont nettoyées, nous pouvons indiquer les fonctionnalités que nous allons utiliser dans le modèle de prévision.

## <a name="step-3-define-features"></a>Étape 3 : définition des fonctionnalités

Dans Machine Learning, les *fonctionnalités* sont des propriétés individuelles mesurables d’un élément qui vous intéresse. Dans notre jeu de données, chaque ligne représente un véhicule et chaque colonne une fonctionnalité de ce véhicule.

La recherche du jeu de fonctionnalités adéquat pour la création d’un modèle de prévision requiert certaines expériences et des connaissances sur le problème qui se pose. Certaines fonctionnalités sont mieux adaptées à la prévision que d’autres. En outre, certaines fonctionnalités ont une forte corrélation avec d’autres fonctionnalités et peuvent être supprimées. Par exemple, city-mpg et highway-mpg sont étroitement liées : nous pouvons donc conserver l’une et supprimer l’autre sans trop affecter la prédiction.

Nous allons développer un modèle utilisant un sous-ensemble de ces fonctionnalités pour notre jeu de données. Vous pouvez revenir en arrière et sélectionner d’autres fonctionnalités, relancer l’expérience et voir si vous obtenez de meilleurs résultats. Mais pour commencer, nous allons essayer les fonctionnalités suivantes :

    make, body-style, wheel-base, engine-size, horsepower, peak-rpm, highway-mpg, price


1. Faites glisser un autre module [Sélectionner des colonnes dans le jeu de données][select-columns] vers le canevas de l’expérience. Connectez le port de sortie de gauche du module [Nettoyage des données manquantes][clean-missing-data] à l’entrée du module [Sélectionner des colonnes dans le jeu de données][select-columns].

    ![Connectez le module « Sélectionner des colonnes dans le jeu de données » au module « Nettoyage des données manquantes »][connect-clean-to-select]
    <br/>
    ***Connectez le module « Sélectionner des colonnes dans le jeu de données » au module « Nettoyage des données manquantes »***

2. Double-cliquez sur le module et saisissez le commentaire suivant : « Sélection des fonctionnalités pour la prévision ».

2. Cliquez sur l’option **Lancer le sélecteur de colonne** figurant dans le volet **Propriétés**.

3. Cliquez sur **With rules**(À l’aide de règles).

4. Sous **Commencer par**, cliquez sur **Aucune colonne**. Dans la ligne de filtre, sélectionnez **Inclure** et **Noms des colonnes**, puis sélectionnez notre liste de noms de colonnes dans la zone de texte. Cela amène le module à ne pas transmettre toutes les colonnes (fonctions), à l’exception de celles que nous spécifions.

5. Cliquez sur le bouton en forme de coche (OK) pour continuer.

    ![Sélectionnez les colonnes (fonctions) à inclure dans la prédiction][select-columns-to-include]
    <br/>
    ***Sélectionnez les colonnes (fonctions) à inclure dans la prédiction***

Cela produit un jeu de données filtré qui contient uniquement les fonctionnalités que nous souhaitons transmettre à l’algorithme d’apprentissage utilisé à l’étape suivante. Plus tard, vous pouvez reprendre la procédure en utilisant une autre sélection de fonctionnalités.

## <a name="step-4-choose-and-apply-a-learning-algorithm"></a>Étape 4 : sélection et application d’un algorithme d’apprentissage

À présent que les données sont prêtes, la construction d'un modèle de prévision passe par la formation et le test. Nous allons utiliser nos données pour former le modèle, puis tester le modèle pour voir dans quelle mesure il peut prédire les prix.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

La *classification* et la *régression* sont deux types d’algorithmes de machine learning supervisé. La classification permet de prédire une réponse à partir d'un jeu de catégories défini, comme une couleur (rouge, bleu ou vert). La régression est utilisée pour prédire un nombre.

Étant donné que nous voulons prédire un prix, correspondant à un nombre, nous allons utiliser un algorithme de régression. Dans cet exemple, nous allons utiliser un modèle simple de *régression linéaire*.

> [!TIP]
> Pour en savoir plus sur les différents types d’algorithmes de machine learning et pour savoir quand les utiliser, vous pouvez visionner la première vidéo de la série Science des données pour les débutants, intitulée [The five questions data science answers](data-science-for-beginners-the-5-questions-data-science-answers.md) (Cinq questions auxquelles la science des données répond). Vous pouvez également consulter l’infographie [Principes de base du machine learning avec exemples d’algorithmes](basics-infographic-with-algorithm-examples.md), ou consulter l’[aide-mémoire d’algorithme Machine Learning](algorithm-cheat-sheet.md).

Nous formons le modèle en lui fournissant un jeu de données qui inclut le prix. Le modèle analyse les données et recherche les corrélations entre les fonctionnalités d’un véhicule automobile et son prix. Puis nous testons le modèle : nous lui affectons un ensemble de fonctionnalités pour véhicules automobiles que nous connaissons et nous étudions la précision du modèle concernant la prédiction des prix.

Nous allons utiliser nos données pour la formation et le test en les divisant en jeux de données distincts de formation et de test.

1. Sélectionnez et faites glisser le module [Fractionner les données][split] sur le canevas d’expérience et connectez-le au dernier module [Sélectionner des colonnes dans le jeu de données][select-columns].

2. Cliquez sur le module [Fractionner les données][split] pour le sélectionner. Rechercher **Fraction de lignes dans le premier jeu de données de sortie** (dans le volet **Propriétés** à droite du canevas) et attribuez-lui la valeur 0,75. Ainsi, nous allons utiliser 75 % des données pour former le modèle, et 25 % pour le tester. Par la suite, vous pourrez expérimenter d’autres pourcentages.

    ![Attribuez à la part de fractionnement du module « Fractionner les données » la valeur 0,75][set-split-data-percentage]
    <br/>
    ***Attribuez à la part de fractionnement du module « Fractionner les données » la valeur 0,75***

    > [!TIP]
    > En modifiant le paramètre **Valeur de départ aléatoire** , vous pouvez produire différents échantillons aléatoires pour la formation et le test. Ce paramètre contrôle la valeur de départ du générateur de nombres pseudo-aléatoire.

2. Exécutez l’expérience. Lors de l’expérience, les modules [Sélectionner des colonnes dans le jeu de données][select-columns] et [Fractionner les données][split] transmettent des définitions de colonne aux modules que nous allons ajouter par la suite.  

3. Pour sélectionner l’algorithme d’apprentissage, développez la catégorie **Machine Learning** dans la palette des modules, à gauche de la zone de dessin, puis développez **Initialiser le modèle**. Différentes catégories de modules s'affichent, permettant d'initialiser des algorithmes d'apprentissage automatique. Pour les besoins de cet exemple, sélectionnez le module [Régression linéaire][linear-regression] sous la catégorie **Régression**, puis faites-le glisser vers le canevas de l’expérience.
Vous pouvez également rechercher le module en tapant « régression linéaire » dans la zone de recherche de la palette.

4. Recherchez et faites glisser le module [Effectuer le traitement de données pour apprentissage du modèle][train-model] jusqu’à la zone de dessin de l’expérience. Connectez la sortie du module [Régression linéaire][linear-regression] à l’entrée de gauche du module [Former le modèle][train-model], puis connectez la sortie des données de formation (port gauche) du module [Fractionner les données][split] à l’entrée de droite du module [Former le modèle][train-model].

    ![Connectez le module « Former le modèle » aux modules « Régression linéaire » et « Fractionner les données »][connect-train-model]
    <br/>
    ***Connectez le module « Former le modèle » aux modules « Régression linéaire » et « Fractionner les données »***

5. Cliquez sur le module [Effectuer le traitement de données][train-model] pour apprentissage du modèle, cliquez sur l’option **Lancer le sélecteur de colonne** du volet **Propriétés** et sélectionnez la colonne **Price**. Il s’agit de la valeur que notre modèle va prévoir.

    Vous pouvez sélectionner la colonne **price** dans le sélecteur de colonne en la faisant passer de la liste **Colonnes disponibles** à la liste **Colonnes sélectionnées**.

    ![Sélectionnez la colonne price pour le module « Former le modèle »][select-price-column]
    <br/>
    ***Sélectionnez la colonne de prix pour le module « Former le modèle »***

6. Exécutez l’expérience.

Nous disposons à présent d’un modèle de régression formé qui permet de noter de nouvelles données automobiles pour effectuer des prédictions de prix.

![Une fois l’exécution terminée, l’expérience doit ressembler à ceci :][second-experiment-run]
<br/>
***Une fois l’exécution terminée, l’expérience doit ressembler à ceci :***

## <a name="step-5-predict-new-automobile-prices"></a>Étape 5 : prédiction des nouveaux prix des voitures

À présent que nous avons formé le modèle à l'aide de 75 % de nos données, nous pouvons l'utiliser pour la notation du reste de nos données (25 %), afin de voir s'il fonctionne correctement.

1. Recherchez et faites glisser le module [Noter le modèle][score-model] vers le canevas de l’expérience. Connectez la sortie du module [Former le modèle][train-model] au port d’entrée de gauche [Noter le modèle][score-model]. Connectez la sortie de données de test (port de droite) du module [Split Data][split] au port d’entrée de droite de [Score Model][score-model].

    ![Connectez le module « Noter le modèle » aux modules « Former le modèle » et « Fractionner les données »][connect-score-model]
    <br/>
    ***Connectez le module « Noter le modèle » aux modules « Former le modèle » et « Fractionner les données »***

2. Pour exécuter l’expérience et afficher la sortie du module [Score Model][score-model] (cliquez sur le port de sortie de [Score Model][score-model] et sélectionnez **Visualiser**). La sortie affiche les valeurs de prévision associées au prix, ainsi que les valeurs connues des données de test.  

    ![Sortie du module « Noter le modèle »][score-model-output]
    <br/>
    ***Sortie du module « Noter le modèle »***

3. Enfin, nous testons la qualité des résultats. Sélectionnez et faites glisser le module [Évaluer le modèle][evaluate-model] vers le canevas de l’expérience, puis connectez la sortie du module [Noter le modèle][score-model] à l’entrée de gauche du module [Évaluer le modèle][evaluate-model].

    > [!TIP]
    > Il existe deux ports d’entrée sur le module [Évaluer le modèle][evaluate-model], car ce dernier peut être utilisé pour comparer deux modèles. Plus tard, vous pourrez ajouter un autre algorithme à l’expérience et utiliser [Évaluer le modèle][evaluate-model] pour déterminer quel module donne les meilleurs résultats.

4. Exécutez l’expérience.

Pour afficher la sortie du module [Evaluate Model][evaluate-model], cliquez sur le port de sortie, puis sélectionnez **Visualiser**.

![Résultats de l’évaluation de l’expérience][evaluation-results]
<br/>
***Résultats de l’évaluation de l’expérience***

Les statistiques suivantes s’affichent pour notre modèle :

- **Erreur d’absolue moyenne** (EAM) : la moyenne des erreurs absolues (une *erreur* correspond à la différence entre la valeur prévue et la valeur réelle).
- **Racine de l’erreur quadratique moyenne** (RMSE) : la racine carrée de la moyenne des erreurs carrées des prévisions effectuées sur le jeu de données de test.
- **Erreur absolue relative**: la moyenne des erreurs absolues relative à la différence absolue entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
- **Erreur carrée relative**: la moyenne des erreurs carrées relative à la différence carrée entre les valeurs réelles et la moyenne de toutes les valeurs réelles.
- **Coefficient de détermination** : aussi nommé **valeur R au carré**, il s’agit d’une mesure statistique indiquant à quel point un modèle correspond aux données.

Pour chacune des statistiques liées aux erreurs, les valeurs les plus petites sont privilégiées. En effet, une valeur plus petite indique un degré de correspondance plus étroit avec la valeur réelle. Plus la valeur du **Coefficient de détermination**, est proche de un (1.0), plus la prévision est correcte.

## <a name="final-experiment"></a>Expérience finale

L’expérience finale doit ressembler à ceci :

![Expérience finale][complete-linear-regression-experiment]
<br/>
***Expérience finale***

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez terminé le premier didacticiel sur le machine learning et que vous avez configuré votre expérience, vous pouvez continuer à améliorer le modèle, puis le déployer en tant que service web prédictif.

- **Création d’une itération pour améliorer le modèle** : par exemple, vous pouvez modifier les fonctionnalités que vous utilisez dans votre prédiction. Ou vous pouvez modifier les propriétés de l'algorithme [Régression linéaire][linear-regression], ou essayer un autre algorithme. Vous pouvez même ajouter plusieurs algorithmes d'apprentissage automatique à la fois à votre expérience et comparer deux d’entre eux à l'aide du module [Evaluate Model][evaluate-model].
Pour découvrir comment comparer plusieurs modèles dans le cadre d’une expérience unique, consultez [Comparer les régresseurs](https://gallery.cortanaintelligence.com/Experiment/Compare-Regressors-5) dans la [galerie Azure AI](https://gallery.cortanaintelligence.com).

    > [!TIP]
    > Pour copier une itération de votre expérience, utilisez le bouton **ENREGISTRER SOUS** au bas de la page. Vous pouvez afficher toutes les itérations de votre expérience en cliquant sur **AFFICHER L’HISTORIQUE D’EXÉCUTION** au bas de la page. Pour en savoir plus, consultez la section [Gérer les itérations des expériences dans Microsoft Azure Machine Learning Studio][runhistory].

[runhistory]: manage-experiment-iterations.md

- **Déploiement du modèle en tant que service web prédictif** : une fois satisfait de votre modèle, vous pouvez le déployer en tant que service web permettant de prédire l’évolution des prix dans le secteur automobile, au moyen de nouvelles données. Consultez la section [Déployer un service web Microsoft Azure Machine Learning][publish] pour en savoir plus.

[publish]: publish-a-machine-learning-web-service.md

Vous souhaitez en savoir plus ? Pour obtenir un guide complet et détaillé du processus de création, formation, notation et déploiement d’un modèle, consultez [Développer une solution prédictive à l’aide d’Azure Machine Learning][walkthrough].

[walkthrough]: walkthrough-develop-predictive-solution.md

<!-- Images -->
[sign-in-to-studio]: ./media/create-experiment/sign-in-to-studio.png
[rename-experiment]: ./media/create-experiment/rename-experiment.png
[visualize-auto-data]:./media/create-experiment/visualize-auto-data.png
[select-visualize]: ./media/create-experiment/select-visualize.png
[showing-excluded-column]:./media/create-experiment/showing-excluded-column.png
[set-remove-entire-row]:./media/create-experiment/set-remove-entire-row.png
[early-experiment-run]:./media/create-experiment/early-experiment-run.png
[select-columns-to-include]:./media/create-experiment/select-columns-to-include.png
[second-experiment-run]:./media/create-experiment/second-experiment-run.png
[connect-score-model]:./media/create-experiment/connect-score-model.png
[evaluation-results]:./media/create-experiment/evaluation-results.png
[complete-linear-regression-experiment]:./media/create-experiment/complete-linear-regression-experiment.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[type-automobile]:./media/create-experiment/type-automobile.png
[type-select-columns]:./media/create-experiment/type-select-columns.png
[launch-column-selector]:./media/create-experiment/launch-column-selector.png
[add-comment]:./media/create-experiment/add-comment.png
[connect-clean-to-select]:./media/create-experiment/connect-clean-to-select.png

[set-split-data-percentage]:./media/create-experiment/set-split-data-percentage.png

<!-- temporarily switching GIFs to PNGs to remove animation --> 
[connect-train-model]:./media/create-experiment/connect-train-model.png
[select-price-column]:./media/create-experiment/select-price-column.png

[score-model-output]:./media/create-experiment/score-model-output.png

<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
