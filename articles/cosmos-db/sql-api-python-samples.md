---
title: "Exemples Python d’API SQL pour Azure Cosmos DB | Microsoft Docs"
description: "Recherchez des exemples Python sur GitHub pour les tâches courantes dans Azure Cosmos DB, y compris les opérations CRUD."
keywords: Exemples Python
services: cosmos-db
author: moderakh
manager: jhubbard
editor: monicar
documentationcenter: python
ms.assetid: 7f4f8db3-e9db-4645-92ef-7819d486a349
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2016
ms.author: moderakh
ms.openlocfilehash: 7f6300b5105b6b54cc27cde4cb95234d7dbf6a98
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="azure-cosmos-db-python-examples"></a>Exemples Python pour Azure Cosmos DB
> [!div class="op_single_selector"]
> * [Exemples .NET](sql-api-dotnet-samples.md)
> * [Exemples Node.js](sql-api-nodejs-samples.md)
> * [Exemples Python](sql-api-python-samples.md)
> * [Galerie d’exemples de code Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Le référentiel GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python/tree/master/samples) contient des exemples de solutions qui exécutent des opérations CRUD, ainsi que d’autres opérations courantes sur les ressources Azure Cosmos DB. Cet article fournit :

* Liens vers les tâches dans chacun des exemples de fichiers de projet Python. 
* Des liens vers le contenu de référence d’API connexe.

**Composants requis**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Vous pouvez [activer les avantages de votre abonnement Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): ce dernier vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Vous devez également disposer du [Kit de développement logiciel (SDK) Python](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Chaque exemple est autonome, se définit lui-même et se nettoie automatiquement. Les exemples transmettent donc plusieurs appels à [document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html). À chaque appel, votre abonnement est facturé pour une heure d’utilisation selon le niveau de performances de la collection en cours de création. 
   > 
   > 

## <a name="database-examples"></a>Exemples de base de données
Le fichier [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement/Program.py) du projet [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/DatabaseManagement) explique comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Créer une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L65-L76) |[document_client.CreateDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Demander un compte pour une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L49-L62) |[document_client.QueryDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Lire une base de données par identifiant](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L79-L96) |[document_client.ReadDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Répertorier les bases de données pour un compte](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L99-L110) |[document_client.ReadDatabases](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |
| [Supprimer une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/DatabaseManagement/Program.py#L113-L126) |[document_client.DeleteDatabase](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html) |

## <a name="collection-examples"></a>Exemples de collection
Le fichier [Program.py](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement/Program.py) du projet [CollectionManagement](https://github.com/Azure/azure-documentdb-python/tree/master/samples/CollectionManagement) explique comment effectuer les tâches suivantes :

| Tâche | Informations de référence sur l'API |
| --- | --- |
| [Création d'une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L84-L135) |[document_client.CreateCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Lire une liste de toutes les collections d’une base de données](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L198-L225) |[document_client.ListCollections](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Obtenir une collection par identifiant](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L178-L195) |[document_client.ReadCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Obtenir le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L139-L161) |[DocumentQueryable.QueryOffers](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Modifier le niveau de performances d’une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L163-L175) |[document_client.ReplaceOffer](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |
| [Supprimer une collection](https://github.com/Azure/azure-documentdb-python/blob/d78170214467e3ab71ace1a7400f5a7fa5a7b5b0/samples/CollectionManagement/Program.py#L212-L225) |[document_client.DeleteCollection](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.document_client.html#CreateCollection) |

