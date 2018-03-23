---
title: Science des données avec une image Data Science Virtual Machine Linux sur Azure | Microsoft Docs
description: Comment effectuer plusieurs tâches courantes de science des données avec la machine virtuelle de science des données Linux.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: bradsev;paulsh
ms.openlocfilehash: 9b8a9b9bba242fd7c86dc285a77317a5821948df
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="data-science-with-a-linux-data-science-virtual-machine-on-azure"></a>Science des données avec une image Data Science Virtual Machine Linux sur Azure
Cette procédure pas à pas vous montre comment effectuer plusieurs tâches courantes de science des données avec la machine virtuelle de science des données Linux. La machine virtuelle de science des données Linux est une image de machine virtuelle disponible sur Azure qui est préinstallée avec plusieurs outils couramment utilisés dans le cadre de l’analyse de données et du Machine Learning. Les composants logiciels clés sont détaillés dans la rubrique [Approvisionnement d’une machine virtuelle de science des données Linux](linux-dsvm-intro.md) . L’image de la machine virtuelle facilite la prise en main de la science des données en quelques minutes, sans avoir à installer et à configurer individuellement chacun des outils individuellement. Le cas échéant, vous pouvez facilement faire monter en puissance la machine virtuelle, et l’arrêter lorsqu’elle est inutilisée. Cette ressource est donc flexible et économique.

