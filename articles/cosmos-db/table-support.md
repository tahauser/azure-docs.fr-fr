---
title: Prise en charge de Stockage Table Azure dans Azure Cosmos DB | Microsoft Docs
description: "Découvrez comment collaborent l’API Table Azure Cosmos DB et Stockage Table Azure."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: c0c8f1aee75c4ee5cc35758b71ef573637fd3edd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Développement avec l’API Table Azure Cosmos DB et Stockage Table Azure

L’API Table Azure Cosmos DB et Stockage Table Azure partagent le même modèle de données de table et exposent les mêmes opérations de création, suppression, mise à jour et interrogation par le biais de leurs SDK. 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Développement avec l’API Table Azure Cosmos DB

À ce stade, [l’API Table de Azure Cosmos DB](table-introduction.md) a quatre kits disponibles pour le développement : 
- Kit de développement logiciel (SDK) .NET [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget). Cette bibliothèque présente les mêmes classes et signatures de méthode que le [SDK Stockage Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage) public, mais elle peut également se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table. 
- [Kit de développement logiciel (SDK) Python](table-sdk-python.md). Le nouveau SDK Azure Cosmos DB Python est le seul SDK qui prend en charge le stockage Table Azure dans Python. Ce kit de développement logiciel se connecte à la fois avec l’API Table Azure Cosmos DB et le Stockage Table Azure.
- [Kit SDK Java](table-sdk-java.md). Ce kit de développement pour le stockage Azure a la possibilité de se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table.
- [Kit de développement logiciel (SDK) Node.js](table-sdk-nodejs.md). Ce kit de développement pour le stockage Azure a la possibilité de se connecter à des comptes Azure Cosmos DB à l’aide de l’API Table.

Des informations supplémentaires sur l’utilisation de l’API Table sont disponibles dans l’article [FAQ : Développer avec l’API Table](faq.md#develop-with-the-table-api).

## <a name="developing-with-the-azure-table-storage"></a>Développement avec Stockage Table Azure

[Stockage Table Azure](table-storage-overview.md) met à votre disposition de nombreux SDK et didacticiels, qui se trouvent désormais dans la section [Stockage Table Azure](table-storage-overview.md). Ces articles sont mis à jour à mesure que l’interopérabilité entre les SDK Stockage Table Azure et les API Table Azure Cosmos DB devient disponible.  





