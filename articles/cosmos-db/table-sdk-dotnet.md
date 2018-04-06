---
title: Kit de développement logiciel (SDK) et ressources de l’API .NET Table Azure Cosmos DB | Microsoft Docs
description: Découvrez l’API Table Azure Cosmos DB, notamment les dates de publication, les dates de retrait et les modifications apportées à chaque version.
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/26/2018
ms.author: mimig
ms.openlocfilehash: 2afd7df65e7b223845752fc6bea5bc0ab4d3efd8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
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
|**Infrastructure actuellement prise en charge**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Si vous avez créé un compte d’API Table dans la préversion, créez un [nouveau compte d’API Table](create-table-dotnet.md#create-a-database-account) pour utiliser les Kits de développement logiciels (SDK) mis à la disposition générale pour l’API Table.
>

## <a name="release-notes"></a>Notes de publication

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Validation supplémentaire pour les ETAGs incorrectement formés en mode direct.
* Bogue de requête LINQ corrigé en mode de passerelle.
* API synchrones désormais exécutées sur le pool de threads avec SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Add TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism and TableQueryContinuationTokenLimitInKb to TableRequestOptions
* Résolution des bogues

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Version en disponibilité générale

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0 - préversion
* Préversion initiale

## <a name="release-and-retirement-dates"></a>Dates de publication et de retrait
Microsoft envoie une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

L’utilisation de la préversion du package [WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) a été déconseillée ; il doit être remplacé par le package [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table). Le kit de développement logiciel (SDK) WindowsAzure.Storage-PremiumTable sera retiré le 15 novembre 2018 ; à compter de cette date, les requêtes envoyées au kit de développement logiciel (SDK) ne seront plus autorisées.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible. 

Toute requête envoyée à Azure Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé est rejetée par le service.
<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.1.1](#1.1.1) |26 mars 2018|--- |
| [1.1.0](#1.1.0) |21 février 2018|--- |
| [1.0.0](#1.0.0) |15 novembre 2017|--- |
| [0.9.0 - préversion](#0.9.0-preview) |11 novembre 2017 |--- |

## <a name="troubleshooting"></a>Résolution de problèmes

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
