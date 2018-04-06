---
title: Didacticiel de démarrage rapide sur le langage R pour Machine Learning | Microsoft Docs
description: Utilisez ce didacticiel sur la programmation R pour prendre en main rapidement l'utilisation du langage R avec Azure Machine Learning Studio afin de créer une solution de prévision.
keywords: démarrage rapide,langage r,langage de programmation r,didacticiel de programmation en r
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 99a3a0fd-b359-481a-b236-66868deccd96
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 231d505e91fc036b30344e2fd9971db8ba2fdf05
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-tutorial-for-the-r-programming-language-for-azure-machine-learning"></a>Didacticiel de démarrage rapide pour le langage de programmation R pour Azure Machine Learning

<!-- Stephen F Elston, Ph.D. -->

## <a name="introduction"></a>Introduction
Ce didacticiel de démarrage rapide vous aide à vous lancer rapidement dans l'extension d'Azure Machine Learning à l'aide du langage de programmation R. Suivez ce didacticiel sur la programmation R pour créer, tester et exécuter du code R dans Azure Machine Learning. Au cours de ce didacticiel de démarrage rapide, vous allez créer une solution de prévision complète à l'aide du langage R dans Azure Machine Learning.  

Si Microsoft Azure Machine Learning intègre divers modules d’apprentissage automatique et de manipulation de données très efficaces, le puissant langage R a été décrit comme la lingua franca de l'analyse. Fort heureusement, l’analyse et la manipulation de données dans Azure Machine Learning peuvent être étendues grâce au langage R, qui conjugue à la fois la grande facilité de déploiement d’Azure Machine Learning avec la souplesse et la capacité d’analyse approfondie du langage R.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="forecasting-and-the-dataset"></a>Prévision et jeu de données
La prévision est une méthode analytique largement répandue et très utile. Elle peut servir à établir des prévisions de ventes d'articles saisonniers, à déterminer les niveaux de stock optimaux ou encore à établir des prévisions sur des variables macroéconomiques. La prévision s'appuie généralement sur des modèles chronologiques.

Les données chronologiques sont des données dont les valeurs ont un index chronologique. L'index chronologique peut être régulier, c'est-à-dire tous les mois ou toutes les minutes, ou irrégulier. Un modèle chronologique repose sur des données chronologiques. Le langage de programmation R offre une infrastructure souple et des analyses étendues pour les données de série chronologique.

Dans ce guide de démarrage rapide, nous allons utiliser les données de production et de tarification des produits laitiers en Californie. Ces données comportent des informations mensuelles sur la production de plusieurs produits laitiers, ainsi que le prix de la matière grasse du lait, produit de base de référence.

Les données utilisées dans cet article et les scripts R peuvent être [téléchargés ici][download]. À l’origine, ces données ont été synthétisées à partir des informations disponibles sur le site de l’Université du Wisconsin à l’adresse http://future.aae.wisc.edu/tab/production.html.

### <a name="organization"></a>Organisation
À travers plusieurs étapes successives, vous allez apprendrez à créer, tester et exécuter du code R d’analyse et de manipulation de données dans l’environnement Azure Machine Learning.  

* Dans un premier temps, nous explorerons les bases de l’utilisation du langage R dans l’environnement Azure Machine Learning Studio.
* Après quoi, nous examinerons différents aspects de l’E/S de données, du code R et des graphiques dans l’environnement Azure Machine Learning.
* Nous œuvrerons ensuite à l'élaboration de la première partie de notre solution de prévision en créant du code destiné à nettoyer et transformer les données.
* Après avoir préparé nos données, nous procéderons à l’analyse des corrélations entre plusieurs variables figurant dans notre jeu de données.
* Enfin, nous créerons un modèle de prévision chronologique saisonnier pour la production de lait.

## <a id="mlstudio"></a>Interaction avec le langage R dans Machine Learning Studio
Cette section vous fait découvrir certains concepts de base de l'interaction avec le langage de programmation R dans l'environnement Machine Learning Studio. Le langage R offre un outil efficace pour créer des modules d’analyse et de manipulation de données personnalisés au sein de l’environnement Azure Machine Learning.

Nous utiliserons RStudio pour développer, tester et déboguer du code R à petite échelle. Ce code sera ensuite coupé et collé dans un module d’exécution de script R (ou [Execute R script][execute-r-script]) dans Studio Machine Learning, prêt à être exécuté.  

### <a name="the-execute-r-script-module"></a>Module d’exécution de script R
Dans Machine Learning Studio, les scripts R sont exécutés dans le module [d’exécution de script R][execute-r-script]. Un exemple du module [d’exécution de script R][execute-r-script] dans Machine Learning Studio est illustré dans la figure 1.

 ![Langage de programmation R : module Execute R Script sélectionné dans Machine Learning Studio][1]

*Figure 1 : environnement Machine Learning Studio avec le module d’exécution de script R sélectionné.*

Dans la figure 1, examinons les principaux éléments de l’environnement Machine Learning Studio permettant de travailler avec le module [d’exécution de script R][execute-r-script].

* Les modules utilisés dans l'expérimentation se trouvent dans le volet central.
* La partie supérieure du volet droit contient une fenêtre permettant d'afficher et de modifier les scripts R.  
* La partie inférieure du volet droit présente certaines propriétés du module [d’exécution de script R][execute-r-script]. Vous pouvez afficher les journaux d'erreurs et de sortie en cliquant aux endroits appropriés de ce volet.

Bien entendu, nous reviendrons plus en détail sur le module [d’exécution de script R][execute-r-script] au fil du présent document.

Lorsqu'il s'agit d'utiliser des fonctions R complexes, je vous recommande de modifier, tester et déboguer dans RStudio. Comme pour tout projet de développement logiciel, développez votre code graduellement et testez-le dans un petit cas de test simple. Ensuite, coupez vos fonctions et collez-les dans la fenêtre de script R du module [d’exécution de script R][execute-r-script]. Cette approche vous permet d’exploiter l’environnement de développement intégré (IDE) de RStudio et la puissance d’Azure Machine Learning.  

#### <a name="execute-r-code"></a>Exécution du code R
Pour exécuter du code R dans le module [d’exécution de script R][execute-r-script], il suffit d’exécuter l’expérimentation en cliquant sur le bouton **Run** (Exécuter). Une fois l’exécution terminée, une coche apparaît sur l’icône du module [d’exécution de script R][execute-r-script].

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Codage R défensif pour Azure Machine Learning
Si vous développez du code R pour, par exemple, un service Web à l’aide d’Azure Machine Learning, vous devez absolument prévoir la façon dont votre code traitera une entrée de données inattendue et les exceptions. Dans un souci de clarté, j'ai évité de surcharger les exemples présentés dans ce document de code relevant de la vérification et de la gestion des exceptions. Cependant, je vous montrerai par la suite plusieurs exemples de fonctions qui font appel à la fonctionnalité de gestion des exceptions du langage R.  

