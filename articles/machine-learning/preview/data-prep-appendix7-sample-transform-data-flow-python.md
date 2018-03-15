---
title: "Exemples de transformations de flux de données possibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit un ensemble d’exemples de transformations de flux de données possibles avec la préparation des données Azure Machine Learning."
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
ms.openlocfilehash: aa213a3b1a8949f0fca5e4bbb7ec5a6a775ae6ec
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="sample-of-custom-data-flow-transforms-python"></a>Exemple de transformations de flux de données personnalisées (Python) 
Le nom de la transformation dans le menu est **Transformation d’un flux de données (Script)**. Avant de lire cette annexe, lisez la [présentation de l’extensibilité de Python](data-prep-python-extensibility-overview.md).

## <a name="transform-frame"></a>Cadre de transformation
### <a name="create-a-new-column-dynamically"></a>Créer une colonne de manière dynamique 
Crée une colonne de manière dynamique (**city2**) et rapproche plusieurs versions différentes de San Francisco par rapport à la colonne « city » existante.
```python
    df.loc[(df['city'] == 'San Francisco') | (df['city'] == 'SF') | (df['city'] == 'S.F.') | (df['city'] == 'SAN FRANCISCO'), 'city2'] = 'San Francisco'
```

### <a name="add-new-aggregates"></a>Ajouter de nouveaux agrégats
Crée un cadre avec les premier et dernier agrégats calculés pour la colonne « score ». Ils sont regroupés par la colonne **risk_category**.
```python
    df = df.groupby(['risk_category'])['Score'].agg(['first','last'])
```
### <a name="winsorize-a-column"></a>Utiliser la méthode d'estimation de Winsor pour une colonne 
Reformule les données afin de réduire le nombre de valeurs hors norme dans une colonne.
```python
    import scipy.stats as stats
    df['Last Order'] = stats.mstats.winsorize(df['Last Order'].values, limits=0.4)
```

## <a name="transform-data-flow"></a>Transformer le flux de données
### <a name="fill-down"></a>Recopier en bas 

L’option de remplissage vers le bas requiert deux transformations, en partant du principe que les données ressemblent au tableau suivant :

|État         |City       |
|--------------|-----------|
|Washington    |Redmond    |
|              |Bellevue   |
|              |Issaquah   |
|              |Seattle    |
|Californie    |Los Angeles|
|              |San Diego  |
|              |San Jose   |
|Texas         |Dallas     |
|              |San Antonio|
|              |Houston    |

1. Créez une transformation « Ajouter une colonne (script) » en utilisant le code suivant :
```python
    row['State'] if len(row['State']) > 0 else None
```

2. Créez une transformation « Transformer le flux de données (script) » qui contient le code suivant :
```python
    df = df.fillna( method='pad')
```

Les données ressemblent maintenant au tableau suivant :

|État         |newState         |City       |
|--------------|--------------|-----------|
|Washington    |Washington    |Redmond    |
|              |Washington    |Bellevue   |
|              |Washington    |Issaquah   |
|              |Washington    |Seattle    |
|Californie    |Californie    |Los Angeles|
|              |Californie    |San Diego  |
|              |Californie    |San Jose   |
|Texas         |Texas         |Dallas     |
|              |Texas         |San Antonio|
|              |Texas         |Houston    |


### <a name="min-max-normalization"></a>Normalisation Min Max
```python
    df["NewCol"] = (df["Col1"]-df["Col1"].mean())/df["Col1"].std()
```