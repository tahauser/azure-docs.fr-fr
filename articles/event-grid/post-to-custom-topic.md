---
title: "Publier un événement dans une rubrique Azure Event Grid personnalisée"
description: "Explique comment publier un événement dans une rubrique personnalisée pour Azure Event Grid"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 43dcdf9ab0fee5f7e61ecdc42aaf40430e272d92
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Publier dans une rubrique personnalisée pour Azure Event Grid

Cet article explique comment publier un événement dans une rubrique personnalisée. Il montre le format des données de publication et d’événement. Le [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) s’applique uniquement aux publications qui correspondent au format attendu.

## <a name="endpoint"></a>Point de terminaison

Quand vous envoyez la demande HTTP POST à une rubrique personnalisée, utilisez le format d’URI `https://<topic-endpoint>?api-version=2018-01-01`.

Par exemple, un URI valide est `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Pour obtenir le point de terminaison pour une rubrique personnalisée avec l’interface de ligne de commande Azure, utilisez :

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Pour obtenir le point de terminaison pour une rubrique personnalisée avec Azure PowerShell, utilisez :

```powershell
(Get-AzureRmEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>En-tête

Dans la demande, incluez une valeur d’en-tête nommée `aeg-sas-key` qui contient une clé pour l’authentification.

Par exemple, une valeur d’en-tête valide est `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Pour obtenir la clé pour une rubrique personnalisée avec l’interface de ligne de commande Azure, utilisez :

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Pour obtenir la clé pour une rubrique personnalisée avec PowerShell, utilisez :

```powershell
(Get-AzureRmEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Données d’événement

Pour les rubriques personnalisées, les données de premier niveau contiennent les mêmes champs que les événements standard définis par les ressources. Une de ces propriétés est une propriété de données qui contient des propriétés propres à la rubrique personnalisée. En tant qu’éditeur d’événements, vous déterminez les propriétés de cet objet de données. Utilisez le schéma suivant :

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Pour obtenir une description de ces propriétés, consultez [Schéma d’événement Azure Event Grid](event-schema.md).

Par exemple, un schéma de données d’événement valide est :

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="next-steps"></a>Étapes suivantes

* Pour une présentation de l’acheminement des événements personnalisés, consultez [Créer et acheminer des événements personnalisés avec Azure CLI et Event Grid](custom-event-quickstart.md) ou [Créer et acheminer des événements personnalisés avec Azure PowerShell et Event Grid](custom-event-quickstart-powershell.md).
* Pour plus d’informations sur la clé d’authentification, consultez la page [Sécurité et authentification Azure Event Grid](security-authentication.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
