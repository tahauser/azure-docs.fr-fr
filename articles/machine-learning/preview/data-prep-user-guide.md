---
title: "Guide d’utilisation détaillé Azure Machine Learning Data Preparations | Microsoft Docs"
description: "Ce document explique de manière globale et en détail comment résoudre les problèmes de données avec Azure Machine Learning Data Preparations"
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
ms.openlocfilehash: 9fc0ae8929f12447123321a5c64ac89309c68dca
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparations-user-guide"></a>Guide de l’utilisateur Data Preparations 
L’expérience Azure Machine Learning Data Preparations fournit de nombreuses fonctionnalités enrichies. Cet article décrit les parties plus avancées de l’expérience.

### <a name="step-execution-history-and-caching"></a>Exécution des étapes, historique et mise en cache 
L’historique des étapes Data Preparations tient à jour une série de caches pour des raisons de performances. Si vous sélectionnez une étape et accédez à un cache, l’étape n’est pas réexécutée. Si vous avez un bloc d’écriture à la fin de l’historique des étapes et parcourez les étapes d’avant en arrière mais sans apporter aucune modification, l’écriture n’est pas déclenchée après la première fois. Une nouvelle écriture se produit et remplace la précédente si vous :

- apportez des modifications au bloc d’écriture ;
- ajoutez un bloc de transformation et le déplacez au-dessus du bloc d’écriture, générant ainsi une invalidation du cache ;
- modifiez les propriétés d’un bloc au-dessus du bloc d’écriture, générant ainsi une invalidation du cache ;
- sélectionnez l’actualisation d’un échantillon (invalidant ainsi la totalité des caches).

### <a name="error-values"></a>Valeurs d’erreur

Il est possible que des transformations de données échouent pour une valeur d’entrée, car cette valeur ne peut pas être gérée correctement. Par exemple, en cas d’opération de forçage de type, le forçage échoue si la valeur de la chaîne d’entrée ne peut pas être convertie en type de cible spécifié. Il se peut qu’une opération de forçage de type convertisse une colonne de type chaîne en un type numérique ou booléen, ou tente de dupliquer une colonne qui n’existe pas. Cette défaillance résulte du déplacement de l’opération *supprimer la colonne X* avant l’opération *dupliquer la colonne X*.

Dans ces cas-là, Data Preparations génère une valeur d’erreur comme sortie. Les valeurs d’erreur indiquent qu’une opération précédente a échoué pour la valeur donnée. En interne, elles sont traitées comme un type valeur de première classe, mais leur présence ne modifie pas le type sous-jacent d’une colonne, même si une colonne contient uniquement des valeurs d’erreur.

Les valeurs d’erreur sont faciles à identifier. Elles sont mises en surbrillance en rouge et portent la mention « Erreur ». Pour déterminer la cause de l’erreur, placez le pointeur de la souris sur une valeur d’erreur afin d’afficher une description de la défaillance.

Les valeurs d’erreur se propagent. Une fois qu’une valeur d’erreur s’est produite, elle se propage dans la plupart des cas en tant qu’erreur dans la plupart des opérations. Il existe trois façons de les remplacer ou de les supprimer :

* Replace
    -  Cliquez avec le bouton droit sur une colonne et sélectionnez **Replace Error Values** (Remplacer les valeurs d’erreur). Vous pouvez ensuite choisir une valeur de remplacement pour chaque valeur d’erreur figurant dans la colonne.

* Supprimer
    - Data Preparations inclut des filtres interactifs pour conserver ou supprimer des valeurs d’erreur.
    - Cliquez avec le bouton droit sur une colonne, puis sélectionnez **Filtrer la colonne**. Pour conserver ou supprimer des valeurs d’erreur, créez un filtre conditionnel avec la condition *« is error »* ou *« is not error »*.

