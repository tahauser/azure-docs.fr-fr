---
title: "Kit de développement logiciel (SDK) et ressources de l’API .NET Table Azure Cosmos DB | Microsoft Docs"
description: "Découvrez l’API Table Azure Cosmos DB, notamment les dates de publication, les dates de retrait et les modifications apportées à chaque version."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 11/20/2017
ms.author: mimig
ms.openlocfilehash: 9dc0f5140a538c3a359dd90b74de822dc163fd70
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2017
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API .NET Table Azure Cosmos DB : Téléchargement et notes de publication
> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [Java](table-sdk-java.md)
> * [Node.JS](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Documentation de l’API**|[Documentation de référence sur l’API .NET](https://aka.ms/acdbtableapiref)|
|**Démarrage rapide**|[Azure Cosmos DB : Créer une application avec .NET et l’API Table](create-table-dotnet.md)|
|**Didacticiel**|[Azure Cosmos DB : Développer avec l’API Table dans .NET](tutorial-develop-table-dotnet.md)|
|**Infrastructure actuellement prise en charge**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> Si vous avez créé un compte d’API Table dans la préversion, créez un [nouveau compte d’API Table](create-table-dotnet.md#create-a-database-account) pour utiliser les Kits de développement logiciels (SDK) mis à la disposition générale pour l’API Table.
>

## <a name="release-notes"></a>Notes de publication

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Version en disponibilité générale

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0 - préversion
* Préversion initiale

## <a name="release-and-retirement-dates"></a>Dates de publication et de retrait
Microsoft envoie une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible. 

Toute requête envoyée à Azure Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé est rejetée par le service.
<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.0.0](#1.0.0) |15 novembre 2017|--- |
| [0.9.0 - préversion](#0.1.0-preview) |11 novembre 2017 |--- |

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous obtenez l’erreur 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

lorsque vous tentez d’utiliser le package Microsoft.Azure.CosmosDB.Table, vous avez deux options pour résoudre le problème :

* Utilisez la console de gestion des packages pour installer le package Microsoft.Azure.CosmosDB.Table et ses dépendances. Pour ce faire, saisissez la commande suivante dans la console du gestionnaire de packages pour votre solution. 
    ```
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```
    
* À l’aide de votre outil de gestion de packages NuGet préféré, installez le package NuGet de Microsoft.Azure.Storage.Common Nuget package avant d’installer Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Forum Aux Questions

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour plus d’informations sur l’API Table Azure Cosmos DB, consultez [Présentation d’Azure Cosmos DB : API Table](table-introduction.md). 
