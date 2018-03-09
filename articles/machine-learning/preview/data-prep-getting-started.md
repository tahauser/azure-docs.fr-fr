---
title: "Bien démarrer avec la Préparation des données pour Azure Machine Learning | Microsoft Docs"
description: "Ceci est le guide de prise en main pour la section de Préparation des données d’AML Workbench."
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/07/2017
ms.openlocfilehash: b0fbb0af433cfad6693b022d7a00373dc39533aa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="getting-started-with-data-preparation"></a>Bien démarrer avec la Préparation des données

Bienvenue dans le guide de prise en main de Préparation des données. 

La Préparation des données fournit un ensemble d’outils permettant d’explorer, de comprendre et de résoudre des problèmes de données de manière efficace. Elle vous permet de consommer des données dans de nombreux formats et de transformer ces données en données nettoyées mieux adaptées à leur utilisation en aval.

La Préparation des données est installée dans le cadre de l’expérience Azure Machine Learning Workbench.  Utilisez la Préparation des données localement ou déployez-la sur un cluster cible et le cloud en tant qu’environnement d’exécution ou runtime.

Le runtime au moment de la conception utilise Python pour l’extensibilité et dépend de plusieurs bibliothèques Python, telles que Pandas. Comme avec d’autres composants d’Azure ML Workbench, il est inutile d’installer Python (il est installé pour vous). Toutefois, si vous avez besoin d’installer des bibliothèques supplémentaires celles-ci doivent être installées dans le répertoire Python d’Azure ML Workbench, et non dans votre répertoire Python habituel. Vous trouverez des informations supplémentaires sur la façon d’installer des packages dans [cet article](data-prep-python-extensibility-overview.md).

Vous devez créer un projet avant de pouvoir utiliser la Préparation des données. Une fois ce projet créé, vous pouvez préparer des données. 

Accédez à la section Data (Données) du projet en sélectionnant l’icône Data ![icône de source de données](media/data-prep-getting-started/data-source-icon.png) sur la gauche de l’écran.  Cliquez à nouveau sur « + » pour **ajouter une source de données**. L’Assistant Source de données doit démarrer et ajouter un fichier de **Source de données** (.dsource) au projet. Par défaut, l’affichage des données est la grille. Au-dessus de la grille, il est également possible de sélectionner l’affichage Métriques. Dans la vue Métriques, des statistiques de synthèse sont affichées.  Après avoir examiné les statistiques de synthèse, cliquez sur **Prepare** (Préparer) en haut de l’écran. [Plus d’informations sur l’Assistant Source de données](data-source-wizard.md) 

## <a name="building-blocks-of-data-preparation"></a>Blocs de construction de la Préparation des données ##
### <a name="the-package"></a>Le package ###

Un package est le conteneur principal pour votre travail. Un package est l’artefact qui est enregistré sur disque et chargé à partir du disque. Pendant que vous travaillez sur le client, le package est constamment enregistré automatiquement en arrière-plan. 

Vous pouvez explorer le résultat (la sortie) d’un package dans Python ou par le biais d’un bloc-notes Jupyter.

Un package peut être exécuté sur plusieurs runtimes, notamment Python local, Spark (y compris dans Docker) et HDInsight.

Un package contient un ou plusieurs flux de données qui sont les étapes et les transformations appliquées aux données.

Un package peut utiliser un autre package en tant que source de données (appelé « flux de données de référence »).

### <a name="the-dataflow"></a>Le flux de données ###
Un flux de données a une source et des transformations facultatives qui sont organisées en une série d’étapes et de destinations facultatives. Un clic sur une étape réexécute toutes les sources et les transformations antérieures à l’étape sélectionnée (y compris cette dernière).  Les données transformées à cette étape sont indiquées dans la grille. Vous pouvez ajouter, déplacer et supprimer des étapes dans un flux de données par le biais de la liste d’étapes.

Vous pouvez ouvrir ou fermer la liste d’étapes affichée sur le côté droit du client pour fournir plus d’espace à l’écran.

Plusieurs flux de données peuvent exister simultanément dans l’interface utilisateur. Chaque flux de données est représenté sous forme d’onglet dans l’interface utilisateur.

### <a name="the-source"></a>La source
Une source représente l’emplacement d’où proviennent les données, ainsi que leur format. Un package de préparation des données obtient toujours ses données à partir d’un autre flux de données (source de données). C’est ce flux de données de référence qui contient les informations. Chaque source a une expérience utilisateur différente qui permet de la configurer. La source génère une vue « rectangulaire » / tabulaire des données. Si la source de données est initialement non-alignée à droite, la structure est normalisée pour être « rectangulaire ». [L’Annexe 2 fournit la liste des sources actuellement prises en charge](data-prep-appendix2-supported-data-sources.md).

