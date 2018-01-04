---
title: "Exemples de destinations/sorties possibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit un ensemble d’exemples de destinations/sorties de données personnalisées avec la préparation des données Azure Machine Learning."
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
ms.date: 09/11/2017
ms.openlocfilehash: 45522e23b592bfa7be2be22ef4ac7ef4713b79bf
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="sample-of-destination-connections-python"></a>Exemple de connexions de destination (Python) 
Avant de lire cette annexe, lisez la [présentation de l’extensibilité de Python](data-prep-python-extensibility-overview.md).


## <a name="write-to-excel"></a>Écriture dans Excel 


L’écriture dans Excel nécessite une bibliothèque supplémentaire. L’ajout de nouvelles bibliothèques est documenté dans la vue d’ensemble de l’extensibilité. `openpyxl` est la bibliothèque que vous devez ajouter.

Avant d’écrire dans Excel, d’autres modifications peuvent être nécessaires. Certains des types de données utilisés dans la préparation de données ne sont pas pris en charge dans certains formats de destination. Par exemple, si des objets « Erreur » existent, ils ne sont pas sérialisés correctement dans Excel. Par conséquent, avant de tenter d’écrire dans Excel, vous avez besoin d’une transformation « Remplacer les valeurs d’erreur », qui supprime les erreurs des colonnes.

Si toutes les tâches ci-dessus ont été effectuées, la ligne suivante écrit la table de données sur une même feuille dans un document Excel. Ajoutez une transformation Écriture d’un flux de données (Script). Ensuite, entrez le code suivant dans la section d’une expression.


### <a name="on-windows"></a>Sur Windows 
```python
df.to_excel('c:\dev\data\Output\Customer.xlsx', sheet_name='Sheet1')
```

### <a name="on-macosos-x"></a>Sur macOS/OS X ###
```python
df.to_excel('c:/dev/data/Output/Customer.xlsx', sheet_name='Sheet1')
```
