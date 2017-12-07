---
title: "Vue d’ensemble de l’API Azure Event Hubs | Microsoft Docs"
description: "Vue d’ensemble des API Azure Event Hubs disponibles"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 3f221a0c-182d-4e39-9f3d-3a3c16c5c6ed
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2017
ms.author: sethm
ms.openlocfilehash: abd44fd0c9cbfab2365b1552e3cd90e84a5348d7
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="available-event-hubs-apis"></a>API Event Hubs disponibles

Cet article décrit l’ensemble des clients API disponibles, que vous pouvez utiliser pour la gestion des ressources Event Hubs.

## <a name="runtime-apis"></a>API de runtime

Vous trouverez ci-dessous une description de tous les clients de runtime Azure Event Hubs actuellement disponibles. Bien que certaines de ces bibliothèques incluent aussi des fonctionnalités de gestion limitées, il existe [des bibliothèques](#management-apis) dédiées aux opérations de gestion. Ces bibliothèques sont principalement destinées à envoyer et recevoir des messages d’un hub d’événements.

Consultez la section [Informations supplémentaires](#additional-information) pour plus de détails sur l’état actuel de chaque bibliothèque de runtime.

| Langue/plateforme | Package client | Package EventProcessorHost | Référentiel |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) | [GitHub](https://github.com/azure/azure-event-hubs-dotnet) |
| .NET Framework | [NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) | N/A |
| Java | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) | [Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) | [GitHub](https://github.com/Azure/azure-event-hubs-java) |
| Nœud | [NPM](https://www.npmjs.com/package/azure-event-hubs) | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-node) |
| C | N/A | N/A | [GitHub](https://github.com/Azure/azure-event-hubs-c) |

### <a name="additional-information"></a>Informations supplémentaires

#### <a name="net"></a>.NET
L’écosystème .NET a plusieurs runtimes, par conséquent, il existe plusieurs bibliothèques .NET pour Event Hubs. La bibliothèque .NET Standard peut être exécutée à l’aide de .NET Core ou de .NET Framework, tandis que la bibliothèque .NET Framework peut uniquement être exécutée dans un environnement .NET Framework. Pour plus d’informations sur .NET Framework, consultez les [versions d’infrastructure](https://docs.microsoft.com/dotnet/articles/standard/frameworks#framework-versions).

#### <a name="node"></a>Nœud

La bibliothèque Node.js est actuellement en version préliminaire et est tenue à jour en parallèle par les employés de Microsoft et des contributeurs externes. Toutes les contributions, y compris le code source sont les bienvenues et seront examinées.

## <a name="management-apis"></a>API de gestion

Vous trouverez ci-dessous une liste de toutes les bibliothèques dédiées à la gestion actuellement disponibles. Aucune de ces bibliothèques ne contient d’opérations de runtime. Elles sont uniquement destinées à la gestion des entités Event Hubs.

| Langue/plateforme | Gestion des packages | Référentiel |
| --- | --- | --- | --- |
| .NET Standard | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) | [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/ResourceManagement/EventHub) |

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble des hubs d’événements](event-hubs-what-is-event-hubs.md)
* [Créer un hub d’événements](event-hubs-create.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)