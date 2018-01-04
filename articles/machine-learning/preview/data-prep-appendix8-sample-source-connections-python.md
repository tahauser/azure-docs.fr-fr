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
ms.date: 09/11/2017
ms.openlocfilehash: 3ffe0e385f9dd71d8341305f42ceb10e0ea49af4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
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

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Charger des données Azure Cosmos DB dans la préparation des données

Créez un flux de données basé sur un script, puis utilisez le script suivant pour charger les données à partir d’Azure Cosmos DB. (Vous devez d’abord installer les bibliothèques. Pour plus d’informations, consultez le lien vers le document de référence précédent.)

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```
