---
title: API HTTP dans Fonctions durables - Azure
description: Découvrez comment implémenter des API HTTP dans l’extension Fonctions durables d’Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 5fa5d9e66912bdeffdf553ddc0cb7d3feb0a5b77
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>API HTTP dans Fonctions durables (Azure Functions)

L’extension Tâche durable expose un ensemble d’API HTTP qui peut être utilisé pour effectuer les tâches suivantes :

* Récupérer l'état d'une instance d’orchestration.
* Envoyer un événement à une instance d’orchestration en attente.
* Mettre fin à une instance d’orchestration en cours d’exécution.


Chacune de ces API HTTP est une opération webhook gérée directement par l’extension Tâche durable. Elles ne sont pas spécifiques à une fonction dans l’application de fonction.

> [!NOTE]
> Ces opérations peuvent également être appelées directement à l’aide des API de gestion d’instance sur la classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html). Pour plus d’informations, consultez [Gestion d’instance](durable-functions-instance-management.md).

## <a name="http-api-url-discovery"></a>Découverte de l’URL de l’API HTTP

La classe [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) expose une API [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) qui peut être utilisée pour générer une charge utile de réponse HTTP contenant des liens vers toutes les opérations prises en charge. Voici un exemple de fonction de déclencheur HTTP qui montre comment utiliser cette API :

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

Cet exemple de fonction produit les données de réponse JSON suivantes. Le type de données de tous les champs est `string`.

| Champ             |Description                           |
|-------------------|--------------------------------------|
| id                |L’ID de l’instance d’orchestration. |
| statusQueryGetUri |L’URL de l’état de l’instance d’orchestration. |
| sendEventPostUri  |L’URL « Raise event » de l’instance d’orchestration. |
| terminatePostUri  |L’URL « d’arrêt » de l’instance d’orchestration. |

Voici un exemple de réponse :

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/webhookextensions/handler/DurableTaskExtension/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```
> [!NOTE]
> Le format des URL « webhook » peut différer selon la version de l’hôte Azure Functions que vous exécutez. L’exemple ci-dessus utilise l’hôte Azure Functions 2.0.

## <a name="async-operation-tracking"></a>Suivi de l’opération asynchrone

La réponse HTTP mentionnée précédemment est conçue pour faciliter l’implémentation d’API asynchrones HTTP à long terme avec Fonctions durables. Cette opération est parfois appelée *modèle d’interrogation du consommateur*. Le flux client/serveur fonctionne comme suit :

1. Le client émet une requête HTTP pour démarrer un processus à long terme, par exemple une fonction d’orchestrateur.
2. Le déclencheur HTTP cible renvoie une réponse HTTP 202 dont l’en-tête `Location` a la valeur `statusQueryGetUri`.
3. Le client interroge l’URL dans l’en-tête `Location`. Il continue de voir les réponses HTTP 202 avec un en-tête `Location`.
4. Lorsque l’instance se termine (ou échoue), le point de terminaison dans l’en-tête `Location` renvoie HTTP 200.

Ce protocole permet de coordonner les processus à long terme avec des clients ou des services externes qui prennent en charge l’interrogation d’un point de terminaison HTTP et le suivi de l’en-tête `Location`. Les éléments fondamentaux sont déjà intégrés aux API HTTP de Fonctions durables.

> [!NOTE]
> Par défaut, toutes les actions basées sur HTTP fournies par [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/) prennent en charge le modèle d’opération asynchrone standard. Cette fonctionnalité permet d’incorporer une fonction durable de longue durée dans un flux de travail Logic Apps. Plus d’informations sur la prise en charge des modèles HTTP asynchrones par Logic Apps, consultez la [documentation sur les actions de flux de travail et les déclencheurs Azure Logic Apps](../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns).

## <a name="http-api-reference"></a>Référence sur l'API HTTP

Toutes les API HTTP implémentées par l’extension utilisent les paramètres suivants. Le type de données de tous les paramètres est `string`.

| Paramètre  | Type de paramètre  | Description |
|------------|-----------------|-------------|
| instanceId | URL             | L’ID de l’instance d’orchestration. |
| taskHub    | Chaîne de requête    | Le nom du [hub de tâches](durable-functions-task-hubs.md). S’il n’est pas spécifié, le nom de hub de tâches de l’application de fonction en cours est supposé. |
| connection | Chaîne de requête    | Le **nom** de la chaîne de connexion du compte de stockage. Si elle n’est pas spécifiée, la chaîne de connexion par défaut pour l’application de la fonction est supposée. |
| systemKey  | Chaîne de requête    | La clé d’autorisation requise pour appeler l’API. |
| showHistory| Chaîne de requête    | Paramètre facultatif. Si la valeur est définie sur `true`, l’historique d’exécution de l’orchestration est inclus dans la charge utile de réponse.| 
| showHistoryOutput| Chaîne de requête    | Paramètre facultatif. Si la valeur est définie sur `true`, les sorties de l’activité sont incluses dans l’historique d’exécution de l’orchestration.| 

`systemKey` est une clé d’autorisation automatiquement générée par l’hôte Azure Functions. Elle accorde l’accès aux API de l’extension Tâche durable et peut être gérée de la même façon que les [autres clés d’autorisation](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). La façon la plus simple pour découvrir la valeur `systemKey` consiste à utiliser l’API `CreateCheckStatusResponse` mentionnée précédemment.

Les quelques sections suivantes présentent les API HTTP spécifiques prises en charge par l’extension et fournissent des exemples d’utilisation.

### <a name="get-instance-status"></a>Obtenir l’état de l’instance

Obtient l'état d'une instance d’orchestration spécifiée.

#### <a name="request"></a>Requête

Pour Functions 1.0, le format de la demande est le suivant :

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}
```

