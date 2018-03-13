---
title: Choisir entre Flow, Logic Apps, Functions et WebJobs | Microsoft Docs
description: "Découvrez une analyse comparative des services d’intégration cloud de Microsoft pour déterminer quel(s) service(s) utiliser."
services: functions,app-service\logic
documentationcenter: na
author: ggailey777
manager: wpickett
tags: 
keywords: "microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, traitement d’événements, calcul dynamique, architecture sans serveur"
ms.assetid: e9ccf7ad-efc4-41af-b9d3-584957b1515d
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 7ffe44828735a5687008ebc5a7d8d9f017f49daa
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Choisir entre Flow, Logic Apps, Functions et WebJobs
Cet article dresse une analyse comparative des services suivants de Microsoft Cloud ; ils permettent tous de résoudre des problèmes d’intégration et d’automatiser les processus métier :

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

Tous ces services sont utiles pour « coller » ensemble des systèmes disparates. Ils peuvent tous définir des entrées, des actions, des conditions et des sorties. Vous pouvez exécuter chacun d’eux selon une planification ou à l’aide d’un déclencheur. Toutefois, chaque service offre ses propres avantages, et leur comparaison ne vise pas à déterminer lequel est le meilleur, mais plutôt lequel convient le mieux à une situation donnée. Une combinaison de ces services constitue souvent le meilleur moyen de créer rapidement une solution d’intégration complète et évolutive.

<a name="flow"></a>

## <a name="flow-vs-logic-apps"></a>Flow vs Logic Apps
Microsoft Flow et Azure Logic Apps peuvent être traités ensemble, puisqu’ils sont tous les deux des services d’intégration *orientés configuration*. La création de processus et de flux de travail, comme l’intégration à diverses applications d’entreprise et SaaS s’en trouvent facilitées. 

* Flow est basé sur Logic Apps.
* Ils présentent le même concepteur de flux de travail.
* [connecteurs](../connectors/apis-list.md) qui fonctionnent dans l’un fonctionnent également dans l’autre.

Flow permet à n’importe quel employé de bureau d’effectuer des intégrations simples (par exemple, un processus d’approbation dans une bibliothèque de documents SharePoint) sans solliciter les développeurs ni le service informatique. En revanche, les applications de Logic Apps prennent quant à elles en charge des intégrations avancées (par exemple des processus B2B) nécessitant des pratiques DevOps et de sécurité au niveau de l’entreprise. Il est courant qu’un flux de travail métier se complexifie au fil du temps. Par conséquent, vous pouvez commencer par un flux et le convertir ensuite en application logique selon vos besoins.

Le tableau suivant vous aide à déterminer si Flow ou Logic Apps convient le mieux à une intégration donnée.

