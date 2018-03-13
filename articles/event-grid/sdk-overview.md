---
title: Kits SDK Azure Event Grid
description: "Décrit les Kits de développement logiciel (SDK) d’Azure Event Grid. Ils assurent la gestion, la publication et la consommation."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9c56e4c3314090ad55017d5c681a0cfd7bf5722c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Kits SDK Event Grid de gestion et de publication

Event Grid fournit des Kits de développement logiciel (SDK) qui permettent de gérer ses ressources et de publier (POST) des événements par programme.

## <a name="management-sdks"></a>Kits SDK de gestion

Les Kits SDK de gestion permettent de créer, de mettre à jour et de supprimer des abonnements et des rubriques Event Grid. Voici les Kits SDK actuellement disponibles :

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="publish-sdks"></a>Kits SDK de publication

Les Kits SDK de publication permettent de publier (POST) des événements sur des rubriques tout en gérant l’authentification, la formation de l’événement et la publication asynchrone sur le point de terminaison spécifié. Voici les Kits SDK actuellement disponibles :

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation d’Event Grid, consultez l’article [What is Event Grid?](overview.md) (Présentation d’Event Grid).
* Pour connaître les commandes Event Grid dans Azure CLI, consultez la section [Azure CLI](/cli/azure/eventgrid).
* Pour connaître les commandes Event Grid dans PowerShell, consultez la section [PowerShell](/powershell/module/azurerm.eventgrid).