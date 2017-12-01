---
title: "Vue d’ensemble d’Azure Functions | Microsoft Docs"
description: "Découvrez comment utiliser les Azure Functions pour optimiser les charges de travail asynchrones en quelques minutes."
services: functions
documentationcenter: na
author: mattchenderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, fonctions, traitement des événements, webhooks, calcul dynamique, architecture sans serveur"
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: d60c898225b944801504f38d536262134a31e021
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2017
---
# <a name="an-introduction-to-azure-functions"></a>Présentation d’Azure Functions  
Azure Functions est une solution conçue pour exécuter facilement des petits morceaux de code, ou « fonctions », dans le cloud. Vous pouvez simplement écrire le code dont vous avez besoin pour le problème, sans vous soucier d’une application dans sa globalité ou de l’infrastructure pour l’exécuter. Grâce à Functions, le développement gagne en productivité et vous pouvez utiliser votre langage de développement préféré, tel que C#, F#, Node.js, Java ou PHP. Payez uniquement pour temps d’exécution de votre code et faites confiance à Azure pour effectuer la mise à l’échelle nécessaire. Azure Functions vous permet de développer des applications [sans serveur](https://azure.microsoft.com/overview/serverless-computing/) sur Microsoft Azure.

Cette rubrique fournit une vue d’ensemble d’Azure Functions. Si vous souhaitez vous familiariser rapidement et démarrer tout de suite avec Functions, commencez par [Créer votre première fonction Azure](functions-create-first-azure-function.md). Si vous recherchez des informations plus techniques sur Azure Functions, consultez les [informations de référence pour les développeurs](functions-reference.md).

## <a name="features"></a>Caractéristiques
Voici les principales fonctionnalités de Functions :