|  | Flux | Logic Apps |
| --- | --- | --- |
| Audience |Employés de bureau, utilisateurs de l’entreprise, administrateurs SharePoint |Intégrateurs et développeurs professionnels, professionnels de l’informatique |
| Scénarios |Libre-service |Intégrations avancées |
| Outil de conception |Dans le navigateur et application mobile, interface utilisateur uniquement |Dans le navigateur et [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [mode Code](../logic-apps/logic-apps-author-definitions.md) disponible |
| Application Lifecycle Management (ALM) |Concevez et testez au sein d’environnements hors production, passez en production lorsque vous êtes prêt. |DevOps : contrôle de code source, tests, support, et automatisation et gestion simplifiée dans [Azure Resource Manager](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). |
| Expérience administrateur |Gérez les stratégies des environnements Flow et de prévention contre la perte des données, suivez la gestion des licences [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com). |Gérez les groupes de ressources, les connexions, la gestion des accès et la connexion [https://portal.azure.com](https://portal.azure.com). |
| Sécurité |Journaux d’audit de sécurité et conformité Office 365, prévention contre la perte de données, [chiffrement au repos](https://wikipedia.org/wiki/Data_at_rest#Encryption) pour les données sensibles, etc. |Garantie de sécurité d’Azure : [Azure Security](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [journaux d’audit](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), etc. |

<a name="function"></a>

## <a name="functions-vs-webjobs"></a>Functions vs WebJobs
Azure Functions et Azure App Service WebJobs peuvent être traités ensemble, car il s’agit dans les deux cas de services d’intégration *orientés code* et conçus pour les développeurs. Ils vous permettent d’exécuter un script ou un bloc de code en réponse à divers événements, tels que [de nouveaux objets blob de stockage](functions-bindings-storage.md) ou [une requête WebHook](functions-bindings-http-webhook.md). Voici leurs points communs : 

* Ils sont tous deux basés sur [Azure App Service](../app-service/app-service-web-overview.md) et proposent des fonctionnalités telles que [le contrôle de code source](../app-service/app-service-continuous-deployment.md), [l’authentification](../app-service/app-service-authentication-overview.md) et [la surveillance](../app-service/web-sites-monitor.md).
* Il s’agit dans les deux cas de services destinés aux développeurs.
* Ils prennent en charge les langages standard de script et de programmation.
* Ils prennent en charge NuGet et NPM.

Functions est l’évolution naturelle de WebJobs dans la mesure où il reprend les atouts de WebJobs en les améliorant. Les améliorations incluent : 

* Modèle d’applications [sans serveur](https://azure.microsoft.com/overview/serverless-computing/).
* Développement, test et exécution de code simplifiés, directement dans le navigateur.
* Intégration native avec d’autres services Azure et des services tiers tels que [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
* Paiement à l’utilisation, sans avoir à payer pour un [plan App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
* [Mise à l’échelle dynamique](functions-scale.md)et automatique.
* Pour les clients existants d’App Service, l’utilisation d’un plan App Service reste possible (pour tirer parti des ressources sous-utilisées).
* Intégration avec Logic Apps.

Le tableau suivant récapitule les différences entre Functions et WebJobs :

|  | Functions | WebJobs |
| --- | --- | --- |
| Mise à l'échelle |Mise à l’échelle sans configuration |Mise à l’échelle avec un plan App Service |
| Tarifs |Paiement à l’utilisation ou dans le cadre d’un plan App Service |Dans le cadre d’un plan App Service |
| Type d’exécution |Déclenchée, planifiée (par un déclencheur de minuteur) |Déclenchée, continue, planifiée |
| Événements déclencheurs |[Minuteur](functions-bindings-timer.md), [Azure Cosmos DB](functions-bindings-cosmosdb.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md), [Azure App Service Mobile Apps](functions-bindings-mobile-apps.md), [Azure Event Hubs](functions-bindings-event-hubs.md), [Files d’attente et objets Blob Stockage Azure](functions-bindings-storage-blob.md), [Files d’attente et rubriques Azure Service Bus](functions-bindings-service-bus.md) |[Files d’attente et objets Blob Stockage Azure](functions-bindings-storage-blob.md), [Files d’attente et rubriques Azure Service Bus](functions-bindings-service-bus.md) |
| Développement dans le navigateur |Prise en charge |Non pris en charge |
| C# |Prise en charge |Prise en charge |
| F# |Prise en charge |Non pris en charge |
| JavaScript |Prise en charge |Prise en charge |
| Java |VERSION PRÉLIMINAIRE | Non pris en charge |
| Bash |Expérimental |Prise en charge |
| Écriture de scripts Windows (.cmd, .bat) |Expérimental |Prise en charge |
| PowerShell |Expérimental |Prise en charge |
| PHP |Expérimental |Prise en charge |
| Python |Expérimental |Prise en charge |
| TypeScript |Expérimental |Non pris en charge |

Le choix entre Functions et WebJobs dépend en définitive de l’utilisation que vous faites d’App Service. Si vous avez une application App Service pour laquelle vous souhaitez exécuter des extraits de code, et que vous voulez les gérer ensemble dans le même environnement DevOps, utilisez WebJobs. Dans les scénarios suivants, utilisez Functions.

* Vous souhaitez exécuter des extraits de code pour d’autres services Azure ou des applications tierces.
* Vous voulez gérer votre code d’intégration séparément à partir de vos applications App Service.
* Vous souhaitez appeler vos extraits de code à partir d’une application logique. 

<a name="together"></a>

## <a name="flow-logic-apps-and-functions-together"></a>Flow, Logic Apps et Functions ensemble
Comme mentionné précédemment, le service qui vous convient le mieux dépend de votre situation. 

* Pour une optimisation simple de votre activité, utilisez Flow.
* Si votre scénario d’intégration est trop avancé pour Flow ou si vous avez besoin de fonctionnalités DevOps, utilisez Logic Apps.
* Si une étape de votre scénario d’intégration nécessite une transformation hautement personnalisée ou du code spécialisé, écrivez une fonction et déclenchez cette fonction en tant qu’action dans votre application logique.

Vous pouvez appeler une application logique dans un flux. Vous pouvez également appeler une fonction dans une application logique et une application logique dans une fonction. L’intégration entre Flow, Logic Apps et Functions continue de s’améliorer au fil du temps. Vous pouvez créer quelque chose dans un service et l’utiliser dans les autres services. Par conséquent, tout investissement dans ces trois technologies est pertinent.

## <a name="next-steps"></a>Étapes suivantes
Prenez en main chacun de ces services en créant votre premier flux, application logique, application de fonction ou tâche web. Cliquez sur l’un des liens suivants :

* [Prise en main de Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Créer votre première fonction Azure](functions-create-first-azure-function.md)
* [Déployer des tâches web à l’aide de Visual Studio](../app-service/websites-dotnet-deploy-webjobs.md)

Vous pouvez également obtenir des informations supplémentaires sur ces services d’intégration en cliquant sur les liens suivants :

* [Leveraging Azure Functions &amp; Azure App Service for integration scenarios (Tirer parti d’Azure Functions et Azure App Service pour les scénarios d’intégration), par Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrations Made Simple (Les intégrations simplifiées), par Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Diffusion web sur Logic Apps](http://aka.ms/logicappslive)
* [Forum Aux Questions sur Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)

