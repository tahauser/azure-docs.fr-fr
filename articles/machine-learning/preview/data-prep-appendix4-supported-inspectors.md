---
title: "Inspecteurs pris en charge disponibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit la liste complète des inspecteurs disponibles pour la préparation des données Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 5d5797ede15be0779873f0a023433f0a915dd74a
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Inspecteurs pris en charge pour la préversion de préparation des données Azure Machine Learning
Ce document décrit le jeu d’inspecteurs disponibles dans cette préversion.

## <a name="the-halo-effect"></a>Effet de Halo 
Certains inspecteurs prennent en charge l’effet de halo. Cet effet utilise deux couleurs différentes pour indiquer immédiatement et visuellement la modification d’une transformation. La couleur grise représente la valeur précédent la transformation la plus récente, tandis que la couleur bleue indique la valeur actuelle. Cet effet peut être activé et désactivé dans les options.

## <a name="graphical-filtering"></a>Filtrage graphique 
Certains inspecteurs prennent en charge le filtrage des données en utilisant l’inspecteur comme éditeur. Pour cela, vous devez sélectionner des éléments de graphiques, puis utiliser la barre d’outils en haut à droite de la fenêtre Inspecteur pour filtrer les valeurs sélectionnées. 

## <a name="column-statistics"></a>Statistiques de colonne
Pour les colonnes numériques, cet inspecteur fournit un éventail de statistiques différentes relatives à la colonne. Les statistiques incluent les mesures suivantes : 
- Minimale
- Quartile inférieur
- Quartile médian
- Quartile supérieur
- Maximale
- Moyenne
- Écart standard


### <a name="options"></a>Options 
- Aucun

## <a name="histogram"></a>Histogramme 
Calcule et affiche un histogramme d’une colonne numérique unique. Le nombre de compartiments par défaut est calculé à l’aide de la règle de Scott. Toutefois, la règle peut être ignorée à l’aide d’options.

Cet inspecteur prend en charge l’effet de Halo.


### <a name="options"></a>Options
- Nombre minimal de compartiments (s’applique même quand la création de compartiments par défaut est activée)
- Nombre de compartiments par défaut (règle de Scott) 
- Afficher le halo
- Superposition du tracé de la densité du noyau (noyau gaussien) 
- Utiliser l’échelle logarithmique


### <a name="actions"></a>Actions
Cet inspecteur prend en charge le filtrage par le biais de compartiments, avec sélection d’un ou plusieurs compartiments. Appliquez les filtres comme indiqué précédemment.

## <a name="value-counts"></a>Nombres de valeurs
Cet inspecteur présente un tableau de la fréquence des valeurs pour la colonne actuellement sélectionnée. Par défaut, les six premières valeurs sont affichées. Vous pouvez cependant modifier la limite et choisir le nombre de votre choix. Vous pouvez également définir l’affichage afin de compter à partir du bas plutôt que du haut. Cet inspecteur prend en charge l’effet de halo.

### <a name="options"></a>Options 
- Nombre de premières valeurs affichées
- Tri décroissant
- Inclure les erreurs/valeurs NULL
- Afficher le halo
- Utiliser l’échelle logarithmique


### <a name="actions"></a>Actions 
Cet inspecteur prend en charge le filtrage par le biais de barres, avec sélection d’une ou plusieurs barres. Appliquez les filtres comme indiqué précédemment.

## <a name="box-plot"></a>Diagramme à surfaces 
Diagramme à surfaces en boîtes à moustaches d’une colonne numérique.

### <a name="options"></a>Options 
- Regrouper par colonne

## <a name="scatter-plot"></a>nuage de points
Nuage de points pour deux colonnes numériques. L’échantillonnage des données est réduit pour des raisons de performances. La taille d’échantillon peut être remplacée dans les options.

### <a name="options"></a>Options  
- Colonne de l’axe X
- Colonne de l’axe Y
- Taille d’échantillon
- Regrouper par colonne


## <a name="time-series"></a>Série chronologique
Graphique linéaire avec reconnaissance du temps sur l’axe X.

### <a name="options"></a>Options
- Colonne de date
- Colonne numérique
- Taille d’échantillon


### <a name="actions"></a>Actions
Cet inspecteur prend en charge le filtrage par le biais d’une méthode de sélection de type « cliquez et faites glisser » afin de sélectionner une plage sur le graphique. Une fois la sélection terminée, appliquez les filtres comme indiqué précédemment.


## <a name="map"></a>Mappage 
Carte avec des points tracés, en partant du principe que la latitude et la longitude ont été spécifiées. La latitude doit être sélectionnée en premier.

### <a name="options"></a>Options
- Colonne de latitude
- Colonne de longitude
- Clustering
- Regrouper par colonne


### <a name="actions"></a>Actions
Cet inspecteur prend en charge le filtrage par sélection de point sur la carte. Appuyez sur la touche **Ctrl**, puis cliquez et faites glisser la souris pour former un carré autour des points. Ensuite, appliquez les filtres comme indiqué précédemment.

Vous pouvez redimensionner rapidement la carte pour afficher uniquement les points possibles en appuyant sur le **E** sur le côté gauche de la carte.


## <a name="pattern-frequency"></a>Fréquence de motifs 

Cet inspecteur montre une liste de motifs dans la colonne de type chaîne sélectionnée. Les motifs sont représentés à l’aide d’une expression régulière comme la syntaxe. En pointant sur le motif vous faites apparaître les exemples de valeurs représentées par ce motif. Accompagnant les motifs, les couvertures approximatives en termes de pourcentage sont également ressorties.

![Image de l’inspecteur de motifs](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Options
- Nombre de premières valeurs affichées
- Tri décroissant
- Afficher le halo

### <a name="actions"></a>Actions
Cet inspecteur prend en charge le filtrage reposant sur les motifs affichés. Appuyez sur la touche **Ctrl**, puis sélectionnez les barres remplies dans l’inspecteur de motifs. Ensuite, appliquez les filtres comme indiqué précédemment. Suite à l’action de l’utilisateur, une étape de filtre avancé est ajoutée. Vous pouvez afficher et modifier le code Python généré en appelant l’option de modification de l’étape de filtre avancé.
