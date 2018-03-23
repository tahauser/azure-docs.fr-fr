---
title: Singletons avec état dans l’extension Fonctions durables - Azure
description: Découvrez comment implémenter un singleton avec état dans l’extension Fonctions durables d’Azure Functions.
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
ms.openlocfilehash: 46cdd8523117e1100e7ce2a29ade9eb2dc0afe75
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="stateful-singletons-in-durable-functions---counter-sample"></a>Singletons avec état dans l’extension Fonctions durables - Échantillon de compteur

> [!NOTE]
> Nous réécrivons cet exemple. Les extraits de code ont été supprimés, et un nouvel article pour disposer d’un nouvel exemple le remplacera.

Les singletons avec état sont des fonctions d’orchestrateur exécutées sur le long terme (voire à l’infini), capables de stocker un état et d’être appelées et interrogées par d’autres fonctions. Les singletons avec état sont similaires au [modèle d’acteur](https://en.wikipedia.org/wiki/Actor_model) des traitements distribués.

Les fonctions d’orchestrateur ne sont pas des implémentations d’« acteur » à proprement parler, mais elles présentent de nombreuses caractéristiques similaires en terme de runtime. Par exemple, elles présentent un état et sont fiables, monothread, transparentes au niveau de leur emplacement et globalement adressables. Certaines caractéristiques rendent les implémentations d’acteur réelles particulièrement utiles, mais sans nécessiter d’infrastructure séparée.

Cet article explique comment exécuter l’échantillon de *compteur*. Cet échantillon détaille un objet de singleton qui prend en charge les opérations *d’incrémentation* et de *décrémentation*, en mettant à jour son état interne comme il convient.

## <a name="prerequisites"></a>Prérequis


* Suivez les instructions indiquées dans la section [Installer des fonctions durables](durable-functions-install.md) pour configurer l’exemple.
* Cet article suppose que vous avez déjà parcouru l’exemple de procédure pas à pas [Séquence Hello](durable-functions-sequence.md).

## <a name="scenario-overview"></a>Présentation du scénario

Le scénario de compteur est étonnamment difficile à implémenter à l’aide de fonctions sans état classiques. L’un des principaux défis à gérer est la **concurrence**. Les opérations telles que *l’incrémentation* et *la décrémentation* doivent être atomiques, afin d’éviter des conditions de concurrence entraînant le remplacement d’une opération par une autre.

Vous pouvez utiliser une machine virtuelle unique pour héberger les données du compteur, mais cette opération est coûteuse ; par exemple, la gestion de la **fiabilité** peut s’avérer délicate, car vous devrez redémarrer régulièrement la machine virtuelle. Il est également possible d’utiliser une plate-forme distribuée avec des outils de synchronisation, tel qu’un bail d’objet blob, pour administrer la concurrence, mais cette opération augmente le niveau de **complexité**.

L’extension Fonctions durables permet de simplifier l’implémentation de ce scénario, car les instances d’orchestration sont apparentées à une machine virtuelle unique et l’exécution des fonctions d’orchestrateur est toujours monothread. De plus, elles s’exécutent sur le long terme, présentent un état et peuvent réagir à des événements externes. L’exemple de code ci-dessous indique comment implémenter ce compteur en tant que fonction d’orchestrateur à long terme.

## <a name="the-sample-function"></a>Exemple de fonction

Cet article présente la fonction **E3_Counter** dans l’exemple d’application, pas à pas.



## <a name="the-counter-orchestration"></a>Orchestration du compteur

Les sections suivantes détaillent le code utilisé pour le développement de Visual Studio Code et du Portail Azure.

### <a name="c-script"></a>Script C#

Le fichier function.json :

<!-- [!code-json[Main](~/samples-durable-functions/samples/csx/E3_Counter/function.json)] -->

Le fichier run.csx :

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Counter/run.csx)] -->

### <a name="precompiled-c"></a>C# précompilé 

Les sections suivantes détaillent le code utilisé pour le développement de Visual Studio.

Voici le code qui implémente la fonction d’orchestrateur :

<!-- [!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Counter.cs)] -->

### <a name="explanation-of-the-code"></a>Explication du code

Cette fonction d’orchestrateur effectue essentiellement les opérations suivantes :

1. Elle écoute un événement externe nommé *operation* à l’aide de l’élément [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_).
2. Elle incrémente ou décrémente la variable locale `counterState` en fonction de l’opération demandée.
3. Elle redémarre l’orchestrateur à l’aide de la méthode [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_), en définissant la valeur la plus récente de l’élément `counterState` en tant que la nouvelle entrée.
4. Elle continue de s’exécuter indéfiniment, ou jusqu’à ce qu’un message de *fin* message soit reçu.

