---
title: 'Azure Cosmos DB : API, SDK et ressources du processeur de flux de modification .NET | Microsoft Docs'
description: Découvrez l’API et le kit SDK du processeur de flux de modification, notamment les dates de lancement, les dates de suppression et les modifications apportées entre chaque version du kit SDK du processeur de flux de modification .NET.
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/07/2018
ms.author: maquaran
ms.openlocfilehash: 7e53cf67bc6e03a5f45de5ad4e23ff91624874f4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>Kit SDK du processeur de flux de modification .NET : téléchargement et notes de publication
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Flux de modification .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java asynchrone](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [API REST Resource Provider](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

|   |   |
|---|---|
|**Téléchargement du Kit de développement logiciel (SDK)**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**Documentation de l’API**|[Documentation de référence de l’API de la bibliothèque du processeur de flux de modification](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Prise en main**|[Prise en main du kit SDK du processeur de flux de modification .NET](change-feed.md)|
|**Infrastructure actuellement prise en charge**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Notes de publication

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Améliorations de la stabilité.
* Prise en charge des points de contrôle manuels.
* Compatible avec les versions 1.21 et version ultérieure du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Ajout de la prise en charge de .NET Standard 2.0. Le package prend désormais en charge les monikers d’infrastructure `netstandard2.0` et `net451`.
* Compatible avec les versions 1.17.0 et supérieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).
* Compatible avec les versions 1.5.1 et supérieures du [Kit de développement logiciel (SDK) principal SQL .NET](sql-api-sdk-dotnet-core.md).

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Résout un problème relatif au calcul de l’estimation du travail restant lorsque le flux de modification était vide ou qu’aucun travail n’était en attente.
* Compatible avec les versions 1.13.2 et supérieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Ajout d’une méthode pour obtenir une estimation du travail restant à traiter dans le flux de modification.
* Compatible avec les versions 1.13.2 et supérieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Kit SDK GA
* Compatible avec les versions 1.14.1 et inférieures du [Kit de développement logiciel (SDK) SQL .NET](sql-api-sdk-dotnet.md).

## <a name="release--retirement-dates"></a>Dates de lancement et de suppression
Microsoft fournira une notification au moins **12 mois** avant le retrait d’un Kit de développement logiciel (SDK) pour faciliter la transition vers une version plus récente/prise en charge.

Les nouvelles fonctionnalités et fonctions, et les optimisations sont uniquement ajoutées au Kit SDK actuel. Par conséquent, il est recommandé de toujours passer à la dernière version du SDK dès que possible. 

Le service rejette toute requête envoyée à Cosmos DB à l’aide d’un Kit de développement logiciel (SDK) supprimé.

<br/>

| Version | Date de lancement | Date de suppression |
| --- | --- | --- |
| [1.3.0](#1.3.0) |07 mars 2018 |--- |
| [1.2.0](#1.2.0) |31 octobre 2017 |--- |
| [1.1.1](#1.1.1) |29 août 2017 |--- |
| [1.1.0](#1.1.0) |13 août 2017 |--- |
| [1.0.0](#1.0.0) |7 juillet 2017 |--- |


## <a name="faq"></a>Forum Aux Questions
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur Cosmos DB, consultez la page du service [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