Si vous avez besoin d’un exposé plus complet sur la gestion des exceptions R, je vous recommande de lire les sections appropriées du livre de Hadley Wickham cité en référence dans l’ [Annexe B – Informations supplémentaires](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio"></a>Débogage et test du langage R dans Machine Learning Studio
Encore une fois, je vous recommande de tester et de déboguer votre code R à petite échelle dans RStudio. Cependant, dans certains cas, vous devrez analyser les problèmes de code R dans le module [d’exécution de script R][execute-r-script] lui-même. De plus, vous aurez tout intérêt à vérifier les résultats dans Machine Learning Studio.

La sortie résultant de l’exécution de votre code R sur la plateforme Azure Machine Learning se trouve principalement dans le fichier output.log. Vous pourrez trouver des informations complémentaires dans le fichier error.log.  

Si une erreur se produit dans Machine Learning Studio pendant l’exécution de votre code R, vous devez dans un premier temps examiner le fichier error.log. Ce fichier peut contenir des messages d’erreur utiles susceptibles de vous aider à cerner l’erreur et à la corriger. Pour afficher le fichier error.log, cliquez sur **View error log** (Afficher le journal d’erreurs) dans le **volet de propriétés** du module [d’exécution de script R][execute-r-script] contenant l’erreur.

Par exemple, j’ai exécuté le code R ci-dessous, avec une variable y non définie, dans un module [d’exécution de script R][execute-r-script] :

    x <- 1.0
    z <- x + y

L'exécution de ce code échoue, ce qui se traduit par une condition d'erreur. En cliquant sur **Afficher le journal des erreurs** dans le **volet des propriétés**, nous obtenons l’affichage présenté à la figure 2.

  ![fenêtre contextuelle de message d’erreur][2]

*Figure 2 : fenêtre contextuelle de message d’erreur.*

Il apparaît ici nécessaire de regarder dans le fichier output.log pour consulter le message d’erreur R. Il convient pour cela de cliquer sur le module [d’exécution de script R][execute-r-script] et sur l’élément **View output.log** (Afficher output.log) dans le **volet de propriétés** à droite. Dans la nouvelle fenêtre de navigateur qui s’ouvre, voici les informations qui s’affichent :

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Ce message d'erreur ne réserve aucune surprise et identifie clairement le problème.

Pour inspecter la valeur d’un objet dans le code R, vous pouvez imprimer les valeurs dans le fichier output.log. Les règles pour examiner les valeurs d'objets sont, pour l'essentiel, identiques à celles d'une session R interactive. Par exemple, si vous tapez un nom de variable sur une ligne, la valeur de l'objet s'imprime dans le fichier output.log.  

#### <a name="packages-in-machine-learning-studio"></a>Packages dans Azure Machine Learning Studio
Azure Machine Learning est fourni avec plus de 350 packages de langage R préinstallés. Vous pouvez utiliser le code ci-dessous dans le module [d’exécution de script R][execute-r-script] pour extraire la liste des packages préinstallés.

    data.set <- data.frame(installed.packages())
    maml.mapOutputPort("data.set")

Si, pour l’heure, vous ne comprenez pas la dernière ligne de ce code, poursuivez la lecture de ce document. Vous trouverez, dans le reste du présent document, un exposé complet sur l’utilisation du langage R dans l’environnement Azure Machine Learning.

### <a name="introduction-to-rstudio"></a>Présentation de RStudio
RStudio est un environnement de développement intégré (IDE) pour R couramment utilisé. C’est l’outil que j’utiliserai pour modifier, tester et déboguer une partie du code R utilisé dans ce guide de démarrage rapide. Une fois le code R testé et prêt, il suffit de le couper dans l’éditeur RStudio et de le coller dans un module [d’exécution de script R][execute-r-script] de Machine Learning Studio.  

Si vous n'avez pas encore installé le langage de programmation R sur votre ordinateur de bureau, je vous conseille de le faire dès maintenant. Des versions open source du langage R sont disponibles en téléchargement gratuit dans la section CRAN (Comprehensive R Archive Network) du site [http://www.r-project.org/](http://www.r-project.org/). Il existe des versions Windows, Mac OS et Linux/UNIX. Choisissez un site miroir voisin et suivez les instructions de téléchargement. Par ailleurs, le section CRAN contient de nombreux packages d'analyse et de manipulation de données fort utiles.

Si vous débutez avec RStudio, téléchargez et installez la version pour ordinateur de bureau. Les versions Windows, Mac OS et Linux/UNIX de RStudio sont disponibles en téléchargement à l’adresse http://www.rstudio.com/products/RStudio/. Suivez les instructions qui vous sont fournies pour installer RStudio sur votre ordinateur de bureau.  

Un didacticiel de présentation sur RStudio est disponible à l’adresse https://support.rstudio.com/hc/sections/200107586-Using-RStudio.

Vous trouverez des informations complémentaires sur l’utilisation de RStudio dans [l’Annexe A][appendixa].  

## <a id="scriptmodule"></a>Obtention de données dans et hors du module d'exécution de script R
Dans cette section, nous allons voir comment obtenir des données dans et hors du module [d’exécution de script R][execute-r-script]. Nous examinerons les façons de gérer les différents types de données lus dans et hors du module [d’exécution de script R][execute-r-script].

Le code complet de cette section se trouve dans le fichier zip téléchargé précédemment.

### <a name="load-and-check-data-in-machine-learning-studio"></a>Chargement et vérification des données dans Machine Learning Studio
#### <a id="loading"></a>Chargement du jeu de données
Nous allons commencer par charger le fichier **csdairydata.csv** dans Azure Machine Learning Studio.

* Démarrez votre environnement Azure Machine Learning Studio.
* Cliquez sur **+ NEW** en bas à gauche de votre écran, puis sélectionnez **Jeu de données**.
* Sélectionnez **From Local File** (Depuis un fichier local), puis **Browse** (Parcourir) pour sélectionner le fichier.
* Veillez à sélectionner **Generic CSV file with header (.csv)** (Fichier CSV générique avec en-tête) comme type de jeu de données.
* Cliquez sur la coche.
* Une fois le jeu de données téléchargé, vous devez voir le nouveau jeu de données en cliquant sur l’onglet **Datasets** (Jeux de données).  

#### <a name="create-an-experiment"></a>Création d'une expérience
Maintenant que Machine Learning Studio contient des données, nous devons créer une expérimentation pour faire l’analyse.  

* Cliquez sur **+ NEW** en bas à gauche, sélectionnez **Experiment** (Expérimentation), puis **Blank Experiment** (Expérimentation à blanc).
* Vous pouvez nommer votre expérimentation en sélectionnant et en modifiant le titre **Experiment created on...** (Expérimentation créée sur) en haut de la page. Vous pouvez par exemple le modifier en **CA Dairy Analysis**.
* Sur la gauche de la page d’expérimentation, développez **Saved Datasets** (Jeux de données enregistrés), puis **My Datasets** (Mes jeux de données). Vous devez voir le fichier **cadairydata.csv** que vous avez téléchargé précédemment.
* Glissez-déplacez le **jeu de données csdairydata.csv** vers l’expérimentation.
* Dans la zone **Search experiment items** (Rechercher dans les éléments de l’expérimentation) en haut du volet gauche, tapez [Execute R Script][execute-r-script] (Exécution de script R). Le module s'affiche alors dans la liste de recherche.
* Glissez-déplacez le module [d’exécution de script R][execute-r-script] vers votre palette.  
* Reliez la sortie du **jeu de données csdairydata.csv** à la première entrée (**Jeu de données 1**) du module [d’exécution de script R][execute-r-script] en partant de la gauche.
* **N’oubliez pas de cliquer sur « Save » (Enregistrer) !**  

À ce stade, votre expérimentation doit être similaire à la figure 3.

![l’expérimentation CA Dairy Analysis avec le jeu de données et le module d’exécution de script R][3]

*Figure 3 : l’expérimentation CA Dairy Analysis avec le jeu de données et le module d’exécution de script R.*

#### <a name="check-on-the-data"></a>Vérification des données
Examinons les données que nous avons chargées dans l'expérimentation. Dans l’expérimentation, cliquez sur la sortie du **jeu de données cadairydata.csv**, puis sélectionnez **Visualize** (Visualiser). Vous obtenez un résultat analogue à la figure 4.  

![aperçu du jeu de données cadairydata.csv][4]

*Figure 4 : aperçu du jeu de données cadairydata.csv.*

Cette vue contient de nombreuses informations utiles. Les premières lignes de ce jeu de données apparaissent également. Si nous sélectionnons une colonne, la section des statistiques affiche plus d'informations sur cette colonne. Par exemple, la ligne Feature Type indique les types de données qui sont affectés à la colonne par Azure Machine Learning Studio. Il est judicieux d'effectuer un contrôle rapide de ce type avant de se lancer dans un travail sérieux.

### <a name="first-r-script"></a>Premier script R
Créons un premier script R simple pour l’expérimenter dans Azure Machine Learning Studio. J’ai créé et testé le script suivant dans RStudio :  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    str(cadairydata)
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Je dois maintenant transférer ce script dans Azure Machine Learning Studio. Je pourrais simplement effectuer un couper-coller. Mais je vais ici transférer le script R via un fichier zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Entrée de données dans le module d'exécution de script R
Penchons-nous sur les entrées du module [d’exécution de script R][execute-r-script]. En l’occurrence, nous allons examiner les données du secteur laitier californien dans le module [d’exécution de script R][execute-r-script].  

Il existe trois types d’entrée possibles pour le module [d’exécution de script R][execute-r-script]. Selon votre application, vous pouvez utiliser une ou toutes ces entrées. Il est aussi tout à fait envisageable d’utiliser un script R qui ne prenne aucune entrée.  

Passons en revue chacune de ces entrées, de gauche à droite. Pour afficher le nom d’une entrée, il suffit de placer le curseur sur l’entrée en question et de lire l’infobulle.  

#### <a name="script-bundle"></a>Script groupé
L’entrée de script groupé permet de transmettre le contenu d’un fichier zip au module [d’exécution de script R][execute-r-script]. Vous pouvez utiliser l’une des commandes suivantes pour lire le contenu du fichier zip dans votre code R :

    source("src/yourfile.R") # Reads a zipped R script
    load("src/yourData.rdata") # Reads a zipped R data file

> [!NOTE]
> Azure Machine Learning traite les fichiers contenus dans le zip comme s’ils se trouvaient dans le répertoire src/. Il est donc nécessaire de faire précéder les noms des fichiers du nom de ce répertoire. Par exemple, si le fichier zip contient les fichiers `yourfile.R` et `yourData.rdata` à sa racine, vous devez les traiter en tant que `src/yourfile.R` et `src/yourData.rdata` quand vous utilisez `source` et `load`.
> 
> 

Nous avons déjà abordé la question du chargement des jeux de données dans la section [Chargement du jeu de données](#loading). Dès que vous avez créé et testé le script R présenté dans la section précédente, procédez comme suit :

1. Enregistrez le script R dans un fichier .R. J'appelle mon fichier de script « simpleplot.R ». Voici le contenu.
   
        ## Only one of the following two lines should be used
        ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
        ## If in RStudio, use the second line with read.csv()
        cadairydata <- maml.mapInputPort(1)
        # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
        str(cadairydata)
        pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
        ## The following line should be executed only when running in
        ## Azure Machine Learning Studio
        maml.mapOutputPort('cadairydata')
2. Créez un fichier zip et copiez votre script dans ce fichier. Dans Windows, vous pouvez cliquer avec le bouton droit sur le fichier, sélectionner **Envoyer vers**, puis **Dossier compressé**. Vous créez ainsi un fichier zip contenant le fichier « simpleplot.R ».
3. Ajoutez ce dernier aux **jeux de données** dans Machine Learning Studio, en spécifiant le type **zip**. Le fichier zip doit dès lors figurer dans vos jeux de données.
4. Glissez-déplacez le fichier zip des **jeux de données** vers le **canevas ML Studio**.
5. Reliez la sortie de l’icône des **données zip** à l’entrée de **script groupé** du module [d’exécution de script R][execute-r-script].
6. Tapez la fonction `source()` avec le nom de votre fichier zip dans la fenêtre de code du module [d’exécution de script R][execute-r-script]. Dans mon cas, j’ai saisi `source("src/simpleplot.R")`.  
7. Pensez à cliquer sur **Save**(Enregistrer).

Une fois ces étapes effectuées, le module [d’exécution de script R][execute-r-script] exécute le script R du fichier zip quand l’expérimentation est exécutée. À ce stade, votre expérimentation doit être similaire à la figure 5.

![expérimentation utilisant le script R compressé][6]

*Figure 5 : expérimentation utilisant le script R compressé.*

#### <a name="dataset1"></a>Jeu de données 1
Vous pouvez transmettre une table de données rectangulaire à votre code R utilisant l’entrée Jeu de données 1. Dans notre script simple, la fonction `maml.mapInputPort(1)` lit les données du port 1. Ces données sont ensuite affectées à un nom de variable de tableau de données (« dataframe ») dans votre code. Dans notre script simple, la première ligne de code mène à bien l'affectation.

    cadairydata <- maml.mapInputPort(1)

Exécutez votre expérimentation en cliquant sur le bouton **Run** (Exécuter). À la fin de l’exécution, cliquez sur le module [d’exécution de script R][execute-r-script], puis sur **View output log** (Afficher le journal de sortie) dans le volet de propriétés. Une nouvelle page doit s’ouvrir dans votre navigateur en affichant le contenu du fichier output.log. Si vous faites défiler l’écran vers le bas, vous devez voir quelque chose de similaire à ceci :

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Plus bas dans la page, vous trouverez des informations détaillées sur les colonnes, qui ressemblent à ce qui suit.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput]
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput]
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput]
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput]
    [ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
    [ModuleOutput]
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput]
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput]
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput]
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...

