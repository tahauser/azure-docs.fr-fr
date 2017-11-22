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
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 93e75429d66a30bfc4588a3070e32d58eec0df4b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Comment configurer la distribution mondiale Azure Cosmos DB à l’aide de l’API Table

Cet article décrit les tâches suivantes : 

> [!div class="checklist"]
> * Configurer la distribution mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide de [l’API Table](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Se connecter à une région de prédilection avec l’API Table

Pour tirer parti de la [distribution mondiale](distribute-data-globally.md), les applications clientes peuvent spécifier la liste ordonnée de préférences de régions à utiliser pour effectuer des opérations sur les documents. Pour ce faire, définissez la valeur de configuration `TablePreferredLocations` dans la configuration de l’application du Kit de développement logiciel (SDK) de l’API Table d’Azure Cosmos DB. Le Kit de développement logiciel (SDK) de l’API Table d’Azure Cosmos DB sélectionnera le point de terminaison qui convient le mieux pour communiquer en fonction de la configuration du compte, de la disponibilité régionale actuelle et de la liste de préférence fournie.

Le paramètre `TablePreferredLocations` doit contenir une liste séparée par des virgules des emplacements (de multihébergement) préférés pour les lectures. Chaque instance de client peut spécifier un sous-ensemble de ces régions dans l’ordre de préférence pour des lectures à faible latence. Les régions doivent être nommées à l’aide de leurs [noms d’affichage](https://msdn.microsoft.com/library/azure/gg441293.aspx), par exemple, `West US`.

Toutes les lectures sont envoyées vers la première région disponible dans la liste `TablePreferredLocations`. Si la demande échoue, le client passe à la région suivante dans la liste et ainsi de suite.

Le SDK tente des opérations de lecture à partir des régions spécifiées dans `TablePreferredLocations`. Ainsi, par exemple, si le compte de base de données est disponible dans trois régions, mais que le client spécifie uniquement deux des régions sans écriture de `TablePreferredLocations`, aucune lecture n’est traitée hors de la région d’écriture, même en cas de basculement.

Le SDK envoie automatiquement toutes les écritures vers la région d’écriture en cours.

Si la propriété `TablePreferredLocations` n’est pas définie, toutes les demandes seront traitées par la zone d’écriture en cours.

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

C’est ici que s’achève ce didacticiel. Découvrez comment gérer la cohérence de votre compte répliqué à l’échelle mondiale en lisant l’article [Niveaux de cohérence dans Azure Cosmos DB](consistency-levels.md). Pour plus d’informations sur le fonctionnement de la réplication de base de données à l’échelle mondiale dans Azure Cosmos DB, voir [Diffuser des données à l’échelle mondiale avec Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez effectué les tâches suivantes :

> [!div class="checklist"]
> * Configurer la distribution mondiale à l’aide du portail Azure
> * Configurer la distribution mondiale à l’aide des API Table d’Azure Cosmos DB