Il s’agit d’un exemple *d’orchestration externe* &mdash; c’est-à-dire d’une orchestration susceptible de ne jamais se terminer. Il répond aux messages qui lui sont envoyés par la méthode [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_), qui peut être appelée par n’importe quelle fonction autre qu’une fonction d’orchestrateur.

Cette fonction d’orchestrateur présente une caractéristique unique : elle n’est aucune historique ; la méthode `ContinueAsNew` réinitialise l’historique après chaque événement traité. Il s’agit de la meilleure façon d’implémenter un orchestrateur qui présente une durée de vie arbitraire. L’utilisation d’une boucle `while` peut entraîner la suppression des liaisons de l’historique de la fonction d’orchestrateur, ce qui entraîne une exploitation élevée de la mémoire, sans nécessité.

> [!NOTE]
> La méthode `ContinueAsNew` présente d’autres cas d’usage, en plus des orchestrations externes. Pour en savoir plus, consultez la section relative aux [orchestrations externes](durable-functions-eternal-orchestrations.md).

## <a name="run-the-sample"></a>Exécution de l'exemple

Vous pouvez démarrer l’orchestration en envoyant la requête HTTP POST suivante. Afin d’autoriser `counterState` à démarrer à partir de zéro (valeur par défaut de l’élément `int`), cette requête est vide.

```
POST http://{host}/orchestrators/E3_Counter
Content-Length: 0
```

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{
  "id":"bcf6fb5067b046fbb021b52ba7deae5a",
  "statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}",
  "terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

L’instance **E3_Counter** démarre, puis attend qu’un événement lui soit envoyé à l’aide de l’élément `RaiseEventAsync` ou du Webhook HTTP POST **sendEventUrl** référencé dans la réponse 202. Les valeurs valides de l’élément `eventName` incluent *incr*, *decr*, et *end*.

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Type: application/json
Content-Length: 6

"incr"
```

Vous pouvez voir les résultats de l’opération « incr » si vous examinez les journaux des fonctions dans le portail Azure Functions.

```
2017-06-29T18:54:53.998 Function started (Id=34e34a61-38b3-4eac-b6e2-98b85e32eec8)
2017-06-29T18:54:53.998 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Function started (Id=b45d6c2f-39f3-42a2-b904-7761b2614232)
2017-06-29T18:58:01.458 Current counter state is 0. Waiting for next operation.
2017-06-29T18:58:01.458 Received 'incr' operation.
2017-06-29T18:58:01.458 Function completed (Success, Id=b45d6c2f-39f3-42a2-b904-7761b2614232, Duration=8ms)
2017-06-29T18:58:11.518 Function started (Id=e1f38cb2-546a-404d-ab22-1ac8f81a93d9)
2017-06-29T18:58:11.518 Current counter state is 1. Waiting for next operation.
```

De même, si vous vérifiez l’état de l’orchestrateur, vous pouvez constater que le champ `input` a été défini sur la valeur mise à jour (1).

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey} HTTP/1.1
```

```
HTTP/1.1 202 Accepted
Content-Length: 129
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":1,"output":null,"createdTime":"2017-06-29T18:58:01Z","lastUpdatedTime":"2017-06-29T18:58:11Z"}
```

Vous pouvez continuer à envoyer de nouvelles opérations à cette instance, et constater que leur état est mis à jour en conséquence. Pour tuer l’instance, envoyez une opération *end*.

> [!WARNING]
> Au moment de l’écriture de cet article, il existe des conditions de concurrence connues lors de l’appel de l’élément `ContinueAsNew` en parallèle avec le traitement des messages, par exemple de demandes d’arrêt ou des événements externes. Pour en savoir plus sur ces conditions de concurrence, consultez cet [article de GitHub](https://github.com/Azure/azure-functions-durable-extension/issues/67).

## <a name="next-steps"></a>Étapes suivantes

Cet exemple a montré comment gérer les [événements externes](durable-functions-external-events.md) et implémenter les [orchestrations externes](durable-functions-eternal-orchestrations.md) dans les [singletons avec état](durable-functions-singletons.md). L’exemple suivant montre comment utiliser les événements externes et les [minuteurs durables](durable-functions-timers.md) pour gérer l’interaction humaine.

> [!div class="nextstepaction"]
> [Exécuter l’exemple d’interaction humaine](durable-functions-phone-verification.md)
