---
title: Bonnes pratiques pour Azure Functions | Microsoft Docs
description: "Découvrez les bonnes pratiques et les modèles pour Azure Functions."
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, modèles, bonne pratique, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8088a8a83bcaefce17ac2756360a46119c8eb27
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Optimisation des performances et de la fiabilité d’Azure Functions

Cet article fournit des instructions pour améliorer les performances et la fiabilité de vos applications de fonction [sans serveur](https://azure.microsoft.com/overview/serverless-computing/). 

## <a name="general-best-practices"></a>Bonnes pratiques générales

Voici les bonnes pratiques liées à la création et à l’élaboration de vos solutions serverless à l’aide d’Azure Functions.

### <a name="avoid-long-running-functions"></a>Évitez les fonctions dont l’exécution prend beaucoup de longtemps

Ces fonctions peuvent provoquer des problèmes de délai d’attente inattendus. Une fonction peut devenir volumineuse en raison d’un nombre important de dépendances Node.js. L’importation des dépendances peut entraîner une augmentation des temps de chargement aboutissant à des délais d’attente inattendus. Les dépendances sont chargées tant explicitement qu’implicitement. Un module chargé par votre code peut charger ses propres modules supplémentaires.  

Autant que possible, subdivisez les fonctions volumineuses en ensembles de fonctions plus petits qui fonctionnent ensemble et retournent des réponses rapides. Par exemple, un webhook ou une fonction de déclenchement HTTP peut nécessiter une réponse avec accusé de réception dans un délai imparti. Il est courant que des webhooks demandent une réponse immédiate. Vous pouvez passer la charge utile du déclencheur HTTP dans une file d’attente en vue de son traitement par une fonction de déclenchement de file d’attente. Cette approche vous permet de différer le travail réel et de retourner une réponse immédiate.


### <a name="cross-function-communication"></a>Communication entre fonctions

Les [Fonctions durables](durable-functions-overview.md) et le service [Azure Logic Apps](../logic-apps/logic-apps-overview.md) sont conçus pour gérer les transitions d’état et la communication entre plusieurs fonctions.

Si vous n’utilisez pas les fonctions durables ni Logic Apps pour l’intégration à plusieurs fonctions, une bonne pratique consiste en général à utiliser des files d’attente de stockage pour la communication entre les fonctions.  La principale raison est que les files d’attente de stockage sont plus économiques et beaucoup plus faciles à configurer. 

La taille de chaque message d’une file d’attente de stockage est limitée à 64 Ko. Pour transmettre des messages plus volumineux entre les fonctions, vous pouvez utiliser une file d’attente Azure Service Bus, qui prend en charge des tailles de message allant jusqu’à 256 Ko pour le niveau Standard, 1 Mo pour le niveau Premium.

Les rubriques Service Bus sont utiles si vous avez besoin de filtrer les messages avant de les traiter.

Les hubs d’événements sont utiles pour prendre en charge les communications de volume élevé.


### <a name="write-functions-to-be-stateless"></a>Écrire des fonctions sans état 

Les fonctions doivent être sans état et idempotentes si possible. Associez toutes les informations d’état requises à vos données. Par exemple, une commande en cours de traitement aurait probablement un membre `state` associé. Une fonction peut traiter une commande suivant cet état, tandis que la fonction elle-même reste sans état. 

Les fonctions idempotentes sont particulièrement recommandées avec les déclencheurs à minuterie. Par exemple, si une tâche doit absolument s’exécuter une fois par jour, écrivez-la de façon à ce qu’elle puisse s’exécuter à n’importe quel moment de la journée avec les mêmes résultats. La fonction peut s’arrêter si aucun travail ne doit être effectué au cours d’un jour donné. En outre, si une exécution précédente a été interrompue, la prochaine exécution doit reprendre au point d’interruption.


### <a name="write-defensive-functions"></a>Écrire des fonctions défensives

Supposons que votre fonction peut être confrontée à une exception à tout moment. Concevez vos fonctions de façon à ce qu’elles puissent reprendre à un point d’échec précédent la prochaine fois qu’elles s’exécutent. Imaginez un scénario qui nécessite les actions suivantes :

1. Récupérer 10 000 lignes d’une base de données.
2. Créer un message de file d’attente pour chacune de ces lignes en vue de leur traitement.
 
Selon la complexité de votre système, il est possible que des services impliqués en aval se comportent de manière incorrecte, que des pannes réseau se produisent, que des limites de quota soient atteintes, etc. Tous ces facteurs peuvent affecter votre fonction à tout moment. Vous devez concevoir vos fonctions en conséquence.

Comment votre code réagit-il si une défaillance se produit après l’insertion de 5 000 de ces éléments dans une file d’attente en vue de leur traitement ? Suivez les éléments dans un jeu que vous avez terminé. Sinon, vous pouvez les réinsérer la prochaine fois. Cela peut avoir un impact sérieux sur votre flux de travail. 

Si un élément de file d’attente a déjà été traité, permettez à votre fonction d’être une absence d’opération.

Tirez parti des mesures défensives déjà fournies pour les composants que vous utilisez dans la plateforme Azure Functions. Par exemple, consultez **Gestion des messages de file d’attente incohérents** dans la documentation relative aux [liaisons et déclencheurs de file d’attente de stockage Azure](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Bonnes pratiques relatives à l’extensibilité

Il existe un certain nombre de facteurs qui ont un impact sur la façon dont les instances de votre application de fonction se mettent à l’échelle. Les détails sont fournis dans la documentation sur la [mise à l’échelle de fonction](functions-scale.md).  Voici quelques-unes des bonnes pratiques assurant l’extensibilité optimale d’une application de fonction.

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Ne pas mélanger code de test et code de production dans la même application de fonction

Les fonctions d’une application de fonction partagent des ressources. Par exemple, la mémoire est partagée. Si vous utilisez une application de fonction en production, n’y ajoutez pas de ressources et de fonctions de test. Cela peut entraîner une surcharge inattendue pendant l’exécution du code de production.

Soyez attentif à ce que vous chargez dans vos applications de fonction en production. La mémoire moyenne est calculée pour chaque fonction au sein de l’application.

Si un assembly partagé est référencé dans plusieurs fonctions .Net, placez-le dans un dossier partagé commun. Référencez l’assembly avec une instruction similaire à l’exemple suivant si des scripts C# (.csx) sont utilisés : 

    #r "..\Shared\MyAssembly.dll". 

Sinon, il est facile de déployer accidentellement plusieurs versions de test du même binaire qui se comportent différemment d’une fonction à l’autre.

N’utilisez pas de journalisation détaillée dans le code de production. Cela affecte les performances.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Utiliser du code asynchrone tout en évitant de bloquer les appels

La programmation asynchrone est une pratique recommandée. Toutefois, évitez toujours de référencer la propriété `Result` ou d’appeler la méthode `Wait` sur une instance `Task`. Cette approche peut mener à un épuisement des threads.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Recevoir des messages par lots chaque fois que possible

Certains déclencheurs, tels que Event Hub, permettent la réception d’un lot de messages sur un simple appel.  Le traitement par lot des messages offre de bien meilleures performances.  Vous pouvez configurer la taille maximale de lot dans le fichier `functions.json`, comme indiqué dans la [documentation de référence de host.json](functions-host-json.md)

Pour les fonctions C#, vous pouvez modifier le type en tableau d’objets fortement typé.  Par exemple, au lieu de `EventData sensorEvent` la signature de méthode peut être `EventData[] sensorEvent`.  Pour d’autres langages, vous devez définir explicitement la propriété de cardinalité dans votre fichier `function.json` sur `many` afin d’activer le traitement par lot, [comme indiqué ici](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurer les comportements d’hôte pour mieux gérer l’accès concurrentiel

Le fichier `host.json` dans l’application de fonction permet la configuration des comportements de déclencheur et de runtime hôtes.  En plus des comportements de traitement par lot, vous pouvez gérer l’accès concurrentiel d’un certain nombre de déclencheurs.  Souvent l’ajustement des valeurs de ces options peut permettre la mise à l’échelle adéquate de chaque instance face aux demandes des fonctions appelées.

Les paramètres dans le fichier des hôtes s’appliquent à toutes les fonctions de l’application, dans une *instance unique* de la fonction. Par exemple, si vous aviez une application de fonction dotée de 2 fonctions HTTP avec une valeur pour les demandes simultanées définie sur 25, une requête à l’un des déclencheurs HTTP serait comptabilisée dans les 25 demandes simultanées partagées.  Si cette application de fonction était redimensionnée à 10 instances, les 2 fonctions autoriseraient en réalité 250 demandes simultanées (10 instances * 25 demandes simultanées par instance).

**Options d’hôte concurrentiel HTTP**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

D’autres options de configuration d’hôte sont consultables [dans le document de configuration d’hôte](functions-host-json.md).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez les ressources suivantes :

Étant donné qu’Azure Functions utilise Azure App Service, vous devez également connaître les directives d’App Service.
* [Modèles et pratiques d’optimisations des performances HTTP](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)