Pour l'essentiel, ces résultats sont conformes aux attentes, avec 228 observations et 9 colonnes dans le tableau de données. Nous pouvons voir les noms de colonnes, le type de données R et un exemple de chaque colonne.

> [!NOTE]
> Pratique, cette même sortie imprimée est disponible depuis la sortie de l’appareil R du module [d’exécution de script R][execute-r-script]. Nous évoquerons les sorties du module [d’exécution de script R][execute-r-script] dans la section suivante.  
> 
> 

#### <a name="dataset2"></a>Jeu de données 2
Le comportement de l'entrée Jeu de données 2 est identique à Jeu de données 1. Avec cette entrée, vous pouvez transmettre une deuxième table de données rectangulaire à votre code R. Pour transmettre ces données, la fonction `maml.mapInputPort(2)`est utilisée avec l’argument 2.  

### <a name="execute-r-script-outputs"></a>Sorties du module d'exécution de script R
#### <a name="output-a-dataframe"></a>Sortie d'un tableau de données
Vous pouvez sortir le contenu d’un tableau de données R sous forme de table rectangulaire via le port du Jeu de données 1 de résultat en utilisant la fonction `maml.mapOutputPort()` . Dans notre script R simple, cette opération est exécutée par la ligne suivante :

    maml.mapOutputPort('cadairydata')

Après avoir exécuté l’expérimentation, cliquez sur le port de sortie du Jeu de données 1 de résultat, puis sur **Visualize**(Visualiser). Vous obtenez un résultat analogue à la figure 6.

![visualisation de la sortie des données du secteur laitier californien][7]

*Figure 6 : visualisation de la sortie des données du secteur laitier californien.*

Cette sortie est identique à l'entrée, exactement comme nous l'attendions.  

### <a name="r-device-output"></a>Sortie de l’appareil R
La sortie de l’appareil du module [d’exécution de script R][execute-r-script] contient des messages et une sortie graphique. La sortie et les messages d'erreur standard de R sont envoyés au port de sortie de l’appareil R.  

Pour afficher la sortie de l’appareil R, cliquez sur le port, puis sur **Visualize**(Visualiser). La sortie et l'erreur standard du script R sont présentées dans la figure 7.

![sortie et erreur standard du port de l’appareil R][8]

*Figure 7 : sortie et erreur standard du port de l’appareil R.*

En faisant défiler l'écran vers le bas, nous voyons la sortie graphique de notre script R, qui est présentée dans la figure 8.  

![sortie graphique du port de l’appareil R][9]

*Figure 8 : sortie graphique du port d’appareil R.*  

## <a id="filtering"></a>Filtrage et transformation des données
Dans cette section, nous allons effectuer certaines opérations de filtrage et de transformation de données de base sur les données du secteur laitier californien. À la fin de cette section, les données seront dans un format idoine pour générer un modèle d'analyse.  

Plus particulièrement, nous allons effectuer dans cette section plusieurs tâches courantes de nettoyage et de transformation de données ; de transformation de types, de filtrage sur des tableaux de données, d’ajout de nouvelles colonnes calculées et de transformation de valeurs. Cette expérience vous aidera à faire face aux diverses cas de figure rencontrés dans les problèmes réels.

Le code R complet de cette section est disponible dans le fichier zip que vous avez téléchargé précédemment.

### <a name="type-transformations"></a>Transformations de types
Maintenant que nous pouvons lire les données du secteur laitier californien dans le code R du module [d’exécution de script R][execute-r-script], nous devons nous assurer que les données contenues dans les colonnes ont un type et un format appropriés.  

Le langage R est dynamiquement typé, ce qui signifie que les types de données sont convertis de force d'un type vers un autre, selon les besoins. Le langage R compte les types de données atomiques suivants : numérique, logique et caractère. Le type facteur est utilisé pour stocker de manière compacte les données relatives aux catégories. Vous trouverez des informations bien plus complètes sur les types de données dans les références fournies dans l’ [Annexe B - Informations supplémentaires](#appendixb).

Quand des données tabulaires sont lues dans du code R à partir d'une source externe, il est toujours judicieux de vérifier les types résultants dans les colonnes. Vous pouvez espérer obtenir une colonne de type caractère, mais dans de nombreux cas, elle se présentera sous forme de facteur ou inversement. Dans d’autres cas, une colonne qui selon vous devrait être de type numérique pourra être représentée par des données caractères, par exemple, « 1,23 » au lieu du nombre à virgule flottante 1,23.  

Heureusement, il est facile de convertir un type dans un autre, dans la mesure où le mappage est possible. Par exemple, vous ne pouvez pas convertir « Nevada » en valeur numérique, mais vous pouvez la convertir en facteur (variable catégorique). De même, vous pouvez convertir la valeur numérique 1 en caractère « 1 » ou en facteur.  

