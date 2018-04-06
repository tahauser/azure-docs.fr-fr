---
title: Moniteurs dans l’extension Fonctions durables - Azure
description: Découvrez comment implémenter un moniteur d’état à l’aide de l’extension Fonctions durables pour Azure Functions.
services: functions
author: kashimiz
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 7e520429e5f5e219e05a77eb4ca18d0d6b6b3977
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Scénario de surveillance dans l’extension Fonctions durables - Exemple d’observateur météo

Le modèle de surveillance fait référence à un processus *récurrent* flexible dans un flux de travail, par exemple l’interrogation jusqu’à ce que certaines conditions soient respectées. Cet article décrit un exemple qui utilise l’extension [Fonctions durables](durable-functions-overview.md) pour implémenter la surveillance.

## <a name="prerequisites"></a>Prérequis


* [Installer Fonctions durables](durable-functions-install.md).
* Suivre la procédure pas à pas [Séquence Hello](durable-functions-sequence.md).

## <a name="scenario-overview"></a>Présentation du scénario

Cet exemple surveille les conditions météorologiques actuelles d’un lieu et alerte un utilisateur par SMS quand le ciel est clair. Vous pouvez utiliser une fonction normale déclenchée par un minuteur pour vérifier la météo et envoyer des alertes. Toutefois, cette approche pose le problème de la **gestion de la durée de vie**. Si une seule alerte doit être envoyée, le moniteur doit se désactiver une fois qu’un temps clair a été détecté. Le modèle de surveillance peut, entre autres avantages, mettre fin à sa propre exécution :