### <a name="the-transform"></a>La transformation ###
Les transformations consomment des données dans un format donné, effectuent une opération sur les données (par exemple changer le type de données) puis génèrent des données dans le nouveau format. Chaque transformation a sa propre interface utilisateur et ses propres comportements. Le chaînage de plusieurs transformations par le biais d’étapes dans le flux de données est le cœur de la fonctionnalité de Préparation des données. [L’Annexe 3 fournit la liste des transformations actuellement prises en charge](data-prep-appendix3-supported-transforms.md).

### <a name="the-inspector"></a>L’inspecteur ###

Les inspecteurs sont des visualisations des données. Ils ont pour but de faciliter la compréhension des données.  Comprendre les données et la qualité des données vous aide à identifier les actions (transformations) qui doivent être effectuées. Certains inspecteurs prennent en charge des actions qui génèrent des transformations. Par exemple, l’inspecteur Value Count (Nombre de valeurs) vous permet de sélectionner une valeur, puis d’appliquer un filtre pour inclure ou exclure cette valeur. Les inspecteurs peuvent également fournir un contexte pour les transformations. Par exemple, le fait de sélectionner une ou plusieurs colonnes change les transformations qui peuvent être appliquées.

Une colonne peut avoir plusieurs inspecteurs à n’importe quel point dans le temps (par exemple, Column Statistics (Statistiques de colonnes) et un histogramme). Il peut également y avoir des instances d’un inspecteur dans plusieurs colonnes. Par exemple, toutes les colonnes numériques peut avoir des histogrammes en même temps.

Les inspecteurs sont affichés dans la section Profiling (Profilage) en bas de l’écran.  Vous pouvez agrandir les inspecteurs pour qu’ils prennent plus de place dans la zone de contenu principale. Vous pouvez considérer la grille de données comme l’inspecteur par défaut. Tout inspecteur peut être étendu dans la zone de contenu principale. Vous pouvez réduire les inspecteurs affichés dans la zone de contenu principale, afin de les afficher dans la section Profilage. Vous pouvez personnaliser un inspecteur en cliquant sur l’icône de crayon dans l’inspecteur. Vous pouvez réorganiser les inspecteurs dans la section inférieure à l’aide d’une opération de glisser-déplacer.

Certains inspecteurs prennent en charge un mode « Halo ». Ce mode affiche la valeur ou l’état en vigueur avant que la dernière transformation ait été appliquée. L’ancienne valeur est affichée en gris, avec la valeur actuelle au premier plan. Cela vous permet de connaître l’impact d’une transformation. [L’Annexe 4 fournit la liste des inspecteurs actuellement pris en charge](data-prep-appendix4-supported-inspectors.md).

### <a name="the-destination"></a>La destination
 Une destination est l’emplacement où vous écrivez/exportez les données une fois que vous les avez préparées dans un flux de données. Un flux de données peut avoir plusieurs destinations. Chaque destination a une expérience utilisateur différente qui permet de la configurer. La destination consomme des données dans un format « rectangulaire » / tabulaire, et les écrit dans un emplacement dans un format donné. [L’Annexe 5 fournit la liste des destinations actuellement prises en charge](data-prep-appendix5-supported-destinations.md).

### <a name="using-data-preparation"></a>Utilisation de la Préparation des données ###
La Préparation des données suit une méthodologie/approche de base en cinq étapes.

#### <a name="step-1-ingestion"></a>Étape 1 : ingestion ####
Importez les données pour la Préparation des données à l’aide de l’option **Add Data Source** (Ajouter une source de données) dans la vue du projet.  Toute l’ingestion initiale des données est gérée par le biais de l’Assistant Source de données.

#### <a name="step-2-understandprofile-the-data"></a>Étape 2 : Comprendre/profiler les données ####

Tout d’abord, examinez la barre de qualité des données en haut de chaque colonne. Le vert représente les lignes qui ont des valeurs. Le gris représente les lignes avec une valeur nulle ou une valeur manquante. Le rouge indique les valeurs d’erreur. Pointez sur la barre pour afficher une info-bulle montrant le nombre exact de lignes dans chacun des trois compartiments. La barre de qualité des données utilise une échelle logarithmique. Veillez donc à toujours vérifier le nombre réel pour obtenir une idée approximative du volume de données manquantes.

![colonnes](media/data-prep-getting-started/columns.png)

Ensuite, utilisez une combinaison des autres inspecteurs et de la grille pour mieux comprendre les caractéristiques des données.  Commencez à formuler des hypothèses sur la préparation des données requise pour une analyse plus approfondie. La plupart des inspecteurs opèrent sur une seule colonne ou sur un petit nombre de colonnes.  