La syntaxe utilisée pour ces conversions est simple : `as.datatype()`. Ces fonctions de conversion de type sont les suivantes :

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

En examinant les types des données affichées dans les colonnes que nous avons entrées à la section précédente, nous constatons que toutes les colonnes sont de type numérique, à l’exception de la colonne intitulée « Month », qui est de type caractère. Convertissons-la en facteur et vérifions les résultats.  

J’ai supprimé la ligne qui a créé la matrice de nuage de points et ajouté une ligne convertissant la colonne « Month » en facteur. Dans mon expérimentation, je vais simplement couper et coller le code R dans la fenêtre de code du module [d’exécution de script R][execute-r-script]. Une autre possibilité aurait été de mettre à jour le fichier zip et de le télécharger dans Azure Machine Learning Studio, mais cette opération nécessite plusieurs étapes.  

    ## Only one of the following two lines should be used
    ## If running in Machine Learning Studio, use the first line with maml.mapInputPort()
    ## If in RStudio, use the second line with read.csv()
    cadairydata <- maml.mapInputPort(1)
    # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(cadairydata$Month)
    str(cadairydata) # Check the result
    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('cadairydata')

Exécutons ce code et examinons le journal de sortie pour le script R. Les données pertinentes du journal sont affichées dans la figure 9.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figure 9 : aperçu du tableau de données avec une variable facteur.*

Le type de la colonne Month doit à présent indiquer «**Factor w/ 14 levels**» (Facteur à 14 niveaux). C'est un problème, car une année ne compte que 12 mois. Vous pouvez aussi vérifier que le type qui apparaît dans la **visualisation** du port du jeu de données de résultat est **Categorical** (Catégorique).

Le problème, c'est que la colonne « Month » n'a pas été codée de façon systématique. Le nom du mois sera dans certains cas affiché en toutes lettres (avril) et dans d’autres, il sera abrégé (avr.). Ce problème peut être résolu en limitant la chaîne à 3 caractères. La ligne de code se présente désormais comme suit :

    ## Ensure the coding is consistent and convert column to a factor
    cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))

Relancez l'expérimentation et affichez le journal de sortie. Les résultats attendus sont présentés dans la figure 10.  

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figure 10 : aperçu du tableau de données avec le nombre correct de niveaux de facteur.*

Notre variable facteur possède désormais les 12 niveaux souhaités.

### <a name="basic-data-frame-filtering"></a>Filtrage de base des tableaux de données
Les tableaux de données R prennent en charge des fonctionnalités de filtrage efficaces. Il est possible de créer des sous-ensembles de jeux de données en appliquant des filtres logiques aux lignes ou colonnes. Dans de nombreux cas, des critères de filtre complexes sont nécessaires. Parmi les références indiquées dans l’ [annexe B - informations supplémentaires](#appendixb) figurent des exemples détaillés de filtrage de tableaux de données.  

Notre jeu de données a besoin d'être filtré. En effet, si l’on examine les colonnes du tableau de données cadiarydata, on constate qu’il y a deux colonnes inutiles. La première colonne contient seulement un numéro de ligne, ce qui n'est pas très utile. La deuxième colonne, Year.Month, contient des informations redondantes. Nous pouvons facilement exclure ces colonnes à l’aide du code R suivant.

> [!NOTE]
> À partir de maintenant, dans cette section, je ne vous montrerai que le code supplémentaire que j’ajoute dans le module [d’exécution de script R][execute-r-script]. Chaque nouvelle ligne sera ajoutée **avant** la fonction `str()`. J’utilise cette fonction pour vérifier les résultats dans Azure Machine Learning Studio.
> 
> 

J’ajoute la ligne suivante à mon code R dans le module [d’exécution de script R][execute-r-script].

    # Remove two columns we do not need
    cadairydata <- cadairydata[, c(-1, -2)]

Exécutez ce code dans votre expérimentation et vérifiez le résultat dans le journal de sortie. Ces résultats sont présentés dans la figure 11.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  7 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figure 11 : aperçu du tableau de données après la suppression des deux colonnes.*

Bonne nouvelle ! Les résultats obtenus sont conformes aux attentes.

### <a name="add-a-new-column"></a>Ajout d'une nouvelle colonne
Pour créer des modèles chronologiques, il va nous être utile de disposer d'une colonne contenant les mois depuis le début de la série chronologique. À cet effet, nous allons créer la colonne « Month.Count ».

Pour une meilleure organisation du code, nous allons créer notre première fonction simple : `num.month()`. Nous appliquerons ensuite cette fonction de façon à créer une colonne dans le tableau de données. Le nouveau code se présente comme suit :

    ## Create a new column with the month count
    ## Function to find the number of months from the first
    ## month of the time series
    num.month <- function(Year, Month) {
      ## Find the starting year
      min.year  <- min(Year)

      ## Compute the number of months from the start of the time series
      12 * (Year - min.year) + Month - 1
    }

    ## Compute the new column for the dataframe
    cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)

À présent, exécutez l'expérimentation mise à jour et utilisez le journal de sortie pour afficher les résultats. Ces résultats sont présentés dans la figure 12.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figure 12 : aperçu du tableau de données avec la colonne supplémentaire.*

Tout semble fonctionner. Nous disposons de la nouvelle colonne et le tableau de données contient les valeurs attendues.

### <a name="value-transformations"></a>Transformations de valeurs
Dans cette section, nous allons effectuer quelques transformations simples sur les valeurs de certaines colonnes du tableau de données. Le langage R prend en charge les transformations de valeurs presque arbitraires. Parmi les références indiquées dans l’ [annexe B - informations supplémentaires](#appendixb) figurent des exemples détaillés.

Si on examine les valeurs contenues dans les aperçus du tableau de données, on note certaines incohérences. Par exemple, produit-on vraiment plus de crème glacée que de lait en Californie ? Bien sûr que non, au grand dam peut-être des amateurs de glaces. Les unités sont différentes. Le prix est exprimé en unités de livres américaines, le lait en unités de 1 M de livres américaines, la crème glacée en unités de 1 000 gallons américains et le fromage blanc en unités de 1 000 livres américaines. En supposant que le poids de la crème glacée est d’environ 6,5 livres par gallon, nous pouvons facilement effectuer la conversion en multipliant les valeurs pour qu’elles soient toutes exprimées dans les mêmes unités de 1 000 livres.

Pour notre modèle de prévision, nous utilisons un modèle multiplicatif de façon à ajuster ces données en fonction des variations saisonnières et tendancielles. Une transformation logarithmique nous permet d'utiliser un modèle linéaire, ce qui simplifie ce processus. Nous pouvons appliquer la transformation logarithmique dans la fonction où le multiplicateur est appliqué.

Dans le code suivant, je définis une nouvelle fonction, `log.transform()`, et l’applique aux lignes contenant les valeurs numériques. La fonction R `Map()` est utilisée pour appliquer la fonction `log.transform()` aux colonnes sélectionnées du tableau de données. Si `Map()` s’apparente à `apply()`, cette fonction accepte cependant plusieurs listes d’arguments. À noter qu’une liste de multiplicateurs fournit le deuxième argument à la fonction `log.transform()` . La fonction `na.omit()` est ici utilisée à des fins de nettoyage pour s’assurer qu’il n’y a pas de valeurs manquantes ou non définies dans le tableau de données.

    log.transform <- function(invec, multiplier = 1) {
      ## Function for the transformation, which is the log
      ## of the input value times a multiplier

      warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                           "ERROR: Arguments to function log.transform must be greate than zero",
                           "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                           "ERROR: Invalid time seies value encountered in function log.transform"
                           )

      ## Check the input arguments
      if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
      if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
      if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

      ## Wrap the multiplication in tryCatch
      ## If there is an exception, print the warningmessage to
      ## standard error and return NA
      tryCatch(log(multiplier * invec),
               error = function(e){warning(warningmessages[4]); NA})
    }


    ## Apply the transformation function to the 4 columns
    ## of the dataframe with production data
    multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
    cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

    ## Get rid of any rows with NA values
    cadairydata <- na.omit(cadairydata)  

Il se passe pas mal de choses dans la fonction `log.transform()` . La majeure partie de ce code recherche des problèmes potentiels au niveau des arguments ou gère les exceptions qui peuvent toujours survenir pendant les calculs. En fait, seules quelques lignes de ce code effectuent les calculs.

