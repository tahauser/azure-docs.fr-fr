---
title: "Managed Service Identity avec Azure Service Bus (préversion) | Microsoft Docs"
description: "Utiliser des identités de service administré avec Azure Service Bus"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 6965e80cf10b732d4d0a8fb78447f188c133979d
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="managed-service-identity-preview"></a>Managed Service Identity (préversion)

Managed Service Identity (MSI) est une fonctionnalité Azure qui vous permet de créer une identité sécurisée associée au déploiement sous lequel s’exécute le code de votre application. Vous pouvez ensuite associer cette identité à des rôles de contrôle d’accès qui accordent des autorisations personnalisées pour l’accès aux ressources Azure nécessaires à votre application.

Avec MSI, la plateforme Azure gère cette identité d’exécution. Vous n’avez pas besoin de stocker et de protéger des clés d’accès dans le code ou la configuration de votre application, que ce soit pour l’identité elle-même ou pour les ressources auxquelles vous devez accéder. Une application cliente Service Bus en cours d’exécution à l’intérieur d’une application Azure App Service ou dans une machine virtuelle sur laquelle la prise en charge de MSI est activée n’a pas besoin de gérer des clés et des règles SAS ou d’autres jetons d’accès. L’application cliente a uniquement besoin de l’adresse de point de terminaison de l’espace de noms Service Bus Messaging. Quand l’application se connecte, Service Bus lie le contexte MSI au client dans une opération illustrée plus loin dans cet article. 

Une fois associé à une identité du service administré, un client Service Bus peut effectuer toutes les opérations autorisées. L’autorisation est accordée en associant une identité de service administré à des rôles Service Bus. 

## <a name="service-bus-roles-and-permissions"></a>Rôles et autorisations Service Bus

Pour la préversion publique initiale, vous pouvez uniquement ajouter une identité de service administré aux rôles « Propriétaire » ou « Contributeur » d’un espace de noms Service Bus, qui accorde à l’identité un contrôle total sur toutes les entités de l’espace de noms. Toutefois, les opérations de gestion qui changent la topologie de l’espace de noms sont initialement prises en charge seulement par Azure Resource Manager et non par l’interface de gestion REST Service Bus native. Cette prise en charge signifie également que vous ne pouvez pas utiliser l’objet [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) client .NET Framework au sein d’une identité de service administré.

## <a name="use-service-bus-with-a-managed-service-identity"></a>Utiliser Service Bus avec une identité de service administré

La section suivante décrit les étapes requises pour créer et déployer un exemple d’application qui s’exécute sous une identité de service administré. Elle explique comment accorder l’accès à un espace de noms Service Bus Messaging et comment l’application interagit avec les entités Service Bus à l’aide de cette identité.

Cette présentation décrit une application web hébergée dans [Azure App Service](https://azure.microsoft.com/services/app-service/). Les étapes requises pour une application hébergée sur une machine virtuelle sont similaires.

### <a name="create-an-app-service-web-application"></a>Créer une application web App Service

La première étape consiste à créer une application ASP.NET App Service. Si vous n’êtes pas familiarisé avec la procédure à suivre dans Azure, suivez [ce guide pratique](../app-service/app-service-web-get-started-dotnet-framework.md). Toutefois, au lieu de créer une application MVC, comme indiqué dans le didacticiel, créez une application Web Forms.

### <a name="set-up-the-managed-service-identity"></a>Configurer l’identité de service administré

Une fois l’application web créée, accédez-y dans le portail Azure (procédure également indiquée dans le guide pratique), puis accédez à la page **Managed Service Identity** et activez la fonctionnalité : 

![](./media/service-bus-managed-service-identity/msi1.png)

Une fois la fonctionnalité activée, une identité de service est créée dans votre annuaire Azure Active Directory et configurée dans l’hôte App Service.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Créer un espace de noms Service Bus Messaging

Ensuite, [créez un espace de noms Service Bus Messaging](service-bus-create-namespace-portal.md) dans l’une des régions Azure qui prend en charge la préversion du contrôle d’accès en fonction du rôle : **Est des États-Unis**, **Est des États-Unis 2** ou **Europe de l'Ouest**. 

Accédez à la page **Contrôle d’accès (IAM)** de l’espace de nom sur le portail, puis cliquez sur **Ajouter** pour ajouter l’identité de service administré au rôle **Propriétaire**. Pour ce faire, recherchez le nom de l’application web dans le champ **Sélectionner** du panneau **Ajouter des autorisations**, puis cliquez sur l’entrée. Cliquez ensuite sur **Enregistrer**.

![](./media/service-bus-managed-service-identity/msi2.png)
 
L’identité de service administré de l’application web a désormais accès à l’espace de noms Service Bus et à la file d’attente que vous avez créée. 

### <a name="run-the-app"></a>Exécution de l'application

À présent, modifiez la page par défaut de l’application ASP.NET que vous avez créée. Vous pouvez également utiliser le code d’application web à partir de [ce référentiel GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity). 

La page Default.aspx est votre page d’accueil. Le code se trouve dans le fichier Default.aspx.cs. Le résultat est une application web minimale avec quelques champs d’entrée et les boutons **send** (envoyer) et **receive** (recevoir) qui permettent de se connecter à Service Bus pour envoyer ou recevoir des messages.

Notez la façon dont l’objet [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) est initialisé. Au lieu d’utiliser le fournisseur de jetons SAP, le code crée un fournisseur de jetons pour l’identité de service administré avec l’appel `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`. Ainsi, il n’est pas nécessaire de conserver et d’utiliser des secrets. Le flux du contexte de l’identité de service administré vers Service Bus et la négociation des autorisations sont gérés automatiquement par le fournisseur de jetons, qui est un modèle plus simple que l’utilisation de SAP.

Une fois que vous avez apporté ces modifications, publiez et exécutez l’application. Un moyen facile d’obtenir les données de publication correctes consiste à télécharger un profil de publication, puis à l’importer dans Visual Studio :

![](./media/service-bus-managed-service-identity/msi3.png)
 
Pour envoyer ou recevoir des messages, saisissez le nom de l’espace de noms et le nom de l’entité que vous avez créée, puis cliquez sur **send** (envoyer) ou **receive** (recevoir). 
 
Notez que l’identité de service administré fonctionne uniquement à l’intérieur de l’environnement Azure et seulement dans le déploiement App Service dans lequel vous l’avez configurée. Notez également que les identités de service administré ne fonctionnent pas avec les emplacements de déploiement App Service pour l’instant.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la messagerie Service Bus, voir les rubriques suivantes.

* [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Files d’attente, rubriques et abonnements Service Bus](service-bus-queues-topics-subscriptions.md)
* [Prise en main des files d’attente Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Utilisation des rubriques et abonnements Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)