Vous aurez sans doute besoin de plusieurs inspecteurs sur plusieurs colonnes pour comprendre les données. Vous pouvez parcourir les différents inspecteurs dans la section Profilage. Vous pouvez également déplacer des inspecteurs au début de la liste afin de les afficher dans la zone immédiatement visible.

![inspecteurs](media/data-prep-getting-started/inspectors.PNG)

Différents inspecteurs sont fournis pour les variables/colonnes catégorielles et continues. Les options du menu Inspector sont activées et désactivées en fonction du type de colonnes/variables que vous avez.

Quand vous travaillez avec des jeux de données larges qui ont beaucoup de colonnes, nous vous conseillons d’adopter une approche pragmatique lors de l’utilisation des sous-ensembles. Vous devez notamment vous concentrer sur un petit nombre de colonnes (par exemple entre cinq et dix), les préparer, puis passer aux autres colonnes. L’inspecteur de grille prend en charge le partitionnement vertical des colonnes. Ainsi, si vous avez plus de 300 colonnes, vous devez les parcourir de page en page.
 

#### <a name="step-3-transform-the-data"></a>Étape 3 : Transformer les données ####
Les transformations changent les données et autorisent leur exécution afin de prendre en charge l’hypothèse de travail actuelle. Elles sont affichées sous forme d’étapes dans la liste des étapes sur le côté droit. Vous pouvez « voyager dans le temps » parmi la liste des étapes en cliquant sur n’importe quel point arbitraire dans la liste.

Une icône verte à gauche d’une étape donnée indique qu’elle a été exécutée et que les données reflètent l’exécution de la transformation. Une barre verticale à gauche de l’étape indique l’état actuel des données dans les inspecteurs.

![steps](media/data-prep-getting-started/steps.PNG)

Essayez d’apporter de petites modifications fréquentes aux données et de les valider (étape 4) après chaque modification à mesure que l’hypothèse évolue.

#### <a name="step-4-verify-the-impact-of-the-transformation"></a>Étape 4 : Vérifier l’impact de la transformation 
Décidez si l’hypothèse est correcte. Si elle est correcte, développez l’hypothèse suivante et répétez les étapes 2 et 3 pour la nouvelle hypothèse. Si elle est incorrecte, annulez la dernière transformation, développez une nouvelle hypothèse et répétez les étapes 2 et 3.

Pour déterminer si la transformation a eu le bon impact, le meilleur moyen consiste à utiliser des inspecteurs. Utilisez les inspecteurs existants. Utilisez les inspecteurs avec l’effet Halo activé ou lancez plusieurs inspecteurs pour afficher les données à des points donnés dans le temps.

![inspecteur avec Halo](media/data-prep-getting-started/halo1.PNG) ![inspecteur avec Halo](media/data-prep-getting-started/halo2.PNG)

Pour annuler une transformation, accédez à la liste des étapes sur le côté droit de l’interface utilisateur. (Vous devrez peut-être faire disparaître le volet de la liste des étapes. Pour l’ouvrir, cliquez sur le double chevron à gauche). Dans le panneau, sélectionnez la transformation qui a été exécutée et que vous souhaitez annuler. Sélectionnez la liste déroulante sur le côté droit du bloc de l’interface utilisateur. Sélectionnez **Edit** (Modifier) pour apporter des modifications ou **Delete** (Supprimer) pour supprimer la transformation de la liste des étapes et du flux de données.

#### <a name="step-5-output"></a>Étape 5 : sortie 
Une fois que vous avez terminé avec votre préparation des données, vous pouvez écrire le flux de données vers une sortie. Un flux de données peut avoir plusieurs sorties. Dans le menu Transformations, vous pouvez sélectionner le type de sortie souhaité pour le jeu de données. Vous pouvez également sélectionner la destination de la sortie. 

## <a name="list-of-appendices"></a>Liste des annexes 
[Annexe 2 - Sources de données prises en charge](data-prep-appendix2-supported-data-sources.md)  
[Annexe 3 - Transformations prises en charge](data-prep-appendix3-supported-transforms.md)  
[Annexe 4 - Inspecteurs pris en charge](data-prep-appendix4-supported-inspectors.md)  
[Annexe 5 - Destinations prises en charge](data-prep-appendix5-supported-destinations.md)  
[Annexe 6 - Exemples d’expressions de filtre dans Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Annexe 7 - Exemples d’expressions de flux de données de transformation dans Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Annexe 8 - Exemples de sources de données dans Python](data-prep-appendix8-sample-source-connections-python.md)  
[Annexe 9 - Exemples de connexions de destination dans Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Annexe 10 - Exemples de transformations de colonnes dans Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

## <a name="see-also"></a>Voir aussi

[Didacticiel sur la préparation des données avancée](tutorial-bikeshare-dataprep.md)