La programmation défensive vise à éviter l’échec d’une seule fonction, ce qui empêcherait la poursuite du traitement. L’échec soudain d’une analyse au long cours peut s’avérer très contrariant pour les utilisateurs. Pour éviter cette situation, il convient de choisir des valeurs de retour par défaut qui limiteront les nuisances à un traitement en aval. Un message est également généré pour avertir les utilisateurs qu'un problème s'est produit.

Si vous n’êtes pas rompu à la programmation défensive en langage R, tout ce code peut vous paraître insurmontable. Je vous accompagnerai tout au long des étapes principales :

1. Un vecteur de quatre messages est défini. Ces messages servent à communiquer des informations sur certaines erreurs et exceptions qui peuvent se produire avec ce code.
2. Dans chaque cas, la valeur NA est renvoyée. Il existe de nombreuses autres possibilités susceptibles de produire moins d’effets secondaires. Je pourrais renvoyer un vecteur de zéros ou le vecteur d'entrée d'origine, par exemple.
3. Les arguments de la fonction font l'objet de vérifications. Dans chaque cas, si une erreur est détectée, une valeur par défaut est renvoyée et un message est généré par la fonction `warning()`. La fonction `warning()` est ici préférée à la fonction `stop()`, car celle-ci met fin à l’exécution, exactement ce que nous voulons éviter. À noter que j'ai écrit ce code dans un style procédural, car dans ce cas, une approche fonctionnelle paraissait complexe et obscure.
4. Les calculs logarithmiques sont circonscrits à `tryCatch()` pour éviter que les exceptions n’entraînent un arrêt soudain du traitement. Sans `tryCatch()` , la plupart des erreurs déclenchées par les fonctions R engendrent un signal d’arrêt, qui est suivi d’un arrêt.

Exécutez ce code R dans votre expérimentation et examinez la sortie imprimée dans le fichier output.log. Les valeurs transformées des quatre colonnes sont visibles apparaissent dans le journal, comme l’illustre la figure 13.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving variable  cadairydata  ..."
    [ModuleOutput] 
    [ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"

*Figure 13 : aperçu des valeurs transformées dans le tableau de données.*

Comme nous pouvons le constater, les valeurs ont été transformées. À présent, la production de lait dépasse nettement la production de tous les autres produits laitiers, ce qui nous rappelle que nous avons maintenant sous nos yeux une échelle logarithmique.

À ce stade, nos données sont nettoyées et nous sommes prêts à procéder à une modélisation. Au vu de l’aperçu de visualisation de la sortie du jeu de données de résultat du module [d’exécution de script R][execute-r-script], vous pouvez constater que la colonne « Month » est de type « catégorique » et qu’elle contient 12 valeurs uniques, là encore comme prévu.

## <a id="timeseries"></a>Objets de série chronologique et analyse des corrélations
Dans cette section, nous allons explorer quelques objets de série chronologique R de base et analyser les corrélations entre certaines variables. Notre objectif est ici de sortir un tableau de données contenant les informations de corrélation par paire à plusieurs décalages.

Le code complet de cette section se trouve dans le fichier zip que vous avez téléchargé précédemment.

### <a name="time-series-objects-in-r"></a>Objets de série chronologique en langage R
Comme nous l'avons déjà vu, une série chronologique est une série de valeurs de données indexées par le temps. Les objets de série chronologique R servent à créer et gérer l'index chronologique. Il y a plusieurs avantages à utiliser des objets de série chronologique. Les objets de série chronologique vous affranchissent de nombreuses tâches de gestion qu’imposent les valeurs d’index chronologique qui sont encapsulées dans les objets. De plus, ils vous permettent d'utiliser les diverses méthodes associées aux séries chronologiques, comme le traçage, l'impression, la modélisation, etc.

La classe de série chronologique POSIXct est couramment utilisée et relativement simple. Cette classe de série chronologique mesure le temps à partir du début de l'époque, soit le 1er janvier 1970. Nous utiliserons dans cet exemple des objets de série chronologique POSIXct. Parmi les autres classes d'objet de série chronologique R souvent utilisées, citons zoo et xts, qui concernent les séries chronologiques extensibles.
<!-- Additional information on R time series objects is provided in the references in Section 5.7. [commenting because this section doesn't exist, even in the original] -->

### <a name="time-series-object-example"></a>Exemple d’objet de série chronologique
Commençons notre exemple. Glissez-déplacez un **nouveau** module [d’exécution de script R][execute-r-script] dans votre expérimentation. Reliez le port de sortie du jeu de données 1 de résultat du module [d’exécution de script R][execute-r-script] existant au port d’entrée du jeu de données 1 du nouveau module [d’exécution de script R][execute-r-script].

Comme dans les premiers exemples, à mesure que nous avancerons dans l’exemple, je ne vous montrerai que les lignes de code R supplémentaires ajoutées à certains points de chaque étape.  

#### <a name="reading-the-dataframe"></a>Lecture du tableau de données
Pour commencer, lisons un tableau de données et vérifions que nous obtenons les résultats attendus. Le code suivant devrait le permettre :

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)
    str(cadairydata) # Check the results

À présent, exécutez l'expérimentation. Le journal de la nouvelle forme d'exécution du script R devrait ressembler à la figure 14.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  8 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...

*Figure 14 : aperçu du tableau de données dans le module d’exécution de script R.*

Ces données présentent le type et le format prévus. Notez que la colonne « Month » est de type facteur et qu'elle affiche le nombre de niveaux attendu.

#### <a name="creating-a-time-series-object"></a>Création d’un objet de série chronologique
Nous devons ajouter un objet de série chronologique au tableau de données. Remplacez le code actuel par le suivant, qui ajoute une nouvelle colonne de classe POSIXct.

    # Comment the following if using RStudio
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata) # Check the results

Maintenant, consultez le journal. Elle doit être similaire à la figure 15.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figure 15 : aperçu du tableau de données avec un objet de série chronologique.*

Comme le montre l'aperçu, la nouvelle colonne appartient en réalité à la classe POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Exploration et transformation des données
Examinons certaines variables de ce jeu de données. Une matrice de nuage de points offre un bon moyen d'obtenir un aperçu rapide. Je remplace la fonction `str()` du code R précédent par la ligne suivante :

    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")

Exécutez ce code et regardez ce qu'il se passe. Le graphique généré au niveau du port de l’appareil R doit être similaire à la figure 16.

![matrice de nuage de points des variables sélectionnées][17]

*Figure 16 : matrice de nuage de points des variables sélectionnées.*

Les relations entre ces variables sont structurées de manière inhabituelle. Peut-être est-ce lié aux tendances des données et au fait que nous n'avons pas standardisé les variables.

### <a name="correlation-analysis"></a>Analyse des corrélations
Pour procéder à l'analyse des corrélations, nous devons éliminer la tendance et standardiser les variables. Nous aurions pu nous contenter d’utiliser la fonction R `scale()` , qui centre et dimensionne les variables. D'autant que son exécution peut même s'avérer plus rapide. Or, mon intention est de vous montrer un exemple de programmation défensive en langage R.

La fonction `ts.detrend()` présentée ci-dessous effectue ces deux opérations. Les deux lignes de code suivantes permettent d'éliminer la tendance des données et de standardiser les valeurs.

    ts.detrend <- function(ts, Time, min.length = 3){
      ## Function to de-trend and standardize a time series

      ## Define some messages if they are NULL  
      messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                    'ERROR: ts.detrend requires argument ts to be of type numeric',
                    paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                    'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                    'ERROR: Detrend regression has failed in ts.detrend',
                    'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
      )
      # Create a vector of zeros to return as a default in some cases
      zerovec  <- rep(length(ts), 0.0)

      # The input arguments are not of the same length, return ts and quit
      if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

      # If the ts is not numeric, just return a zero vector and quit
      if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

      # If the ts is too short, just return it and quit
      if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

      ## Check that the Time variable is of class POSIXct
      if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

      ## De-trend the time series by using a linear model
      ts.frame  <- data.frame(ts = ts, Time = Time)
      tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
               error = function(e){warning(messages[5]); zerovec})

      tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
                 ts <- ts/stdev},
                error = function(e){warning(messages[6]); zerovec})

      ts
    }  
    ## Apply the detrend.ts function to the variables of interest
    df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

    ## Plot the results to look at the relationships
    pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")

Il se passe pas mal de choses dans la fonction `ts.detrend()` . La majeure partie de ce code recherche des problèmes potentiels au niveau des arguments ou gère les exceptions qui peuvent toujours survenir pendant les calculs. En fait, seules quelques lignes de ce code effectuent les calculs.