Le format de Functions 2.0 a les mêmes paramètres, mais avec un préfixe d’URL légèrement différent :

```http
GET /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}?taskHub={taskHub}&connection={connection}&code={systemKey}&showHistory={showHistory}&showHistoryOutput={showHistoryOutput}
```

#### <a name="response"></a>response

Plusieurs valeurs de code d’état possibles peuvent être retournées.

* **HTTP 200 (OK)** : l’instance spécifiée est dans un état terminé.
* **HTTP 202 (acceptée)** : l’instance spécifiée est en cours d’exécution.
* **HTTP 400 (Bad Request)** : l’instance spécifiée a échoué ou a été arrêtée.
* **HTTP 404 (Not Found)** : l’instance spécifiée n’existe pas ou n’a pas démarré.

La charge utile de réponse pour les cas **HTTP 200** et **HTTP 202** est un objet JSON avec les champs suivants :

| Champ           | Type de données | Description |
|-----------------|-----------|-------------|
| runtimeStatus   | chaîne    | L’état d’exécution de l’instance. Les valeurs sont *Running*, *Pending*, *Failed*, *Canceled*, *Terminated*, *Completed*. |
| entrée           | JSON      | Les données JSON utilisées pour initialiser l’instance. |
| sortie          | JSON      | La sortie JSON de l’instance. Ce champ est `null` si l’instance n’est pas dans un état terminé. |
| createdTime     | chaîne    | Heure à laquelle l’instance a été créée. Utilise la notation étendue ISO 8601. |
| lastUpdatedTime | chaîne    | Heure du dernier état persistant de l’instance. Utilise la notation étendue ISO 8601. |
| historyEvents   | JSON      | Tableau JSON contenant l’historique d’exécution de l’orchestration. Ce champ est `null`, sauf si le paramètre de chaîne de requête `showHistory` a la valeur `true`.  | 

Voici un exemple de charge utile de réponse incluant l’historique et les sorties de l’activité d’exécution d’orchestration (mis en forme pour une meilleure lisibilité) :

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

La réponse **HTTP 202** inclut également un en-tête de réponse **Location** qui fait référence à la même URL que le champ `statusQueryGetUri` mentionné précédemment.

### <a name="raise-event"></a>Raise event

Envoie un message de notification d’événement à une instance d’orchestration en cours d’exécution.

#### <a name="request"></a>Requête

Pour Functions 1.0, le format de la demande est le suivant :

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Le format de Functions 2.0 a les mêmes paramètres, mais avec un préfixe d’URL légèrement différent :

```http
POST /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection={connection}&code={systemKey}
```

Les paramètres de requête pour cette API incluent l’ensemble par défaut mentionné précédemment, ainsi que les paramètres uniques suivants :

| Champ       | Type de paramètre  | Type de données | Description |
|-------------|-----------------|-----------|-------------|
| eventName   | URL             | chaîne    | Le nom de l’événement que l’instance d’orchestration cible attend. |
| {content}   | Contenu de la demande | JSON      | La charge utile de l’événement au format JSON. |

#### <a name="response"></a>response

Plusieurs valeurs de code d’état possibles peuvent être retournées.

* **HTTP 202 (Accepted)** : l’événement déclenché a été accepté pour traitement.
* **HTTP 400 (Bad request)** : le contenu de la demande n’était pas de type `application/json` ou n’était pas un objet JSON valide.
* **HTTP 404 (Not Found)** : l’instance spécifiée est introuvable.
* **HTTP 410 (Gone)** : l’instance spécifiée est terminée ou a échoué et ne peut traiter aucun événement déclenché.

Voici un exemple de requête qui envoie la chaîne JSON `"incr"` à une instance en attente d’un événement nommé **operation** :

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Les réponses pour cette API sont vides.

### <a name="terminate-instance"></a>Arrêter une instance

Arrête une instance d’orchestration en cours d’exécution.

#### <a name="request"></a>Requête

Pour Functions 1.0, le format de la demande est le suivant :

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Le format de Functions 2.0 a les mêmes paramètres, mais avec un préfixe d’URL légèrement différent :

```http
DELETE /webhookextensions/handler/DurableTaskExtension/instances/{instanceId}/terminate?reason={reason}&taskHub={taskHub}&connection={connection}&code={systemKey}
```

Les paramètres de la demande pour cette API incluent l’ensemble par défaut mentionné précédemment, ainsi que le paramètre unique suivant.

| Champ       | Type de paramètre  | Type de données | Description |
|-------------|-----------------|-----------|-------------|
| motif      | Chaîne de requête    | chaîne    | facultatif. Motif d’arrêt de l’instance d’orchestration. |

#### <a name="response"></a>response

Plusieurs valeurs de code d’état possibles peuvent être retournées.

* **HTTP 202 (Accepted)** : la demande d’arrêt a été acceptée pour traitement.
* **HTTP 404 (Not Found)** : l’instance spécifiée est introuvable.
* **HTTP 410 (Gone)** : l’instance spécifiée est terminée ou a échoué.

Voici un exemple de demande qui met fin à une instance en cours d’exécution et affiche la raison **buggy** (bogué) :

```
DELETE /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Les réponses pour cette API sont vides.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Découvrez comment gérer les erreurs](durable-functions-error-handling.md)
