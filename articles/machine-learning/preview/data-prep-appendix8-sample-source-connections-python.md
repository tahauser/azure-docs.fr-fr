---
title: "Exemples de connexions de données sources supplémentaires avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit un ensemble d’exemples de connexions de données sources possibles avec la préparation des données Azure Machine Learning."
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
ms.date: 02/01/2018
ms.openlocfilehash: 7fbca027d02512671cb380e9b440b03ffef86b89
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="sample-of-custom-source-connections-python"></a>Exemple de connexions sources personnalisées (Python) 
Avant de lire cette annexe, lisez la [présentation de l’extensibilité de Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Charger des données à partir de data.world

### <a name="prerequisites"></a>configuration requise

#### <a name="register-yourself-at-dataworld"></a>Vous inscrire auprès de data.world
Vous avez besoin d’un jeton d’API du site web data.world.

#### <a name="install-dataworld-library"></a>Installer la bibliothèque data.world

Ouvrez l’interface de ligne de commande d’Azure Machine Learning Workbench en sélectionnant **Fichier** > **Ouvrir une interface de ligne de commande**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Ensuite, exécutez `dw configure` sur la ligne de commande et spécifiez votre jeton. Quand vous entrez votre jeton, un répertoire .dw/ est créé dans votre répertoire de base et votre jeton y est stocké.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Vous devriez maintenant pouvoir importer des bibliothèques data.world.

#### <a name="load-data-into-data-preparation"></a>Charger des données dans la préparation des données

Créer un flux de données basé sur un script. Ensuite, utilisez le script suivant pour charger les données à partir de data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Azure Cosmos DB en tant que connexion de source de données
Pour obtenir un exemple d’Azure Cosmos DB en tant que connexion de données, lisez [Load Azure Cosmos DB as a source data connection](data-prep-load-azure-cosmos-db.md) (Charger Azure Cosmos DB en tant que connexion de source de données)