Nous avons déjà vu un exemple de programmation défensive dans la section [Transformations de valeurs](#valuetransformations). Les deux blocs de calcul sont circonscrits à `tryCatch()`. Pour certaines erreurs, il est judicieux de renvoyer le vecteur d'entrée d'origine. Dans d'autres cas, je renvoie un vecteur de zéros.  

Notez que la régression linéaire utilisée pour éliminer la tendance est une régression chronologique. La variable explicative est un objet de série chronologique.  

Une fois que la fonction `ts.detrend()` est définie, nous l’appliquons aux variables appropriées du tableau de données. Nous devons convertir de force la liste obtenue créée par `lapply()` en tableau de données à l’aide de `as.data.frame()`. Compte tenu des aspects défensifs de `ts.detrend()`, l’échec du traitement de l’une des variables n’empêchera pas le traitement correct des autres.  

La dernière ligne de code crée un nuage de points par paire. Les résultats du nuage de points obtenu après l’exécution du code R sont illustrés dans la figure 17.

![nuage de points par paire de la série chronologique après élimination de la tendance et standardisation][18]

*Figure 17 : nuage de points par paire de la série chronologique après élimination de la tendance et standardisation.*

Vous pouvez comparer ces résultats à ceux présentés à la figure 16. L'élimination de la tendance et la standardisation des variables ont nettement déstructuré les relations entre ces variables.

Le code permettant de calculer les corrélations en tant qu’objets ccf R est le suivant :

    ## A function to compute pairwise correlations from a
    ## list of time series value vectors
    pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
      ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
    }

    ## A list of the pairwise indices
    corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

    ## Compute the list of ccf objects
    cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

    cadairycorrelations

L'exécution de ce code génère le journal présenté dans la figure 18.

    [ModuleOutput] Loading objects:
    [ModuleOutput]   port1
    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] [[1]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.148 0.358 0.317 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[2]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.395 -0.186 -0.238 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[3]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.059 -0.089 -0.127 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[4]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]    -1     0     1 
    [ModuleOutput] 0.140 0.294 0.293 
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] [[5]]
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput] Autocorrelations of series 'X', by lag
    [ModuleOutput] 
    [ModuleOutput] 
    [ModuleOutput]     -1      0      1 
    [ModuleOutput] -0.002 -0.074 -0.124 

*Figure 18 : liste des objets ccf résultant de l’analyse des corrélations par paire.*

À chaque décalage correspond une valeur de corrélation. Aucune de ces valeurs de corrélation n'est suffisamment grande pour être significative. Nous pouvons donc en conclure qu'il est possible de modéliser chaque variable de façon indépendante.

### <a name="output-a-dataframe"></a>Sortie d'un tableau de données
Nous avons calculé les corrélations par paire en tant que liste d'objets ccf R. Cela pose un léger problème dans le sens où le port de sortie du jeu de données de résultat a vraiment besoin d'un tableau de données. En outre, l’objet ccf est lui-même une liste et nous voulons uniquement les valeurs contenues dans le premier élément de cette liste, c’est-à-dire les corrélations aux différents décalages.

Le code suivant permet d’extraire les valeurs de décalage de la liste d’objets ccf, qui sont eux-mêmes des listes :

    df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

    c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
    r.names  <- c("Corr Cot Cheese - Ice Cream",
                  "Corr Cot Cheese - Milk Prod",
                  "Corr Cot Cheese - Fat Price",
                  "Corr Ice Cream - Mik Prod",
                  "Corr Ice Cream - Fat Price",
                  "Corr Milk Prod - Fat Price")

    ## Build a dataframe with the row names column and the
    ## correlation data frame and assign the column names
    outframe <- cbind(r.names, df.correlations)
    colnames(outframe) <- c.names
    outframe


    ## WARNING!
    ## The following line works only in Azure Machine Learning
    ## When running in RStudio, this code will result in an error
    #maml.mapOutputPort('outframe')

La première ligne de code est un peu compliquée et vous pourriez avoir besoin d’une explication vous aider à la comprendre. Voici ce que l'on a :