Les tâches de science des données décrites dans cette procédure pas à pas suivent les étapes décrites dans le [processus de science des données pour les équipes](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Ce processus fournit une approche systématique de la science des données qui permet aux équipes de scientifiques des données de collaborer efficacement tout au long du cycle de vie du développement d’applications intelligentes. Le processus de science des données fournit également une infrastructure itérative pour la science des données, qui peut être suivie par une personne.

Au cours de cette procédure pas à pas, nous analysons le jeu de données [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) . Il s’agit d’un ensemble d’e-mails marqués comme courrier indésirable ou courrier légitime (non indésirable), qui contient également des statistiques sur le contenu des e-mails. Les statistiques incluses sont évoquées dans la section suivante.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir utiliser une machine virtuelle de science des données Linux, vous devez disposer des éléments suivants :

* Un **abonnement Azure**. Si vous n’en avez pas déjà un, voir [Créez votre compte Azure gratuit](https://azure.microsoft.com/free/).
* Une [**machine virtuelle de science des données Linux**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Pour plus d’informations sur l’approvisionnement de cette machine virtuelle, consultez [Approvisionnement d’une machine virtuelle de science des données Linux](linux-dsvm-intro.md).
* [X2Go](http://wiki.x2go.org/doku.php) installé sur votre ordinateur et une session XFCE ouverte. Pour plus d’informations sur l’installation et la configuration d’un **client X2Go**, consultez [Installation et configuration du client X2Go](linux-dsvm-intro.md#installing-and-configuring-x2go-client).
* Pour un défilement plus fluide, basculez l’indicateur gfx.xrender.enabled dans about:config, dans le navigateur FireFox des machines virtuelles. [Cliquez ici pour en savoir plus.](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/) Envisagez également de définir *mousewheel.enable_pixel_scrolling* sur False. [Cliquez ici pour obtenir des instructions.](https://support.mozilla.org/en-US/questions/981140)
* Un **compte AzureML**. Si vous n’avez pas déjà un, inscrivez-vous pour en obtenir un nouveau sur la [page d’accueil AzureML](https://studio.azureml.net/). Il existe un niveau d’utilisation gratuit pour vous aider à commencer.

## <a name="download-the-spambase-dataset"></a>Télécharger le jeu de données spambase
Le jeu de données [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) est un ensemble de données relativement réduit qui ne contient que 4 601 exemples. Il s’agit d’une taille pratique à utiliser lors de la démonstration de certaines des fonctionnalités clés de la machine virtuelle de science des données, car elle limite les besoins en ressources.

> [!NOTE]
> Cette procédure pas à pas a été créée sur une machine virtuelle de science des données Linux de taille D2 v2. Cette taille de machine virtuelle de science des données est capable de gérer les procédures décrites dans cette procédure pas à pas.
>
>

Si vous avez besoin de plus d’espace de stockage, vous pouvez créer des disques supplémentaires et les joindre à votre machine virtuelle. Ces disques utilisent le stockage Azure persistant ; ainsi, leurs données sont conservées même lorsque le serveur est réapprovisionné en raison d’un redimensionnement ou d’un arrêt. Pour ajouter un disque et l’attacher à votre machine virtuelle, suivez les instructions [Ajouter un disque à une machine virtuelle Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Cette procédure utilise l’interface de ligne de commande Azure (Azure CLI), qui est déjà installée sur la machine virtuelle de science des données. Par conséquent, ces procédures peuvent être entièrement effectuées à partir de la machine virtuelle elle-même. Une autre option pour augmenter le stockage consiste à utiliser les [fichiers Azure](../../storage/files/storage-how-to-use-files-linux.md).

Pour télécharger les données, ouvrez une fenêtre de terminal et exécutez cette commande :

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Le fichier téléchargé n’a pas de ligne d’en-tête, nous allons donc créer un autre fichier doté d’un en-tête. Exécutez cette commande pour créer un fichier avec les en-têtes appropriés :

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Ensuite, concaténez les deux fichiers ensemble avec la commande :

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Le jeu de données possède plusieurs types de statistiques sur chaque e-mail :

* Les colonnes comme ***word\_freq\_WORD*** indiquent le pourcentage de mots dans l’e-mail correspondant à *WORD*. Par exemple, si la valeur *word\_freq\_make* correspond à 1, 1 % de tous les mots dans l’e-mail était *make*.
* Les colonnes comme ***char\_freq\_CHAR*** indiquent le pourcentage de tous les caractères dans l’e-mail correspondant à *CHAR*.
* ***capital\_run\_length\_longest*** est la longueur la plus longue d’une séquence de lettres majuscules.
* ***capital\_run\_length\_average*** est la longueur moyenne de toutes les séquences de lettres majuscules.
* ***capital\_run\_length\_total*** est la longueur totale de toutes les séquences de lettres majuscules.
* ***spam*** indique si l’e-mail a été considéré comme du courrier indésirable ou non (1 = courrier indésirable, 0 = courrier non indésirable).

## <a name="explore-the-dataset-with-microsoft-r-open"></a>Explorer le jeu de données avec Microsoft R Open
Nous allons examiner les données et découvrir certaines fonctionnalités de base du Machine Learning avec R. La machine virtuelle de science des données est fournie avec [Microsoft R Open](https://mran.revolutionanalytics.com/open/) préinstallé. Les bibliothèques mathématiques multithread dans cette version de R offrent de meilleures performances que les différentes versions monothread. Microsoft R Open fournit également la reproductibilité à l’aide d’une capture instantanée du référentiel du package CRAN.

Pour obtenir des copies des exemples de code utilisés dans cette procédure pas à pas, clonez le référentiel **Azure-Machine-Learning-Data-Science** à l’aide de git, qui est déjà préinstallé sur la machine virtuelle. Depuis la ligne de commande git, exécutez :

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Ouvrez une fenêtre de terminal et démarrez une nouvelle session R avec la console interactive R.

> [!NOTE]
> Vous pouvez également utiliser RStudio pour les procédures suivantes. Pour installer RStudio, exécutez cette commande à partir d’un terminal : `./Desktop/DSVM\ tools/installRStudio.sh`
>
>

Pour importer les données et configurer l’environnement, exécutez :

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Pour afficher un résumé des statistiques de chaque colonne :

    summary(data)

Pour une vue différente des données :

    str(data)

Vous voyez le type de chaque variable et les premières valeurs dans le jeu de données.

La colonne *spam* a été lue comme un entier, mais il s’agit en fait d’une variable par catégorie (ou facteur). Pour définir son type :

    data$spam <- as.factor(data$spam)

Pour effectuer une analyse exploratoire, utilisez le package [ggplot2](http://ggplot2.org/) , une bibliothèque de graphiques populaire pour R qui est déjà installée sur la machine virtuelle. Remarque : à partir des données récapitulatives affichées précédemment, nous disposons de statistiques résumées sur la fréquence du point d’exclamation. Nous allons tracer ces fréquences ici avec les commandes suivantes :

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Étant donné que la barre de zéro décale le tracé, nous allons nous en débarrasser :

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Il existe une densité non triviale au-dessus de 1 qui semble intéressante. Examinons simplement ces données :

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Ensuite, répartissez-les en courrier indésirable et courrier légitime :

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Ces exemples doivent vous permettre d’effectuer des tracés similaires des autres colonnes pour explorer les données qu’elles contiennent.

## <a name="train-and-test-an-ml-model"></a>Effectuer l’apprentissage et tester un modèle ML
À présent, nous allons effectuer l’apprentissage de quelques modèles de Machine Learning pour classer les e-mails dans le jeu de données comme courrier indésirable ou courrier légitime. Dans cette section, nous effectuons l’apprentissage d’un modèle d’arbre de décision et d’un modèle de forêts aléatoires, puis nous testons la précision de leurs prédictions.

> [!NOTE]
> Le package rpart (partition récursive et arbres de régression) utilisé dans le code suivant est déjà installé sur la machine virtuelle de science des données.
>
>

Nous allons commencer par diviser le jeu de données en jeu d’apprentissage et jeu de test :

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Ensuite, nous allons créer un arbre de décision pour classer les e-mails.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Voici le résultat :

![1](./media/linux-dsvm-walkthrough/decision-tree.png)

Pour déterminer la qualité d’exécution sur l’ensemble d’apprentissage, utilisez le code suivant :

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Pour déterminer la qualité d’exécution sur le jeu de test :

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Essayons également un modèle de forêts aléatoires. Les forêts aléatoires effectuent l’apprentissage d’une multitude d’arbres de décision et génèrent une classe qui constitue le mode de classification de tous les arbres de décision individuels. Elles fournissent une approche de Machine Learning plus puissante, car elles corrigent la tendance d’un modèle d’arbre de décision à dépasser un jeu de données d’apprentissage.

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Déployer un modèle dans Azure ML
[Azure Machine Learning Studio](https://studio.azureml.net/) (AzureML) est un service cloud qui facilite la création et le déploiement des modèles d’analyse prédictive. L’une des fonctionnalités intéressantes d’AzureML est sa capacité à publier toute fonction R comme un service web. Le package R AzureML rend le déploiement facile à réaliser à partir de notre session R sur la machine virtuelle de science des données.

Pour déployer le code de l’arbre de décision à partir de la section précédente, vous devez vous connecter à Azure Machine Learning Studio. Vous avez besoin de votre ID d’espace de travail et d’un jeton d’autorisation pour vous connecter. Pour rechercher ces valeurs et initialiser les variables AzureML avec celles-ci :

Sélectionnez **Paramètres** dans le menu de gauche. Notez votre **ID D’ESPACE DE TRAVAIL**. ![2](./media/linux-dsvm-walkthrough/workspace-id.png)

Sélectionnez **Jetons d’authentification** dans le menu général et notez votre **Jeton d’autorisation principal**.![3](./media/linux-dsvm-walkthrough/workspace-token.png)

Chargez le package **AzureML** , puis définissez les valeurs des variables avec votre jeton et votre ID d’espace de travail dans votre session R sur la machine virtuelle de science des données :

    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Nous allons simplifier le modèle afin de rendre cette démonstration plus facile à implémenter. Sélectionnez les trois variables dans l’arbre de décision le plus proche de la racine, et créez un arbre à l’aide de ces trois variables seulement :

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Nous avons besoin d’une fonction de prédiction qui prend les fonctionnalités comme une entrée et renvoie les valeurs prédites :

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publiez la fonction predictSpam sur AzureML à l’aide de la fonction **publishWebService** :

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Cette fonction prend la fonction **predictSpam**, crée un service web nommé **spamWebService** avec des entrées et des sorties définies, et renvoie des informations sur le nouveau point de terminaison.

Affichez les détails du service web publié, notamment son point de terminaison d’API et les touches d’accès rapide avec la commande :

    spamWebService[[2]]

Pour l’essayer sur les 10 premières lignes du test défini :

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Utiliser les autres outils disponibles
Les sections suivantes montrent comment utiliser certains des outils installés sur la machine virtuelle de science des données Linux. Voici la liste des outils abordés :

* XGBoost
* Python
* Jupyterhub
* Rattle
* PostgreSQL et Squirrel SQL
* SQL Server Data Warehouse

## <a name="xgboost"></a>XGBoost
[XGBoost](https://xgboost.readthedocs.org/en/latest/) est un outil qui offre une implémentation rapide et précise des arborescences optimisées.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost peut également appeler à partir de Python ou d’une ligne de commande.

## <a name="python"></a>Python
Pour un développement basé sur Python, les versions 2.7 et 3.5 des distributions Anaconda Python ont été installées dans la machine virtuelle de science des données Linux.

> [!NOTE]
> La distribution Anaconda inclut [Condas](http://conda.pydata.org/docs/index.html), qui peut être utilisé pour créer des environnements personnalisés pour Python avec des versions et/ou des packages différents installés.
>
>

Nous allons lire le jeu de données spambase et classer les e-mails avec des machines à vecteurs de support dans scikit-learn :

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Pour effectuer des prédictions :

    clf.predict(X.ix[0:20, :])

Pour montrer comment publier un point de terminaison AzureML, créons un modèle plus simple des trois variables comme nous l’avons fait précédemment lorsque nous avons publié le modèle R.

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Pour publier le modèle dans AzureML :

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

> [!NOTE]
> Cette option est uniquement disponible pour Python 2.7 et n’est pas encore prise en charge sur 3.5. Exécutez avec **/anaconda/bin/python2.7**.
>
>

## <a name="jupyterhub"></a>Jupyterhub
La distribution Anaconda dans la machine virtuelle de science des données est fournie avec un bloc-notes Jupyter, un environnement multiplateforme pour partager Python, R, ou le code et l’analyse Julia. Le serveur Jupyter Notebook est accessible via JupyterHub. Vous vous connectez en utilisant votre nom d’utilisateur Linux local et votre mot de passe à ***https://\<nom DNS de machine virtuelle ou adresse IP\>:8000/***. Tous les fichiers de configuration pour JupyterHub se trouvent dans le répertoire **/etc/jupyterhub**.

Plusieurs exemples de notebooks sont déjà installés sur la machine virtuelle :

* Consultez [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) pour un exemple de notebook Python.
* Consultez [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) pour un exemple de notebook **R** .
* Consultez [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) pour un autre exemple de notebook **Python** .

> [!NOTE]
> Le langage Julia est également disponible à partir de la ligne de commande sur la machine virtuelle de science des données Linux.
>
>

## <a name="rattle"></a>Rattle
[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (l’outil d’analyse R pour apprendre plus facilement) est un outil R graphique pour l’exploration de données. Il possède une interface intuitive qui facilite la charge, l’exploration et la transformation des données, ainsi que la création et l’évaluation des modèles.  L’article [Rattle: A Data Mining GUI for R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) (Rattle : une interface utilisateur graphique pour l’exploration de données pour R) fournit une procédure pas à pas présentant ses fonctionnalités.

Installez et démarrez Rattle avec les commandes suivantes :

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

> [!NOTE]
> L’installation n’est pas obligatoire sur la machine virtuelle de science des données. Toutefois, Rattle peut vous inviter à installer des packages supplémentaires lors de son chargement.
>
>

Rattle utilise une interface basée sur des onglets. La plupart des onglets correspondent aux étapes du [Processus de science des données](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), telles que le chargement de données ou l’exploration. Le processus de science des données se déroule de gauche à droite à travers les onglets. Cependant, le dernier onglet contient un journal des commandes R exécutées par Rattle.

Pour charger et configurer le jeu de données :

* Pour charger le fichier, sélectionnez l’onglet **Données** , puis
* Choisissez le sélecteur en regard de **Nom de fichier**, puis **spambaseHeaders.data**.
* Pour charger le fichier, sélectionnez **Exécuter** dans la première ligne de boutons. Vous devez voir un résumé de chaque colonne, notamment son type de données identifié, qu’il s’agisse d’une entrée, d’une cible ou d’un autre type de variable, ainsi que le nombre de valeurs uniques.
* Rattle a correctement identifié la colonne **spam** comme étant la cible. Sélectionnez la colonne spam, puis définissez le **Type de données cible** sur **Par catégorie**.

Pour explorer les données :

* Sélectionnez l’onglet **Explorer** .
* Cliquez sur **Résumé**, puis sur **Exécuter**, pour afficher des informations sur les types de variable et certaines statistiques résumées.
* Pour afficher d’autres types de statistiques relatives à chaque variable, sélectionnez d’autres options, comme **Décrire** ou **Concepts de base**.

L’onglet **Explorer** vous permet également de générer de nombreux tracés ingénieux. Pour tracer un histogramme des données :

* Sélectionnez **Distributions**.
* Cochez **Histogramme** pour **word_freq_remove** et **word_freq_you**.
* Sélectionnez **Exécuter**. Vous devez voir les deux graphiques de densité dans une seule fenêtre de graphique, où il est clair que le mot « you » (vous) apparaît beaucoup plus fréquemment dans les e-mails que le mot « remove » (supprimer).

Les tracés de corrélation sont également intéressants. Pour en créer un :

* Choisissez **Corrélation** comme **Type**, puis
* Sélectionnez **Exécuter**.
* Rattle vous avertit qu’il recommande un maximum de 40 variables. Sélectionnez **Oui** pour afficher le tracé.

Des corrélations intéressantes existent : le terme « technologie » est étroitement corrélé avec les termes « HP » et « laboratoires », par exemple. Il est également étroitement corrélé avec « 650 », car le code de région des donneurs du dataset est 650.

Les valeurs numériques des corrélations entre les mots sont disponibles dans la fenêtre d’exploration. Il est intéressant de noter, par exemple, que le terme « technologie » est négativement corrélé avec les termes « votre » et « argent ».

Rattle peut transformer le jeu de données pour gérer certains problèmes courants. Par exemple, il vous permet de remettre à l’échelle les fonctionnalités, d’imputer les valeurs manquantes, de gérer les valeurs hors-norme et de supprimer des variables ou des observations avec des données manquantes. Rattle peut également identifier des règles d’association entre des observations et/ou des variables. Ces onglets sont hors de portée pour cette introduction pas à pas.

Rattle peut également effectuer une analyse de cluster. Nous allons exclure certaines fonctionnalités pour simplifier la lecture de la sortie. Sous l’onglet **Données**, choisissez **Ignorer** en regard de chacune des variables à l’exception de ces dix éléments :

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Ensuite, revenez à l’onglet **Cluster**, choisissez **KMeans**, et définissez le *Nombre de clusters* sur 4. Ensuite, sélectionnez **Exécuter**. Les résultats s’affichent dans la fenêtre de sortie. Un cluster possède une fréquence élevée de « george » et de « hp » et est probablement un e-mail professionnel légitime.

Pour créer un modèle Machine Learning d’arbre de décision simple :

* Sélectionnez l’onglet **Modèle** ,
* Choisissez **Arbre** en tant que **Type**.
* Sélectionnez **Exécuter** pour afficher l’arbre sous forme de texte dans la fenêtre de sortie.
* Sélectionnez le bouton **Dessin** pour afficher une version graphique. Celle-ci est très similaire à l’arbre obtenu précédemment à l’aide de *rpart*.

L’une des fonctionnalités intéressantes de Rattle est sa capacité à exécuter plusieurs méthodes Machine Learning et à les évaluer rapidement. Voici la procédure :

* Choisissez **Tous** pour le **Type**.
* Sélectionnez **Exécuter**.
* Une fois la procédure terminée, vous pouvez cliquer sur n’importe quel **Type** unique, comme **SVM**, et afficher les résultats.
* Vous pouvez également comparer les performances des modèles sur le jeu de validation à l’aide de l’onglet **Évaluer** . Par exemple, la sélection de l’option **Matrice d’erreur** affiche la matrice de confusion, l’erreur globale et l’erreur de classe moyennée pour chaque modèle sur le jeu de validation.
* Vous pouvez également tracer des courbes ROC, effectuer des analyses de sensibilité et d’autres types d’évaluations de modèle.

Une fois que vous avez terminé la création de modèles, sélectionnez l’onglet **Journal** pour afficher le code R exécuté par Rattle pendant votre session. Vous pouvez sélectionner le bouton **Exporter** pour l’enregistrer.

> [!NOTE]
> Il existe un bogue dans la version actuelle de Rattle. Pour modifier le script ou l’utiliser pour répéter les étapes ultérieurement, vous devez insérer un symbole dièse (#) devant *Exporter ce journal… * dans le texte du journal.
>
>

## <a name="postgresql--squirrel-sql"></a>PostgreSQL et Squirrel SQL
La machine virtuelle de science des données est fournie avec PostgreSQL installé. PostgreSQL est une base de données relationnelle, open source et sophistiquée. Cette section montre comment charger notre jeu de données spam dans PostgreSQL, puis l’interroger.

Avant de pouvoir charger les données, vous devez autoriser l’authentification par mot de passe à partir de l’hôte local. À l’invite de commandes :

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Au bas du fichier de configuration, plusieurs lignes détaillent les connexions autorisées :

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Modifiez la ligne « connexions locales IPv4 » pour utiliser md5 au lieu d’ident, afin de permettre la connexion avec un nom d’utilisateur et un mot de passe :

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Ensuite, redémarrez le service postgres :

    sudo systemctl restart postgresql

Pour lancer psql, un terminal interactif pour PostgreSQL, en tant qu’utilisateur postgres intégré, exécutez la commande suivante à partir d’une invite de commandes :

    sudo -u postgres psql

Créez un compte d’utilisateur à l’aide du même nom d’utilisateur que celui du compte Linux avec lequel vous êtes actuellement connecté, et attribuez-lui un mot de passe :

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Ensuite, connectez-vous à psql en tant qu’utilisateur :

    psql

Et importez les données dans une base de données :

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

À présent, nous allons explorer les données et exécuter des requêtes à l’aide de **Squirrel SQL**, un outil graphique qui vous permet d’interagir avec les bases de données via un pilote JDBC.

Pour commencer, lancez Squirrel SQL à partir du menu Applications. Pour configurer le pilote :

* Sélectionnez **Windows**, puis **Afficher les pilotes**.
* Cliquez avec le bouton droit sur **PostgreSQL** et sélectionnez **Modifier le pilote**.
* Sélectionnez **Chemin d’accès de la classe supplémentaire**, puis **Ajouter**.
* Entrez ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** pour le **nom de fichier** et
* Sélectionnez **Ouvrir**.
* Choisissez Pilotes de la liste, puis sélectionnez **org.postgresql.Driver** dans **Nom de la classe**, et sélectionnez **OK**.

Pour configurer la connexion au serveur local :

* Sélectionnez **Windows**, puis **Afficher les alias**.
* Choisissez le bouton **+** pour créer un alias.
* Nommez-le *Base de données de courrier indésirable*, et choisissez **PostgreSQL** dans la liste déroulante **Pilote**.
* Définissez l’URL sur *jdbc:postgresql://localhost/spam*.
* Entrez votre *nom d’utilisateur* et votre *mot de passe*.
* Cliquez sur **OK**.
* Pour ouvrir la fenêtre **Connexion**, double-cliquez sur l’alias de la ***Base de données de courrier indésirable***.
* Sélectionnez **Connecter**.

Pour exécuter des requêtes :

* Sélectionnez l’onglet **SQL** .
* Entrez une requête simple telle que `SELECT * from data;` dans la zone de texte de requête en haut de l’onglet SQL.
* Appuyez sur **Ctrl+Entrée** pour l’exécuter. Par défaut, SQL Squirrel renvoie les 100 premières lignes de votre requête.

Il existe de nombreuses requêtes supplémentaires, que vous pouvez exécuter pour explorer ces données. Par exemple, en quoi la fréquence du mot *make* diffère-t-elle entre le courrier indésirable et le courrier légitime ?

    SELECT avg(word_freq_make), spam from data group by spam;

Ou quelles sont les caractéristiques des e-mails qui contiennent souvent le terme *3d*?

    SELECT * from data order by word_freq_3d desc;

La plupart des e-mails qui présentent de nombreuses occurrences de *3d* sont apparemment du courrier indésirable. Cela peut donc constituer une fonctionnalité utile pour la création d’un modèle prédictif de classement des e-mails.

Si vous souhaitiez effectuer du Machine Learning avec des données stockées dans une base de données PostgreSQL, envisagez d’utiliser [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>SQL Server Data Warehouse
Azure SQL Data Warehouse est une base de données de mise à l’échelle basée sur le cloud qui prend en charge le traitement de grands volumes de données relationnelles et non relationnelles. Pour plus d’informations, consultez [En quoi consiste Azure SQL Data Warehouse ?](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Pour vous connecter à l’entrepôt de données et créer la table, exécutez la commande suivante depuis une invite de commandes :

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Ensuite, à l’invite sqlcmd :

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copier des données avec bcp :

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

> [!NOTE]
> Les fins de ligne dans le fichier téléchargé sont de type Windows, mais bcp attend le style UNIX ; il convient donc de l’indiquer à bcp avec l’indicateur -r.
>
>

Et exécuter des requêtes avec sqlcmd :

    select top 10 spam, char_freq_dollar from spam;
    GO

Vous pouvez également exécuter des requêtes avec Squirrel SQL. Suivez des étapes similaires pour PostgreSQL, à l’aide du pilote JDBC Microsoft MSSQL Server, disponible dans ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Étapes suivantes
Pour une vue d’ensemble des rubriques qui vous guident à travers les tâches qui constituent le processus de science des données dans Azure, consultez [processus de science des données pour les équipes](http://aka.ms/datascienceprocess).

Pour une description des autres procédures pas à pas complètes illustrant les étapes du processus TDSP pour des scénarios spécifiques, voir [Procédures pas à pas du processus TDSP (Team Data Science Process)](../team-data-science-process/walkthroughs.md). Les procédures pas à pas montrent également comment combiner les outils et services dans le cloud et sur site dans un flux de travail ou un pipeline pour créer une application intelligente.
