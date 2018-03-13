---
title: "Managed Service Identity avec Azure Event Hubs (préversion) | Microsoft Docs"
description: "Utiliser des identités du service managé avec Azure Event Hubs"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2017
ms.author: sethm
ms.openlocfilehash: dd50e4f6ebc5fdf5496a5127fde20bd052087b59
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="managed-service-identity-preview"></a>Managed Service Identity (préversion)

Managed Service Identity (MSI) est une fonctionnalité Azure qui vous permet de créer une identité sécurisée associée au déploiement sous lequel s’exécute le code de votre application. Vous pouvez ensuite associer cette identité à des rôles de contrôle d’accès qui accordent des autorisations personnalisées pour l’accès aux ressources Azure nécessaires à votre application. 

Avec MSI, la plateforme Azure gère cette identité d’exécution. Vous n’avez pas besoin de stocker et protéger des clés d’accès dans le code ou la configuration de votre application, que ce soit pour l’identité elle-même ou pour les ressources auxquelles vous devez accéder. Une application cliente Event Hubs en cours d’exécution à l’intérieur d’une application Azure App Service ou dans une machine virtuelle sur laquelle la prise en charge de MSI est activée n’a pas besoin de gérer des clés et des règles SAS ou d’autres jetons d’accès. L’application cliente a uniquement besoin de l’adresse de point de terminaison de l’espace de noms Event Hubs. Quand l’application se connecte, Event Hubs lie le contexte MSI au client dans une opération illustrée plus loin dans cet article.

Une fois associé à une identité du service managé, un client Event Hubs peut effectuer toutes les opérations autorisées. L’autorisation est accordée en associant une identité MSI à des rôles Event Hubs. 

## <a name="event-hubs-roles-and-permissions"></a>Rôles et autorisations Event Hubs

Pour la préversion publique initiale, vous pouvez uniquement ajouter une identité du service managé aux rôles « Propriétaire » ou « Contributeur » d’un espace de noms Event Hubs, qui accorde à l’identité un contrôle total sur toutes les entités dans l’espace de noms. Toutefois, les opérations de gestion qui changent la topologie de l’espace de noms sont initialement prises en charge seulement par Azure Resource Manager et non par l’interface de gestion REST Event Hubs native. Cette prise en charge signifie également que vous ne pouvez pas utiliser l’objet [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) client .NET Framework au sein d’une identité du service managé. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Utiliser Event Hubs avec une identité du service managé

La section suivante décrit les étapes requises pour créer et déployer un exemple d’application qui s’exécute sous une identité du service managé, et explique comment accorder l’accès à un espace de noms Event Hubs et comment l’application interagit avec les hubs d’événements à l’aide de cette identité.

Cette présentation décrit une application web hébergée dans [Azure App Service](https://azure.microsoft.com/services/app-service/). Les étapes requises pour une application hébergée sur une machine virtuelle sont similaires.

### <a name="create-an-app-service-web-application"></a>Créer une application web App Service

La première étape consiste à créer une application ASP.NET App Service. Si vous n’êtes pas familiarisé avec la procédure à suivre dans Azure, suivez [ce guide pratique](../app-service/app-service-web-get-started-dotnet-framework.md). Toutefois, au lieu de créer une application MVC, comme indiqué dans le didacticiel, créez une application Web Forms.

### <a name="set-up-the-managed-service-identity"></a>Configurer l’identité du service managé

Une fois l’application web créée, accédez-y dans le portail Azure (procédure également indiquée dans le guide pratique), puis accédez à la page **Managed Service Identity** et activez la fonctionnalité : 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Une fois la fonctionnalité activée, une identité de service est créée dans votre annuaire Azure Active Directory et configurée dans l’hôte App Service.

### <a name="create-a-new-event-hubs-namespace"></a>Créer un espace de noms Event Hubs

Ensuite, [créez un espace de noms Event Hubs](event-hubs-create.md) dans l’une des régions Azure qui prend en charge la préversion pour MSI : **Est des États-Unis**, **Est des États-Unis 2**, ou **Europe de l’Ouest**. 

Accédez à la page **Contrôle d’accès (IAM)** de l’espace de nom sur le portail, puis cliquez sur **Ajouter** pour ajouter l’identité du service managé au rôle **Propriétaire**. Pour ce faire, recherchez le nom de l’application web dans le champ **Sélectionner** du panneau **Ajouter des autorisations**, puis cliquez sur l’entrée. Cliquez ensuite sur **Enregistrer**.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
L’identité du service managé de l’application web a désormais accès à l’espace de noms Event Hubs et au hub d’événements que vous avez créé. 

### <a name="run-the-app"></a>Exécution de l'application

À présent, modifiez la page par défaut de l’application ASP.NET que vous avez créée. Vous pouvez également utiliser le code d’application web à partir de [ce dépôt GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Une fois que vous démarrez votre application, pointez votre navigateur sur EventHubsMSIDemo.aspx. Vous pouvez également la définir comme page de démarrage. Le code se trouve dans le fichier EventHubsMSIDemo.aspx.cs. Le résultat est une application web minimale avec quelques champs d’entrée et les boutons **send** (envoyer) et **receive** (recevoir) qui permettent de se connecter à Event Hubs pour envoyer ou recevoir des messages. 

Notez la façon dont l’objet [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) est initialisé. Au lieu d’utiliser le fournisseur de jetons SAP, le code crée un fournisseur de jetons pour l’identité du service managé avec l’appel `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)`. Ainsi, il n’est pas nécessaire de conserver et d’utiliser des secrets. Le flux du contexte de l’identité du service managé vers Event Hubs et la négociation des autorisations sont gérés automatiquement par le fournisseur de jetons, qui est un modèle plus simple que l’utilisation de SAP.

Une fois que vous avez apporté ces modifications, publiez et exécutez l’application. Un moyen facile d’obtenir les données de publication correctes consiste à télécharger un profil de publication, puis à l’importer dans Visual Studio :

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Pour envoyer ou recevoir des messages, entrez le nom de l’espace de noms et le nom de l’entité que vous avez créée, puis cliquez sur **send** (envoyer) ou **receive** (recevoir). 
 
Notez que l’identité du service managé fonctionne uniquement à l’intérieur de l’environnement Azure et seulement dans le déploiement App Service dans lequel vous l’avez configurée. Notez également que les identités du service managé ne fonctionnent pas avec les emplacements de déploiement App Service pour l’instant.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les concentrateurs d’événements, accédez aux liens suivants :

* Prise en main avec un [didacticiel des concentrateurs d’événements](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ sur les hubs d'événements](event-hubs-faq.md)
* [Tarification des concentrateurs d'événements](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Exemples d’application complets qui utilisent des concentrateurs d’événements](https://github.com/Azure/azure-event-hubs/tree/master/samples)