* Les moniteurs s’exécutent selon une périodicité, et non selon des planifications : un déclencheur de minuteur *s’exécute* toutes les heures ; un moniteur *attend* une heure entre les actions. Sauf indication contraire, les actions d’un moniteur ne se superposent pas, ce qui peut être important pour les tâches longues.
* Les moniteurs peuvent avoir des intervalles dynamiques : le délai d’attente peut changer en fonction de certaines conditions.
* Les moniteurs peuvent s’arrêter quand une condition donnée est respectée ou être interrompus par un autre processus.
* Les moniteurs peuvent prendre des paramètres. L’exemple montre comment le même processus de surveillance météorologique peut être appliqué à n’importe quels emplacement et numéro de téléphone demandés.
* Les moniteurs sont évolutifs. Étant donné que chaque moniteur est une instance d’orchestration, il est possible de créer plusieurs moniteurs sans avoir à créer de nouvelles fonctions ou à définir davantage de code.
* Les moniteurs s’intègrent facilement à de plus grands flux de travail. Un moniteur peut être une section d’une fonction d’orchestration plus complexe, ou une [sous-orchestration](https://docs.microsoft.com/en-us/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Configuration de l’intégration de Twilio

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Configuration de l’intégration de Weather Underground

Cet exemple implique l’utilisation de l’API Weather Underground pour vérifier les conditions météorologiques actuelles pour un lieu.

La première chose dont vous avez besoin est un compte Weather Underground. Vous pouvez en créer un gratuitement à l’adresse [https://www.wunderground.com/signup](https://www.wunderground.com/signup). Une fois que vous avez un compte, vous devez acquérir une clé d’API. Pour ce faire, visitez le site [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api), puis sélectionnez les paramètres de la clé. Le plan Stratus Developer est gratuit et suffisant pour exécuter cet exemple.

Une fois que vous disposez d’une clé API, ajoutez le **paramètre d’application** suivant à votre application de fonction.

| Nom du paramètre d’application | Description de la valeur |
| - | - |
| **WeatherUndergroundApiKey**  | Clé de votre API Weather Underground. |

## <a name="the-functions"></a>Les fonctions

Cet article explique les fonctions suivantes dans l’exemple d’application :

* `E3_Monitor` : fonction d’orchestrateur qui appelle régulièrement `E3_GetIsClear`. Elle appelle `E3_SendGoodWeatherAlert` si `E3_GetIsClear` retourne la valeur true.
* `E3_GetIsClear` : fonction d’activité qui vérifie les conditions météorologiques actuelles pour un lieu.
* `E3_SendGoodWeatherAlert` : fonction d’activité qui envoie un SMS via Twilio.

Les sections suivantes décrivent la configuration et le code utilisés pour les scripts C#. Le code de développement de Visual Studio est affiché à la fin de l’article.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>L’orchestration de la surveillance des conditions météorologiques (Visual Studio Code et exemple de code du portail Azure)

La fonction **E3_Monitor** utilise le fichier *function.json* standard pour les fonctions d’orchestrateur.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Voici le code qui implémente la fonction :

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

Cette fonction d’orchestrateur effectue les actions suivantes :

1. Obtient la valeur **MonitorRequest** constituée du *lieu* à surveiller et du *numéro de téléphone* auquel il envoie une notification par SMS.
2. Détermine le délai d’expiration du moniteur. Par souci de concision, l’exemple utilise une valeur codée en dur.
3. Appelle **E3_GetIsClear** pour déterminer si le ciel est dégagé au lieu demandé.
4. Si le temps est clair, appelle **E3_SendGoodWeatherAlert** pour envoyer une notification par SMS au numéro de téléphone demandé.
5. Crée un minuteur durable pour reprendre l’orchestration à l’intervalle d’interrogation suivant. Par souci de concision, l’exemple utilise une valeur codée en dur.
6. Continue à s’exécuter jusqu’à ce que le paramètre [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) passe l’heure d’expiration du moniteur, ou une alerte SMS est envoyée.

Il est possible d’exécuter simultanément plusieurs instances d’orchestrateur en envoyant plusieurs **MonitorRequests**. Le lieu à surveiller et le numéro de téléphone auquel envoyer une alerte SMS peuvent être spécifiés.

## <a name="strongly-typed-data-transfer"></a>Transfert de données fortement typées

L’orchestrateur nécessite plusieurs éléments de données. Par conséquent, des [objets OCT partagés](functions-reference-csharp.md#reusing-csx-code) sont utilisés pour le transfert des données fortement typées : [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

## <a name="helper-activity-functions"></a>Fonctions d’activité d’assistance

Comme avec d’autres exemples, les fonctions d’activité d’assistance sont des fonctions régulières qui utilisent la liaison de déclencheur `activityTrigger`. La fonction **E3_GetIsClear** obtient les conditions météorologiques actuelles à l’aide de l’API Weather Underground et détermine si le ciel est clair. Le fichier *function.json* est défini comme suit :

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

Et voici l’implémentation. Comme les objets OCT utilisés pour le transfert de données, une logique pour gérer l’appel de l’API et analyser la réponse JSON est abstraite dans une classe partagée. Elle est disponible dans le cadre de l’[exemple de code Visual Studio](#run-the-sample).

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

La fonction **E3_SendGoodWeatherAlert** utilise la liaison Twilio pour envoyer un SMS à l’utilisateur final pour l’avertir que c’est le moment d’aller faire une promenade. Son fichier *function.json* est simple :

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

Et voici le code qui envoie le SMS :

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

## <a name="run-the-sample"></a>Exécution de l'exemple

En utilisant les fonctions déclenchées via HTTP incluses dans l’exemple, vous pouvez démarrer l’orchestration en envoyant la requête HTTP POST suivante :

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "Location": { "City": "Redmond", "State": "WA" }, "Phone": "+1425XXXXXXX" }
```
```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

L’instance d’**E3_Monitor** démarre et interroge les conditions météorologiques actuelles pour le lieu demandé. Si le temps est clair, elle appelle une fonction d’activité pour envoyer une alerte ; dans le cas contraire, elle définit un minuteur. Quand le minuteur expire, l’orchestration reprend.

Vous pouvez voir l’activité de l’orchestration en examinant les journaux de fonction dans le portail Azure Functions.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

L’orchestration [s’arrête](durable-functions-instance-management.md#terminating-instances) une fois que son délai d’attente est atteint ou qu’un ciel clair est détecté. Vous pouvez également utiliser `TerminateAsync` à l’intérieur d’une autre fonction, ou appeler le Webhook HTTP POST **terminatePostUri** référencé dans la réponse 202 ci-dessus, en remplaçant `{text}` par le motif de l’arrêt :

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Exemple de code Visual Studio

Voici l’orchestration, présentée sous la forme d’un seul fichier C# dans un projet Visual Studio :

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Étapes suivantes

Cet exemple a montré comment utiliser l’extension Fonctions durables pour surveiller l’état d’une source externe à l’aide de [minuteurs durables](durable-functions-timers.md) et d’une logique conditionnelle. L’exemple suivant montre comment utiliser les événements externes et les [minuteurs durables](durable-functions-timers.md) pour gérer l’interaction humaine.

> [!div class="nextstepaction"]
> [Exécuter l’exemple d’interaction humaine](durable-functions-phone-verification.md)