* Utilisez une expression Python pour opérer de manière conditionnelle sur des valeurs d’erreur. Pour plus d’informations, consultez la [section sur les extensions Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>échantillonnage
Un fichier Sources de données accepte des données brutes à partir d’une ou plusieurs sources, à partir du système de fichiers local ou d’un emplacement distant. Le bloc Échantillon vous permet de spécifier s’il faut utiliser un sous-ensemble des données en générant des échantillons. Le fait d’opérer sur un échantillon de données plutôt que sur un jeu de données volumineux entraîne souvent une amélioration des performances lors de l’exécution des opérations des étapes ultérieures.

Pour chaque fichier Sources de données, plusieurs échantillons peuvent être générés et stockés. Cependant, un seul échantillon peut être défini comme échantillon actif. Vous pouvez créer, modifier ou supprimer des échantillons dans l’Assistant Source de données ou en modifiant le bloc Échantillon. Tout fichier Data Preparations qui référence une source de données utilise implicitement l’échantillon spécifié dans le fichier Sources de données.

Plusieurs stratégies d’échantillonnage sont disponibles, chacune avec différents paramètres configurables.

#### <a name="top"></a>Top (Premiers)
Vous pouvez appliquer cette stratégie à des fichiers locaux ou distants. Elle prend les N premières lignes (spécifiées par Count) dans la source de données.

#### <a name="random-n"></a>Random N 
Vous pouvez appliquer cette stratégie uniquement à des fichiers locaux. Elle prend N lignes aléatoires (spécifiées par Count) dans la source de données. Vous pouvez fournir une valeur de départ spécifique pour être certain que le même échantillon est généré si la valeur Count est identique.

#### <a name="random-"></a>% aléatoire 
Vous pouvez appliquer cette stratégie à des fichiers locaux ou distants. Dans les deux cas, vous devez fournir une probabilité et une valeur de départ, comme avec la stratégie Random N.

Pour les échantillons de fichiers distants, vous devez fournir des paramètres supplémentaires :

- Générateur d’échantillon 
  - Sélectionnez un cluster Spark ou une cible de calcul Docker distante à utiliser pour la génération d’échantillon. Afin d’apparaître dans cette liste, la cible de calcul doit être créée au préalable pour le projet. Pour créer des cibles de calcul, suivez les étapes fournies dans la section Créer une cible de calcul de l’article [Guide pratique pour utiliser des GPU dans Azure Machine Learning](how-to-use-gpu.md).
- Stockage de l’échantillon 
  - Spécifiez un emplacement de stockage intermédiaire pour stocker l’échantillon distant. Ce chemin doit être un répertoire différent de celui de l’emplacement du fichier d’entrée.

#### <a name="full-file"></a>Full file 
Vous pouvez appliquer cette stratégie uniquement à des fichiers locaux. Le fichier complet est inséré dans la source de données. Si le fichier est trop volumineux, cette option peut ralentir les opérations ultérieures dans l’application. Il peut s’avérer plus approprié d’utiliser une stratégie d’échantillonnage différente.


### <a name="fork-merge-and-append"></a>Dupliquer, fusionner et ajouter

Quand vous appliquez un filtre à un jeu de données, l’opération fractionne les données en deux jeux de résultats : un qui représente les enregistrements remplissant les conditions du filtre et un autre pour les enregistrements qui échouent. Dans les deux cas, l’utilisateur peut choisir le jeu de résultats à afficher. Il peut ignorer l’autre jeu de données ou le placer dans un nouveau flux de données. Cette dernière option porte le nom de duplication.

Pour effectuer une duplication : 
1. Sélectionnez une colonne, cliquez avec le bouton droit et sélectionnez **Filtrer la colonne**.

2. Sous **Je veux**, sélectionnez**Conserver les lignes** pour afficher le jeu de résultats qui remplit les conditions du filtre.

3. Sélectionnez **Supprimer les lignes** pour afficher le jeu ayant échoué.

4. Après **Conditions**, sélectionnez **Create Dataflow Containing the Filtered Out Rows** (Créer un flux de données contenant les lignes filtrées) pour dupliquer le jeu de résultats non affiché dans un nouveau flux de données.


Cette pratique a souvent pour but de séparer un jeu de données qui nécessite une préparation supplémentaire. Une fois que vous avez préparé le jeu de données dupliqué, il est courant de fusionner les données avec le jeu de résultats dans le flux de données d’origine. Pour effectuer une fusion (c’est-à-dire l’inverse d’une opération de duplication), exécutez l’une des actions suivantes :

- **Ajouter des lignes**. Fusionner plusieurs flux de données verticalement (d’après les lignes). 
- **Ajouter des colonnes**. Fusionner plusieurs flux de données horizontalement (d’après les colonnes).


>[!NOTE]
>L’ajout de colonnes échoue en cas de conflit de colonne.


Après une opération de fusion, un ou plusieurs flux de données sont référencés par un flux de données source. Data Preparations émet une notification dans le coin inférieur droit de l’application, sous la liste des étapes.


Toute opération sur le flux de données référencé exige que le flux de données parent actualise l’échantillon utilisé à partir du flux de données référencé. Dans ce cas, une boîte de dialogue de confirmation remplace la notification de référence de flux de données dans le coin inférieur droit. Cette boîte de dialogue confirme que vous devez actualiser le flux de données pour synchroniser les modifications apportées à tout flux de données de dépendance.

### <a name="list-of-appendices"></a>Liste des annexes 
* [Sources de données prises en charge](data-prep-appendix2-supported-data-sources.md)  
* [Transformations prises en charge](data-prep-appendix3-supported-transforms.md)  
* [Inspecteurs pris en charge](data-prep-appendix4-supported-inspectors.md)  
* [Destinations prises en charge](data-prep-appendix5-supported-destinations.md)  
* [Exemples d’expressions de filtre dans Python](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Exemples d’expressions de flux de données de transformation dans Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Exemples de sources de données dans Python](data-prep-appendix8-sample-source-connections-python.md)  
* [Exemples de connexions de destination dans Python](data-prep-appendix9-sample-destination-connections-python.md)  
* [Exemples de transformations de colonne dans Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