* **Choix du langage** : vous pouvez écrire des fonctions en C#, F# ou Javascript. Pour plus d’informations sur les autres options, consultez [Langages pris en charge](supported-languages.md).
* **Modèle de tarification à l’utilisation** : seul le temps passé à exécuter votre code vous est facturé. Reportez-vous à l’option de plan d’hébergement de consommation dans la [section relative à la tarification](#pricing).  
* **Intégration de vos propres dépendances** : Azure Functions prenant en charge NuGet et NPM, vous pouvez utiliser vos bibliothèques préférées.  
* **Sécurité intégrée** : protégez les fonctions déclenchées par HTTP à l’aide de fournisseurs OAuth comme Azure Active Directory, Facebook, Google, Twitter et Microsoft Account.  
* **Intégration simplifiée** : tirez facilement parti des services Azure et des offres SaaS (software as a service). Pour obtenir des exemples, consultez la [section relative aux intégrations](#integrations).  
* **Développement flexible** : codez vos fonctions directement dans le portail ou configurez une intégration continue et déployez votre code via [GitHub](../app-service/scripts/app-service-cli-continuous-deployment-github.md), [Visual Studio Team Services](../app-service/scripts/app-service-cli-continuous-deployment-vsts.md) et les autres [outils de développement pris en charge](../app-service/app-service-deploy-local-git.md).  
* **Open Source** : le runtime Azure Functions est open source et [disponible sur GitHub](https://github.com/azure/azure-webjobs-sdk-script).  

## <a name="what-can-i-do-with-functions"></a>Que puis-je faire avec Azure Functions ?
Functions est une solution idéale pour le traitement des données, l’intégration de systèmes, l’utilisation de l’Internet des objets (IoT) et la création de microservices et d’API simples. La solution Azure Functions peut être utilisée pour des tâches telles que le traitement d’images ou de commandes, la maintenance de fichiers ou toute tâche que vous voulez exécuter selon une planification. 

Azure Functions fournit des modèles pour vous aider à mettre en œuvre les principaux scénarios, notamment :

* **HTTPTrigger** : déclenchez l’exécution de votre code à l’aide d’une requête HTTP. Pour voir un exemple, consultez [Créer votre première fonction](functions-create-first-azure-function.md).
* **TimerTrigger** : exécutez des tâches de nettoyage ou d’autres tâches de traitement par lots selon une planification prédéfinie. Pour voir un exemple, consultez [Créer une fonction déclenchée par un minuteur](functions-create-scheduled-function.md).
* **Webhook GitHub** : répondez aux événements qui se produisent dans vos référentiels GitHub. Pour voir un exemple, consultez [Créer une fonction déclenchée par un webhook GitHub](functions-create-github-webhook-triggered-function.md).
* **Webhook générique** : traitez les requêtes HTTP de webhook à partir de n’importe quel service prenant en charge les webhooks. Pour voir un exemple, consultez [Créer une fonction déclenchée par un webhook générique](functions-create-generic-webhook-triggered-function.md).
* **CosmosDBTrigger** : traitez des documents Azure Cosmos DB quand ils sont ajoutés ou mis à jour dans des collections d’une base de données NoSQL. Pour voir un exemple, consultez [Créer une fonction déclenchée par Azure Cosmos DB](functions-create-cosmos-db-triggered-function.md).
* **BlobTrigger** : traitez les objets blob d’Azure Storage lors de leur ajout à des conteneurs. Vous pouvez utiliser cette fonction pour le redimensionnement d’images. Pour plus d’informations, consultez [Liaisons de stockage Blob](functions-bindings-storage-blob.md).
* **QueueTrigger** : répondez aux messages dès leur arrivée dans une file d’attente de stockage Azure. Pour voir un exemple, consultez [Créer une fonction déclenchée par Stockage File d’attente Azure](functions-create-storage-queue-triggered-function.md).
* **EventHubTrigger** : répondre aux événements remis à un Azure Event Hub. Particulièrement utile pour l’instrumentation de l’application, le traitement du workflow ou de l’expérience utilisateur, et les scénarios de l’Internet des Objets (IoT). Pour plus d’informations, consultez [Liaisons Event Hubs](functions-bindings-event-hubs.md).
* **ServiceBusQueueTrigger** : connectez votre code à d’autres services Azure ou à des services locaux en écoutant les files d’attente de messages. Pour plus d’informations, consultez [Liaisons Service Bus](functions-bindings-service-bus.md).
* **ServiceBusTopicTrigger** : connectez votre code à d’autres services Azure ou à des services locaux en vous abonnant à des rubriques. Pour plus d’informations, consultez [Liaisons Service Bus](functions-bindings-service-bus.md).

Azure Functions prend en charge les *déclencheurs*, qui permettent de démarrer l’exécution de votre code, et les *liaisons*, qui permettent de simplifier le codage des données d’entrée et de sortie. Pour obtenir une description détaillée des déclencheurs et des liaisons offerts par Azure Functions, consultez l’article [Informations de référence pour les développeurs sur les déclencheurs et liaisons Azure Functions](functions-triggers-bindings.md).

## <a name="integrations"></a>Intégrations
Azure Functions s’intègre avec différents services Azure et services tiers. Ces services peuvent déclencher votre fonction et démarrer l’exécution, ou servir d’entrée et de sortie à votre code. Azure Functions prend en charge les intégrations de service suivantes :

* Azure Cosmos DB
* Hubs d'événements Azure 
* Azure Event Grid
* Azure Mobile Apps (tables)
* Azure Notification Hubs
* Azure Service Bus (files d’attente et rubriques)
* Azure Storage (objets blob, files d’attente et tables) 
* GitHub (webhooks)
* Services locaux (à l’aide de Service Bus)
* Twilio (messages SMS)

## <a name="pricing"></a>Combien coûte Azure Functions ?
Azure Functions propose deux sortes de plans tarifaires. Choisissez celui qui répond le mieux à vos besoins : 

* **Plan de consommation** : lorsque votre fonction est exécutée, Azure fournit toutes les ressources de calcul nécessaires. Vous n’avez pas à vous préoccuper de la gestion des ressources et seul le temps d’exécution vous est facturé. 
* **Plan App Service** : exécutez vos fonctions comme vos applications API, mobiles et web. Si vous utilisez déjà App Service pour d’autres applications, vous pouvez exécuter vos fonctions dans le cadre du même plan, sans coûts supplémentaires. 

Pour plus d’informations sur les plans d’hébergement, consultez [Comparaison des plans d’hébergement Azure Functions](functions-scale.md). Vous trouverez toutes les informations sur la tarification sur la [page Tarification de Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Étapes suivantes
* [créer votre première fonction Azure](functions-create-first-azure-function.md)  
  vous permet de créer votre première fonction à l’aide du démarrage rapide d’Azure Functions. 
* [Informations de référence pour les développeurs sur Azure Functions](functions-reference.md)  
  fournit des informations techniques supplémentaires sur l’exécution d’Azure Functions, ainsi qu’une référence pour le codage de fonctions et la définition des déclencheurs et des liaisons.
* [Test d’Azure Functions](functions-test-a-function.md)  
  décrit plusieurs outils et techniques permettant de tester vos fonctions.
* [Comment mettre à l’échelle Azure Functions](functions-scale.md)  
  Présente les plans de service disponibles pour Azure Functions, dont le plan d’hébergement de consommation, et explique comment choisir le plan adapté à vos besoins. 
* [En savoir plus sur Azure App Service](../app-service/app-service-web-overview.md)  
  Azure Functions s’appuie sur Azure App Service pour les fonctionnalités essentielles comme les déploiements, les variables d’environnement et les diagnostics. 