1. L’opérateur « **[[** » associé à l’argument « **1** » sélectionne le vecteur de corrélations au niveau des décalages dans le premier élément de la liste d’objets ccf.
2. La fonction `do.call()` applique la fonction `rbind()` aux éléments de la liste renvoyée par `lapply()`.
3. La fonction `data.frame()` convertit de force le résultat généré par `do.call()` en tableau de données.

Notez que les noms de lignes se trouvent dans une colonne du tableau de données. Cela préserve les noms de lignes quand ils sont générés à partir du module [d’exécution de script R][execute-r-script].

L’exécution du code génère la sortie présentée dans la figure 19 après avoir actionné la commande **Visualize** (Visualiser) au niveau du port du jeu de données de résultat. Les noms de lignes se trouvent dans la première colonne, comme prévu.

![résultats générés par l’analyse des corrélations][20]

*Figure 19 : résultats générés par l’analyse des corrélations.*

## <a id="seasonalforecasting"></a>Exemple de série chronologique : prévision saisonnière
Les données se trouvent maintenant dans un format adapté à l’analyse et nous avons déterminé qu’il n’y avait pas de corrélations significatives entre les variables. Continuons et créons un modèle de prévision chronologique. Nous nous servirons de ce modèle pour établir des prévisions concernant la production de lait californienne au cours des 12 mois de 2013.

Notre modèle de prévision aura deux composantes : une tendancielle et une saisonnière. La prévision complète est le fruit de ces deux composantes. Ce type de modèle est connu sous le nom de modèle multiplicatif. Le modèle alternatif est appelé modèle additif. Nous avons déjà appliqué une transformation logarithmique aux variables qui nous intéressent, ce qui rend cette analyse malléable.

Le code complet de cette section se trouve dans le fichier zip que vous avez téléchargé précédemment.

### <a name="creating-the-dataframe-for-analysis"></a>Création du tableau de données pour l’analyse
Commencez par ajouter un **nouveau** module [d’exécution de script R][execute-r-script] à votre expérimentation. Reliez la sortie de **jeu de données de résultat** du module [d’exécution de script R][execute-r-script] existant à l’entrée **Jeu de données 1** du nouveau module. Le résultat doit être similaire à la figure 20.

![l’expérimentation après l’ajout du nouveau module d’exécution de script R][21]

*Figure 20 : l’expérimentation après l’ajout du nouveau module d’exécution de script R.*

Comme pour l'analyse des corrélations que nous venons d'effectuer, nous devons ajouter une colonne avec un objet de série chronologique POSIXct. C’est exactement ce que fait le code suivant :

    # If running in Machine Learning Studio, uncomment the first line with maml.mapInputPort()
    cadairydata <- maml.mapInputPort(1)

    ## Create a new column as a POSIXct object
    Sys.setenv(TZ = "PST8PDT")
    cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

    str(cadairydata)

Exécutez ce code et consultez le journal. Le résultat doit être similaire à la figure 21.

    [ModuleOutput] [1] "Loading variable port1..."
    [ModuleOutput] 
    [ModuleOutput] 'data.frame':    228 obs. of  9 variables:
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
    [ModuleOutput] 
    [ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
    [ModuleOutput] 
    [ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...

*Figure 21 : aperçu du tableau de données.*

Avec ce résultat, nous sommes en mesure de lancer l'analyse.

### <a name="create-a-training-dataset"></a>Création d’un jeu de données d’apprentissage
Après avoir construit le tableau de données, nous devons maintenant créer un jeu de données d'apprentissage. Ces données engloberont toutes les observations à l'exception des 12 dernières de l'année 2013, ce qui correspond à notre jeu de données de test. Le code suivant scinde le tableau de données en sous-ensembles et crée des graphiques des variables de production et de prix des produits laitiers. Je crée ensuite des graphiques des quatre variables de production et de prix. Une fonction anonyme est utilisée pour définir des arguments pour le graphique, puis pour itérer sur la liste des deux autres arguments à l’aide de `Map()`. Vous vous dites peut-être qu'une boucle for aurait ici fait l'affaire. Vous avez raison, mais comme le langage R est un langage fonctionnel, je vous montre une approche fonctionnelle.

    cadairytrain <- cadairydata[1:216, ]

    Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
                   "Log CA Ice Cream Production, 1000s lb",
                   "Log CA Milk Production 1000s lb",
                   "Log North CA Milk Milk Fat Price per 1000 lb")

    Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)

L'exécution du code génère la série de graphiques chronologiques à partir de la sortie de l’appareil R présentée dans la figure 22. Notez que l'axe du temps est exprimé en unités de dates, avantage appréciable de la méthode de graphique chronologique.

![premier graphique chronologique des données de production et de prix des produits laitiers californiens](./media/r-quickstart/unnamed-chunk-161.png)

![deuxième graphique chronologique des données de production et de prix des produits laitiers californiens](./media/r-quickstart/unnamed-chunk-162.png)

![troisième graphique chronologique des données de production et de prix des produits laitiers californiens](./media/r-quickstart/unnamed-chunk-163.png)

![quatrième graphique chronologique des données de production et de prix des produits laitiers californiens](./media/r-quickstart/unnamed-chunk-164.png)

*Figure 22 : graphiques chronologiques des données de production et de prix des produits laitiers californiens.*

### <a name="a-trend-model"></a>Modèle de tendance
Après avoir créé un objet de série chronologique et examiné les données, lançons-nous dans l'élaboration d'un modèle de tendance pour les données de production de produits laitiers californiens. Pour cela, nous pouvons utiliser une régression chronologique. Toutefois, il apparaît évident au vu du graphique que nous aurons besoin de plusieurs pentes et intercepts pour modéliser avec précision la tendance observée dans les données d’apprentissage.

Compte tenu de la petite échelle des données, je vais générer le modèle de tendance dans RStudio, puis couper et coller le modèle obtenu dans Azure Machine Learning. RStudio offre un environnement interactif pour ce type d'analyse interactive.

Dans un premier temps, je vais tenter une régression polynomiale avec des puissances maximales de 3. Il existe un risque réel de surapprentissage de ces types de modèles. Par conséquent, il est préférable d'éviter les termes d'ordre élevé. La fonction `I()` empêche l’interprétation des contenus (elle les interprète « tels quels ») et vous permet d’écrire une fonction interprétée littéralement dans une équation de régression.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
    summary(milk.lm)

Voici ce que cela génère :

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12667 -0.02730  0.00236  0.02943  0.10586
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
    ## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
    ## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
    ## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0418 on 212 degrees of freedom
    ## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
    ## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16

Si l'on considère les valeurs P (Pr(>|t|)) de cette sortie, nous pouvons constater que le terme au carré n'est peut-être pas significatif. Je vais utiliser la fonction `update()` pour modifier ce modèle en éliminant le terme au carré.

    milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
    summary(milk.lm)

Voici ce que cela génère :

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.12597 -0.02659  0.00185  0.02963  0.10696
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
    ## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
    ## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0417 on 213 degrees of freedom
    ## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
    ## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16

Cela semble mieux. Tous les termes sont significatifs. Cependant, la valeur 2e-16 est une valeur par défaut à laquelle il ne faut pas trop prêter attention.  

En guise de test, créons un graphique chronologique à partir des données de production de produits laitiers californiens en affichant la courbe de tendance. J’ai ajouté le code suivant dans le module [d’exécution de script R][execute-r-script] Azure Machine Learning (pas RStudio) pour créer le modèle et tracer un graphique. Ce schéma est illustré dans la Figure 23.

    milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)

![données de production laitière californienne avec affichage du modèle de tendance](./media/r-quickstart/unnamed-chunk-18.png)

*Figure 23 : données de production laitière californienne avec affichage du modèle de tendance.*

Il apparaît que le modèle de tendance est assez bien adapté aux données. En outre, il n'y a visiblement pas de surapprentissage avec, par exemple, des fluctuations incongrues dans la courbe du modèle.  

### <a name="seasonal-model"></a>Modèle saisonnier
Maintenant que nous disposons d’un modèle de tendance, nous devons passer à l’étape supérieure et inclure les effets saisonniers. Nous allons utiliser le mois de l’année comme variable factice dans le modèle linéaire pour capturer l’effet mois par mois. Notez que lorsque des variables facteur sont introduites dans un modèle, l'intercept ne doit pas être calculé. Dans le cas contraire, la formule est surspécifiée et le code R supprime l'un des facteurs souhaités, mais conserve le terme intercept.

Comme notre modèle de tendance est satisfaisant, nous pouvons utiliser la fonction `update()` pour ajouter les nouveaux termes au modèle existant. La valeur -1 dans la formule de mise à jour supprime le terme intercept. Poursuivons dans RStudio pour le moment :

    milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
    summary(milk.lm2)

Voici ce que cela génère :

    ##
    ## Call:
    ## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
    ##     data = cadairytrain)
    ##
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max
    ## -0.06879 -0.01693  0.00346  0.01543  0.08726
    ##
    ## Coefficients:
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
    ## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
    ## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
    ## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
    ## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
    ## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
    ## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
    ## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
    ## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
    ## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
    ## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
    ## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
    ## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ##
    ## Residual standard error: 0.0263 on 202 degrees of freedom
    ## Multiple R-squared:     1,    Adjusted R-squared:     1
    ## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16

Nous constatons que le modèle n'a plus de terme intercept et qu'il comporte 12 facteurs mois significatifs. C'est exactement ce que nous voulions.

Créons un autre graphique chronologique à partir des données de production laitière californienne pour voir si le modèle saisonnier fonctionne correctement. J’ai ajouté le code suivant dans le module [d’exécution de script R][execute-r-script] Azure Machine Learning pour créer le modèle et tracer un graphique.

    milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

    plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
    lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)

L’exécution de ce code dans Azure Machine Learning génère le graphique présenté dans la figure 24.

![production laitière californienne avec le modèle incluant les effets saisonniers](./media/r-quickstart/unnamed-chunk-20.png)

*Figure 24 : production laitière californienne avec le modèle incluant les effets saisonniers.*

L'adéquation par rapport aux données présentées dans la figure 24 est plutôt encourageante. Les effets tendanciel et saisonnier (variation mensuelle) semblent raisonnables.

Intéressons-nous à présent aux résidus, autre point de vérification de notre modèle. Le code suivant calcule les valeurs prévisionnelles de nos deux modèles, calcule les résidus du modèle saisonnier et élabore ensuite le graphique de ces résidus pour les données d’apprentissage.

    ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute and plot the residuals
    residuals <- cadairydata$Milk.Prod - predict2
    plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")

Le graphique des résidus est illustré dans la figure 25.

![résidus du modèle saisonnier pour les données d’apprentissage](./media/r-quickstart/unnamed-chunk-21.png)

*Figure 25 : résidus du modèle saisonnier pour les données d’apprentissage.*

Ces résidus semblent corrects. Il n'y a pas de structure particulière si l'on excepte l'effet de la récession de 2008-2009, dont notre modèle ne rend pas très bien compte.

Le graphique représenté dans la figure 25 s’avère utile pour détecter les phénomènes temporels dans les résidus. L'approche explicite du calcul et de la représentation des résidus que j'ai utilisée place les résidus dans un ordre chronologique sur le graphique. Si, en revanche, j’avais représenté `milk.lm$residuals`sur le graphique, celui-ci n’aurait pas été établi dans un ordre chronologique.

Vous pouvez aussi utiliser `plot.lm()` pour générer une série de graphiques de diagnostic :

    ## Show the diagnostic plots for the model
    plot(milk.lm2, ask = FALSE)

Ce code génère une série de graphiques de diagnostic représentés dans la figure 26.

![premier graphique de diagnostic pour le modèle saisonnier](./media/r-quickstart/unnamed-chunk-221.png)

![deuxième graphique de diagnostic pour le modèle saisonnier](./media/r-quickstart/unnamed-chunk-222.png)

![troisième graphique de diagnostic pour le modèle saisonnier](./media/r-quickstart/unnamed-chunk-223.png)

![quatrième graphique de diagnostic pour le modèle saisonnier](./media/r-quickstart/unnamed-chunk-224.png)

*Figure 26 : graphiques de diagnostic pour le modèle saisonnier.*

Même si nous parvenons à identifier quelques points saillants dans ces graphiques, il n'en ressort rien d'extraordinaire. De plus, nous pouvons constater dans le graphique Normal Q-Q que les résidus sont répartis assez normalement, postulat important pour les modèles linéaires.

### <a name="forecasting-and-model-evaluation"></a>Prévision et évaluation des modèles
Il ne nous reste plus qu'une étape à effectuer pour terminer notre exemple. Nous devons calculer les prévisions et mesurer les erreurs par rapport aux données réelles. Notre prévision concernera les 12 mois de l'année 2013. Nous pouvons calculer une mesure d’erreur pour cette prévision par rapport aux données réelles qui ne font pas partie de notre jeu de données d’apprentissage. De plus, nous pouvons comparer les performances réalisées sur les 18 années des données d'apprentissage à celles enregistrées au cours des 12 mois des données de test.  

