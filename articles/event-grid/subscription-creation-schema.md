---
title: "Schéma d’abonnement à Azure Event Grid"
description: "Décrit les propriétés d’abonnement à un événement avec Azure Event Grid."
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: babanisa
ms.openlocfilehash: a915473c67a7577582837b56d1a9ccec4d21c461
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-subscription-schema"></a>Schéma d’abonnement à Event Grid

Pour créer un abonnement Event Grid, envoyez une requête à l’opération Créer un abonnement aux événements. Utilisez le format suivant :

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Par exemple, pour créer un abonnement aux événements pour un compte de stockage nommé `examplestorage` dans un groupe de ressources nommé `examplegroup`, utilisez le format suivant :

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

L’article décrit les propriétés et le schéma pour le corps de la requête.
 
## <a name="event-subscription-properties"></a>Propriétés de l’abonnement aux événements

| Propriété | type | Description |
| -------- | ---- | ----------- |
| destination | objet | Objet qui définit le point de terminaison. |
| filter | objet | Champ facultatif pour filtrer les types d’événements. |

### <a name="destination-object"></a>objet de destination

| Propriété | type | Description |
| -------- | ---- | ----------- |
| endpointType | chaîne | Type de point de terminaison pour l’abonnement (webhook/HTTP, concentrateur d’événements ou file d’attente). | 
| endpointUrl | chaîne | URL de destination des événements dans cet abonnement aux événements. | 

### <a name="filter-object"></a>objet de filtre

| Propriété | type | Description |
| -------- | ---- | ----------- |
| includedEventTypes | array | Affiche une correspondance lorsque le type d’événement du message d’événement correspond exactement à l’un de ces noms de type d’événement. Génère une erreur lorsque le nom de l’événement ne correspond pas aux noms de type d’événement inscrits pour la source d’événements. Génère une correspondance pour tous les types d’événements. |
| subjectBeginsWith | chaîne | Filtre de correspondance de préfixe appliqué au champ objet du message de l’événement. La chaîne vide ou par défaut représente une correspondance générale. | 
| subjectEndsWith | chaîne | Filtre de correspondance de suffixe appliqué au champ objet du message de l’événement. La chaîne vide ou par défaut représente une correspondance générale. |
| subjectIsCaseSensitive | chaîne | Contrôle la correspondance sensible à la casse pour les filtres. |


## <a name="example-subscription-schema"></a>Exemple de schéma d’abonnement

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>étapes suivantes

* Pour une présentation d’Event Grid, consultez l’article [What is Event Grid?](overview.md) (Présentation d’Event Grid).