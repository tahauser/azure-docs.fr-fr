---
title: "Référence de schéma de webhook Azure Container Registry"
description: "Référence de charge utile JSON de requête de Webhook pour Azure Container Registry."
services: container-registry
author: mmacy
manager: timlt
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: marsma
ms.openlocfilehash: 84f0277a7b1a5bd7dfe2178f78f34140b1dd2642
ms.sourcegitcommit: a48e503fce6d51c7915dd23b4de14a91dd0337d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2017
---
# <a name="azure-container-registry-webhook-reference"></a>Référence de webhook Azure Container Registry

Vous pouvez [configurer des webhooks](container-registry-webhook.md) pour votre registre de conteneurs qui génèrent des événements lorsque certaines actions sont effectuées. Par exemple, vous pouvez activer des webhooks qui sont déclenchés sur des opérations `push` et `delete` d’image conteneur. Lorsqu’un webhook est déclenché, Azure Container Registry envoie une requête HTTP ou HTTPS contenant des informations sur l’événement à un point de terminaison que vous spécifiez. Votre point de terminaison peut ensuite traiter le webhook et agir en conséquence.

Les sections suivantes détaillent le schéma de requêtes de webhook générées par des événements pris en charge. Les sections de l’événement contient le schéma de charge utile pour le type d’événement, une charge utile de requête par exemple, et un ou plusieurs exemples de commandes qui peuvent déclencher le webhook.

Pour plus d’informations sur la configuration des webhooks pour votre registre de conteneurs Azure, consultez [Utilisation de webhooks Azure Container Registry](container-registry-webhook.md).

## <a name="webhook-requests"></a>Requêtes de webhook

### <a name="http-request"></a>Demande HTTP

Un webhook déclenché envoie une requête `POST` HTTP au point de terminaison d’URL que vous avez spécifié lorsque vous avez configuré le webhook.

### <a name="http-headers"></a>En-têtes HTTP

Les requêtes de webhook incluent un `Content-Type` de `application/json` si vous n’avez pas spécifié un en-tête personnalisé `Content-Type` pour votre webhook.

Aucun en-tête, autre que ces en-têtes personnalisés que vous pouvez avoir spécifiés pour le webhook, n’est ajouté à la requête.

## <a name="push-event"></a>Événement push

Webhook déclenché lorsqu’une image conteneur est envoyée vers un référentiel.

### <a name="push-event-payload"></a>Charge utile d’événement push

|Élément|Type|Description|
|-------------|----------|-----------|
|`id`|String|ID de l’événement de webhook.|
|`timestamp`|DateTime|Heure à laquelle l’événement de webhook a été déclenché.|
|`action`|String|Action qui a déclenché l’événement de webhook.|
|[cible](#target)|Type complexe|Cible de l’événement qui a déclenché l’événement de webhook.|
|[requête](#request)|Type complexe|Requête qui a généré l’événement de webhook.|

### <a name="target"></a>cible

|Élément|Type|Description|
|------------------|----------|-----------|
|`mediaType`|String|Type MIME de l’objet référencé.|
|`size`|Int32|Nombre d’octets du contenu. Identique au champ Longueur.|
|`digest`|String|Résumé du contenu, tel que défini par la spécification d’API du Registre V2 HTTP.|
|`length`|Int32|Nombre d’octets du contenu. Identique au champ Taille.|
|`repository`|String|Nom du référentiel.|
|`tag`|String|Nom de la balise d’image.|

### <a name="request"></a>request

|Élément|Type|Description|
|------------------|----------|-----------|
|`id`|String|ID de la requête qui a initié l’événement.|
|`host`|String|Nom d’hôte accessible de l’extérieur de l’instance du registre, tel que spécifié par l’en-tête d’hôte HTTP sur les requêtes entrantes.|
|`method`|String|Méthode de requête qui a généré l’événement.|
|`useragent`|String|En-tête d’agent utilisateur de la requête.|

### <a name="payload-example-push-event"></a>Exemple de charge utile : événement push

```JSON
{
  "id": "cb8c3971-9adc-488b-bdd8-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-86cd-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Exemple de commande [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) qui déclenche le webhook d’événement **push** :

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="delete-event"></a>Supprimer un événement

Webhook déclenché lorsqu’un référentiel ou un manifeste est supprimé. Non déclenché lorsqu’une balise est supprimée.

### <a name="delete-event-payload"></a>Charge utile d’événement de suppression

|Élément|Type|Description|
|-------------|----------|-----------|
|`id`|String|ID de l’événement de webhook.|
|`timestamp`|DateTime|Heure à laquelle l’événement de webhook a été déclenché.|
|`action`|String|Action qui a déclenché l’événement de webhook.|
|[cible](#delete_target)|Type complexe|Cible de l’événement qui a déclenché l’événement de webhook.|
|[requête](#delete_request)|Type complexe|Requête qui a généré l’événement de webhook.|

### <a name="delete_target"></a> cible

|Élément|Type|Description|
|------------------|----------|-----------|
|`mediaType`|String|Type MIME de l’objet référencé.|
|`digest`|String|Résumé du contenu, tel que défini par la spécification d’API du Registre V2 HTTP.|
|`repository`|String|Nom du référentiel.|

### <a name="delete_request"></a> requête

|Élément|Type|Description|
|------------------|----------|-----------|
|`id`|String|ID de la requête qui a initié l’événement.|
|`host`|String|Nom d’hôte accessible de l’extérieur de l’instance du registre, tel que spécifié par l’en-tête d’hôte HTTP sur les requêtes entrantes.|
|`method`|String|Méthode de requête qui a généré l’événement.|
|`useragent`|String|En-tête d’agent utilisateur de la requête.|

### <a name="payload-example-delete-event"></a>Exemple de charge utile : événement de suppression

```JSON
{
    "id": "afc359ce-df7f-4e32-bdde-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:80f0d5c8786bb9e621a45ece0db56d11cdc624ad20da9fe62e9d25490f331d7d",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-807f-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Exemple de commandes [Azure CLI 2.0](/cli/azure/acr) qui déclenchent un webhook d’événement **supprimer** :

```azurecli
# Delete repository
az acr repository delete -n MyRegistry --repository MyRepository

# Delete manifest
az acr repository delete -n MyRegistry --repository MyRepository --tag MyTag --manifest
```

## <a name="next-steps"></a>Étapes suivantes

[Utilisation de webhooks Azure Container Registry](container-registry-webhook.md)