Un certain nombre de mesures permettent de mesurer les performances des modèles chronologiques. En l’occurrence, nous utiliserons l’erreur quadratique moyenne (ou RMS). La fonction suivante calcule l’erreur RMS entre deux séries :  

    RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
      ## Function to compute the RMS error or difference between two
      ## series or vectors

      messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                    "ERROR: Input vector to function RMS.error is too short",
                    "ERROR: Input vectors to function RMS.error must be of same length",
                    "WARNING: Funtion rms.error has received invald input time series.")

      ## Check the arguments
      if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
        warning(messages[1])
        return(NA)}

      if(length(series1) < min.length) {
        warning(messages[2])
        return(NA)}

      if((length(series1) != length(series2))) {
           warning(messages[3])
        return(NA)}

      ## If is.log is TRUE exponentiate the values, else just copy
      if(is.log) {
        tryCatch( {
          temp1 <- exp(series1)
          temp2 <- exp(series2) },
          error = function(e){warning(messages[4]); NA}
        )
      } else {
        temp1 <- series1
        temp2 <- series2
      }

     ## Compute predictions from our models
    predict1  <- predict(milk.lm, cadairydata)
    predict2  <- predict(milk.lm2, cadairydata)

    ## Compute the RMS error in a dataframe
      tryCatch( {
        sqrt(sum((temp1 - temp2)^2) / length(temp1))},
        error = function(e){warning(messages[4]); NA})
    }

À l’instar de la fonction `log.transform()` , dont nous avons parlé dans la section « Transformations de valeurs », cette fonction contient beaucoup de code de recherche d’erreurs et de récupération d’exceptions. Les principes utilisés sont les mêmes. Le travail est accompli à deux endroits circonscrits à `tryCatch()`. Dans un premier temps, les séries chronologiques sont élevées à une puissance, car nous travaillons avec les logarithmes des valeurs. Dans un deuxième temps, l'erreur RMS réelle est calculée.  

Étant dotés d'une fonction de mesure de l'erreur RMS, nous allons générer et sortir un tableau de données contenant les erreurs RMS. Nous inclurons des termes pour le modèle de tendance seul et le modèle complet avec des facteurs saisonniers. Le code suivant accomplit la tâche en utilisant les deux modèles linéaires que nous avons construits :

    ## Compute the RMS error in a dataframe
    ## Include the row names in the first column so they will
    ## appear in the output of the Execute R Script
    RMS.df  <-  data.frame(
    rowNames = c("Trend Model", "Seasonal Model"),
      Traing = c(
      RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
      RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
      Forecast = c(
        RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
        RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
    )
    RMS.df

    ## The following line should be executed only when running in
    ## Azure Machine Learning Studio
    maml.mapOutputPort('RMS.df')

L'exécution de ce code génère la sortie présentée dans la figure 27 au niveau du port de sortie du jeu de données de résultat.

![comparaison de l’erreur RMS pour les modèles][26]

*Figure 27 : comparaison de l’erreur RMS pour les modèles.*

Ces résultats nous montrent que l’ajout des facteurs saisonniers au modèle a pour effet de réduire sensiblement l’erreur RMS. Fort logiquement, l'erreur RMS au niveau des données d'apprentissage est plus limitée qu'au niveau de la prévision.

## <a id="appendixa"></a>ANNEXE A : guide de RStudio
RStudio étant très bien documenté, vous trouverez dans cette annexe des liens vers les principales sections de la documentation RStudio, qui vous aideront dans la prise en main du produit.

1. Création de projets
   
   Vous pouvez organiser et gérer votre code R dans les projets à l’aide de RStudio. Vous trouverez la documentation qui utilise des projets à l’adresse https://support.rstudio.com/hc/articles/200526207-Using-Projects.
   
   Je vous recommande de suivre ces instructions et de créer un projet pour les exemples de code R présentés dans ce document.  
2. Modification et exécution de code R
   
   RStudio offre un environnement intégré qui permet de modifier et d'exécuter du code R. Vous trouverez la documentation à l’adresse https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code.
3. Débogage
   
   RStudio intègre de puissantes fonctionnalités de débogage. La documentation sur ces fonctionnalités est disponible à l’adresse https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio.
   
   Les fonctionnalités de résolution des problèmes relatifs au point d’arrêt sont documentées à l’adresse https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting.

## <a id="appendixb"></a>ANNEXE B : informations supplémentaires
Ce didacticiel sur la programmation R couvre les concepts de base de ce qu'il vous faut pour utiliser le langage R avec Azure Machine Learning Studio. Si vous ne connaissez pas le langage R, deux présentations sont disponibles sur le site CRAN :

* « R pour les débutants » d'Emmanuel Paradis constitue un bon point de départ, à l'adresse http://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf.  
* Une introduction à R par W. N. Venables, et al. approfondit un peu le sujet : http://cran.r-project.org/doc/manuals/R-intro.html.

Il existe de nombreux livres sur le langage R qui peuvent vous aider à vous lancer. En voici quelques-uns que j'ai trouvés utiles :

* « The Art of R Programming: A Tour of Statistical Software Design » de Norman Matloff est une excellente introduction à la programmation en langage R.  
* L’ouvrage « R Cookbook » de Paul Teetor propose des solutions aux problèmes rencontrés lors de l’utilisation du langage R.  
* « R in Action » de Robert Kabacoff est un autre ouvrage introductif utile. Le site web d’accompagnement Quick R est une ressource utile à l’adresse http://www.statmethods.net/.
* R Inferno de Patrick Burns est un ouvrage qui aborde sur un ton étonnamment humoristique plusieurs thèmes complexes liés à la programmation en R. L’ouvrage est disponible gratuitement à l’adresse http://www.burns-stat.com/documents/books/the-r-inferno/.
* Si vous souhaitez examiner plus en profondeur les aspects avancés du langage R, jetez un œil au livre « Advanced R » de Hadley Wickham. La version en ligne de ce livre est disponible gratuitement à l'adresse http://adv-r.had.co.nz/.

Vous trouverez dans la section Task Views du site CRAN un catalogue de packages R pour l’analyse des séries chronologiques : http://cran.r-project.org/web/views/TimeSeries.html. Pour plus d’informations sur les packages d’objets chronologiques spécifiques, reportez-vous à la documentation de ce package.

L’ouvrage « Introductory Time Series with R » de Paul Cowpertwait et Andrew Metcalfe propose une introduction à l’utilisation de R pour l’analyse des séries chronologiques. Divers textes théoriques proposent des exemples R.

Quelques ressources Internet particulièrement utiles :

* DataCamp : apprenez le langage R tout en restant confortablement installé devant votre navigateur, grâce à des leçons vidéo et des exercices de codage. Il existe des didacticiels interactifs sur les dernières techniques et de langage R. Suivez le didacticiel R interactif gratuit à l’adresse https://www.datacamp.com/courses/introduction-to-r
* Guide de prise en main de R à partir de Programiz https://www.programiz.com/r-programming
* Un bref didacticiel sur le langage R par Kelly Black de l’Université de Clarkson : http://www.cyclismo.org/tutorial/R/
* Plus de 60 ressources sur le langage R : http://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html

<!--Image references-->
[1]: ./media/r-quickstart/fig1.png
[2]: ./media/r-quickstart/fig2.png
[3]: ./media/r-quickstart/fig3.png
[4]: ./media/r-quickstart/fig4.png
[5]: ./media/r-quickstart/fig5.png
[6]: ./media/r-quickstart/fig6.png
[7]: ./media/r-quickstart/fig7.png
[8]: ./media/r-quickstart/fig8.png
[9]: ./media/r-quickstart/fig9.png
[10]: ./media/r-quickstart/fig10.png
[11]: ./media/r-quickstart/fig11.png
[12]: ./media/r-quickstart/fig12.png
[13]: ./media/r-quickstart/fig13.png
[14]: ./media/r-quickstart/fig14.png
[15]: ./media/r-quickstart/fig15.png
[16]: ./media/r-quickstart/fig16.png
[17]: ./media/r-quickstart/fig17.png
[18]: ./media/r-quickstart/fig18.png
[19]: ./media/r-quickstart/fig19.png
[20]: ./media/r-quickstart/fig20.png
[21]: ./media/r-quickstart/fig21.png
[22]: ./media/r-quickstart/fig22.png

[26]: ./media/r-quickstart/fig26.png

<!--links-->
[appendixa]: #appendixa
[download]: https://azurebigdatatutorials.blob.core.windows.net/rquickstart/RFiles.zip


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
