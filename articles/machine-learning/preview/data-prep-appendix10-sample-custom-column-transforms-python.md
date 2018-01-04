---
title: "Exemple Python pour dériver de nouvelles colonnes dans la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit des exemples de code Python pour la création de colonnes dans la préparation des données Azure Machine Learning."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: e915c936af65bc9cac591d1fc011351b1720bb5b
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="sample-of-custom-column-transforms-python"></a>Exemple de transformations de colonnes personnalisées (Python) 
Le nom de cette transformation dans le menu est **Add Column (Script)**.

Avant de lire cette annexe, lisez la [présentation de l’extensibilité de Python](data-prep-python-extensibility-overview.md).

## <a name="test-equivalence-and-replace-values"></a>Tester l’équivalence et remplacer les valeurs 
Si la valeur de Col1 est inférieure à 4, la nouvelle colonne doit avoir la valeur 1. Si la valeur dans Col1 est supérieure à 4, la nouvelle colonne affiche la valeur 2. 

```python
    1 if row["Col1"] < 4 else 2
```
## <a name="current-date-and-time"></a>Date et heure actuelles 

```python
    datetime.datetime.now()
```
## <a name="typecasting"></a>Cast de type 
```python
    float(row["Col1"]) / float(row["Col2"] - 1)
```
## <a name="evaluate-for-nullness"></a>Évaluer le caractère null 
Si Col1 contient une valeur null, marquer la nouvelle colonne comme étant **incorrecte**. Sinon, la marquer comme **bonne**. 

```python
    'Bad' if pd.isnull(row["Col1"]) else 'Good'
```
## <a name="new-computed-column"></a>Nouvelle colonne calculée 
```python
    np.log(row["Col1"])
```
## <a name="epoch-computation"></a>Calcul de l’époque 
Nombre de secondes depuis l’époque Unix (en supposant que Col1 est déjà une date) : 
```python
    row["Col1"] - datetime.datetime.utcfromtimestamp(0)).total_seconds()
```





