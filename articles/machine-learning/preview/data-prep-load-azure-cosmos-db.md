---
title: "Connexion à Azure Cosmos DB en tant que source de données dans Azure Machine Learning Workbench | Microsoft Docs"
description: "Ce document présente un exemple de connexion à Azure Cosmos DB via Azure Machine Learning Workbench"
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: d36b394a528dc4bc1b6e0a9e0e5dbde728cbee1b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2018
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Connexion à Azure Cosmos DB en tant que source de données
Cet article contient un exemple Python qui permet de vous connecter à Cosmos DB dans Azure Machine Learning Workbench.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Charger des données Azure Cosmos DB dans la préparation des données

Créez un flux de données basé sur un script, puis utilisez le script suivant pour charger les données à partir d’Azure Cosmos DB. 

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

## <a name="other-data-source-connections"></a>Connexions à d’autres sources de données
Pour obtenir d’autres exemples, consultez [Exemple de connexions à des sources de données supplémentaires](data-prep-appendix8-sample-source-connections-python.md).
