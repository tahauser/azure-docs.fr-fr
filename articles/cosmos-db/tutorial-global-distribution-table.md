---
title: "Didacticiel de distribution mondiale Azure Cosmos DB pour l’API Table | Microsoft Docs"
description: "Découvrez comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API Table."
services: cosmos-db
keywords: distribution mondiale, Table
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: 40c0bfe913e1396194de00cf6fa1d1ff823b1d0e
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/19/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API Table

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Configurer la distribution mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide de [l’API Table](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Se connecter à une région de prédilection avec l’API Table

Pour tirer parti de la [distribution mondiale](distribute-data-globally.md), les applications clientes peuvent spécifier la liste ordonnée de préférences de régions à utiliser pour effectuer des opérations sur les documents. Pour ce faire, définissez la propriété [TableConnectionPolicy.PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.table.tableconnectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_CosmosDB_Table_TableConnectionPolicy_PreferredLocations). Le Kit SDK de l’API Table d’Azure Cosmos DB sélectionne le point de terminaison qui convient le mieux pour communiquer en fonction de la configuration du compte, de la disponibilité régionale actuelle et de la liste de préférence fournie.

PreferredLocations doit contenir une liste séparée par des virgules des emplacements (de multihébergement) préférés pour les lectures. Chaque instance de client peut spécifier un sous-ensemble de ces régions dans l’ordre de préférence pour des lectures à faible latence. Les régions doivent être nommées à l’aide de leurs [noms d’affichage](https://msdn.microsoft.com/library/azure/gg441293.aspx), par exemple, `West US`.

Toutes les lectures sont envoyées vers la première région disponible dans la liste PreferredLocations. Si la demande échoue, le client passe à la région suivante dans la liste et ainsi de suite.

Le Kit SDK tente des opérations de lecture à partir des régions spécifiées dans PreferredLocations. Ainsi, par exemple, si le compte de base de données est disponible dans trois régions, mais que le client spécifie uniquement deux des régions sans écriture de PreferredLocations, aucune lecture n’est traitée hors de la région d’écriture, même en cas de basculement.

Le Kit SDK envoie automatiquement toutes les écritures vers la région d’écriture active.

Si la propriété PreferredLocations n’est pas définie, toutes les demandes seront traitées par la zone d’écriture en cours.

C’est ici que s’achève ce didacticiel. Découvrez comment gérer la cohérence de votre compte répliqué à l’échelle mondiale en lisant l’article [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md). Pour plus d’informations sur le fonctionnement de la réplication de base de données à l’échelle mondiale dans Azure Cosmos DB, voir [Diffuser des données à l’échelle mondiale avec Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez :

> [!div class="checklist"]
> * Configurer la diffusion mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide des API Table d’Azure Cosmos DB

