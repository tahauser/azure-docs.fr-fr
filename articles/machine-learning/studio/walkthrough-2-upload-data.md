---
title: 'Étape 2 : télécharger des données dans une expérience Machine Learning | Microsoft Docs'
description: "Étape 2 du guide pas à pas du développement d'une solution prédictive : téléchargement de données publiques stockées dans Azure Machine Learning Studio."
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 9f4bc52e-9919-4dea-90ea-5cf7cc506d85
ms.service: machine-learning
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.openlocfilehash: f482b1273f83f5ae5bb4f1e64609767ee0c5fe32
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="walkthrough-step-2-upload-existing-data-into-an-azure-machine-learning-experiment"></a>Étape de didacticiel pas à pas 2 : téléchargement de données existantes dans une expérience Azure Machine Learning
Voici la deuxième étape de la procédure pas à pas [Développement d'une solution d'analyse prédictive dans Azure Machine Learning](walkthrough-develop-predictive-solution.md)

1. [Créer un espace de travail Machine Learning](walkthrough-1-create-ml-workspace.md)
2. **Télécharger des données existantes**
3. [Créer une expérience](walkthrough-3-create-new-experiment.md)
4. [Former et évaluer les modèles](walkthrough-4-train-and-evaluate-models.md)
5. [Déployer le service web](walkthrough-5-publish-web-service.md)
6. [Accéder au service web](walkthrough-6-access-web-service.md)

- - -
Pour développer un modèle prédictif pour un risque de crédit, nous avons besoin de données que nous pouvons utiliser pour former et ensuite tester le modèle. Pour cette procédure pas à pas, nous allons utiliser le modèle « UCI Statlog (German Credit Data) Data Set » qui se trouve dans le référentiel de UC Irvine Machine Learning. Vous pouvez le trouver ici :   
<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Nous allons utiliser le fichier nommé **german.data**. Téléchargez ce fichier sur votre disque dur.  

Le jeu de données **german.data** contient des lignes de 20 variables pour 1000 candidats à un crédit. Ces 20 variables représentent l’ensemble des caractéristiques du jeu de données (le *vecteur de fonctionnalité*), qui fournit des caractéristiques permettant d’identifier chaque candidat à un crédit. Une colonne supplémentaire pour chaque ligne représente le risque de crédit calculé de chaque candidat : 700 candidats constituent un faible risque de crédit et 300 un risque élevé.

Le site Web UCI fournit une description des attributs du vecteur de fonctionnalité pour ces données. Cela inclut des informations financières, l’historique de crédit, le statut professionnel et des informations personnelles. Une évaluation binaire a été appliquée à chaque candidat, afin d'indiquer s'il constitue un risque de crédit faible ou élevé. 

Nous allons utiliser ces données pour former un modèle d'analyse prédictive. Lorsque nous aurons terminé, notre modèle pourra accepter un vecteur de fonctionnalité pour un nouvel individu et prévoir si celui-ci constitue un risque de crédit faible ou élevé.  

Voici une évolution intéressante. Sur le site web UCI, la description du jeu de données détermine les coûts générés par une erreur de classification des risques associés à un crédit accordé.
Si le modèle prévoit un crédit à haut risque pour une personne qui présente un risque réduit, cela signifie que la classification est erronée.
Cependant, le fait de prévoir un risque faible pour une personne présentant un risque élevé est cinq fois plus coûteux pour l’institution financière.

Nous voulons donc configurer notre modèle de sorte qu’il considère l’erreur de classification ci-dessus comme étant cinq fois plus coûteuse que l’erreur inverse.
Il existe une manière assez simple d’y parvenir lorsque nous configurons le modèle de notre exemple, à savoir multiplier par cinq la valeur des entrées représentant une personne qui présente un risque de crédit élevé. Ensuite, si le modèle considère une personne présentant un risque élevé comme ne représentant qu’un faible risque, il reproduit cette erreur cinq fois, une fois par doublon. Cela augmentera le coût de cette erreur dans les résultats de formation.


## <a name="convert-the-dataset-format"></a>Conversion du format du jeu de données
Le jeu de données d'origine utilise un format séparé par des espaces. Machine Learning Studio fonctionne mieux avec un fichier de valeurs séparées par des virgules (CSV). Nous allons donc convertir le jeu de données en remplaçant les espaces par des virgules.  

Il existe de nombreux moyens de convertir ces données. L'une des méthodes consiste à utiliser la commande Windows PowerShell suivante :   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Vous pouvez aussi utiliser la commande Unix sed :  

    sed 's/ /,/g' german.data > german.csv  

Dans les deux cas, nous avons créé une version séparée par des virgules des données dans un fichier nommé **german.csv** que nous utiliserons dans notre expérience.

## <a name="upload-the-dataset-to-machine-learning-studio"></a>Télécharger le jeu de données vers Machine Learning Studio
Une fois les données converties au format CSV, nous devons les télécharger vers Machine Learning Studio. 

1. Ouvrez la page d’accueil de Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Cliquez sur le menu ![Menu][1] dans le coin supérieur gauche de la fenêtre, cliquez sur **Azure Machine Learning**, sélectionnez **Studio**, puis connectez-vous.

3. Cliquez sur **+NOUVEAU** en bas de la fenêtre.

4. Sélectionnez **JEU DE DONNÉES**.

5. Sélectionnez **DEPUIS UN FICHIER LOCAL**.

    ![Ajouter un jeu de données à partir d’un fichier local][2]

6. Dans la boîte de dialogue **Charger un nouveau jeu de données**, cliquez sur **Parcourir**, puis recherchez le fichier **german.csv** que vous avez créé.

7. Entrez le nom du jeu de données. Pour les besoins de cette procédure, donnez-lui le nom suivant : « Données de carte de crédit allemande UCI ».

8. Pour le type de données, sélectionnez **Fichier CSV générique sans en-tête (.nh.csv)**.

9. Ajoutez la description de votre choix.

10. Cliquez sur la coche **OK**.  

    ![Télécharger le jeu de données][3]

Les données sont téléchargées dans un module de jeu de données utilisable dans une expérience.

Vous pouvez gérer les jeux de données que vous avez téléchargés dans Studio en cliquant sur l’onglet **JEUX DE DONNÉES** à gauche de la fenêtre de Studio.

![Gérer des jeux de données][4]

Pour plus d’informations sur l’importation d’autres types de données dans une expérience, consultez [Importez vos données d’apprentissage dans Azure Machine Learning Studio](import-data.md).

**Étape suivante : [créer une expérience](walkthrough-3-create-new-experiment.md)**

[1]: media/walkthrough-2-upload-data/menu.png
[2]: media/walkthrough-2-upload-data/add-dataset.png
[3]: media/walkthrough-2-upload-data/upload-dataset.png
[4]: media/walkthrough-2-upload-data/dataset-list.png
