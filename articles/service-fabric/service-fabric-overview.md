---
title: Vue d’ensemble de Service Fabric sur Azure | Microsoft Docs
description: Présentation de Service Fabric, où les applications sont composées de nombreux microservices pour fournir une mise à l’échelle et une résilience. Service Fabric est une plateforme de systèmes distribués qui permet de créer des applications évolutives, fiables et faciles à gérer pour le cloud.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: masnider
ms.assetid: bbcc652a-a790-4bc4-926b-e8cd966587c0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: overview
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: msfussell
ms.custom: mvc
ms.openlocfilehash: d5bd75dfff87e73fa4104901c1c0ac0989b1e3bb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="overview-of-azure-service-fabric"></a>Vue d’ensemble d’Azure Service Fabric
Azure Service Fabric est une plateforme de systèmes distribués qui facilite le packaging, le déploiement et la gestion de conteneurs et de microservices évolutifs et fiables. Service Fabric résout également les problèmes non négligeables du développement et de la gestion des applications natives au cloud. Les développeurs et administrateurs sont en mesure d’éviter les problèmes d’infrastructure complexes et peuvent se concentrer sur l’implémentation de charges de travail stratégiques et exigeantes, évolutives, fiables et faciles à gérer. Service Fabric représente la plateforme de nouvelle génération pour la création et la gestion de ces applications d’entreprise de niveau 1 à l’échelle du cloud et exécutées dans des conteneurs.

Cette courte vidéo présente Service Fabric et les microservices :
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-Service-Fabric/player]

## <a name="applications-composed-of-microservices"></a>Applications composées de microservices 
Service Fabric vous permet de créer et gérer des applications évolutives et fiables, composées de microservices qui s’exécutent à haute densité sur un pool partagé d’ordinateurs appelé cluster. Il fournit un runtime léger et sophistiqué pour générer des microservices exécutés dans des conteneurs, avec et sans état distribués et évolutifs. Il fournit également des fonctionnalités complètes de gestion d’application pour la configuration, le déploiement, l’analyse, la mise à niveau/mise à jour corrective et la suppression d’applications incluant des services en conteneur.

Service Fabric alimente de nombreux services Microsoft aujourd’hui, notamment Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype Entreprise et de nombreux services Azure principaux.

Service Fabric est adapté à la création de services cloud natifs qui peuvent commencer petit, si nécessaire, et évoluer à grande échelle avec des centaines ou des milliers d’ordinateurs.

Actuellement, les services Internet sont composés de microservices. Les microservices sont, par exemple, les passerelles de protocole, les profils utilisateur, les paniers d’achat, le traitement des stocks, les files d’attente et les caches. Service Fabric est une plateforme de microservices qui attribue à chaque microservice (ou conteneur) un nom unique pouvant être avec ou sans état.

Service Fabric fournit des fonctionnalités complètes de gestion du runtime et du cycle de vie pour les applications constituées de ces microservices. Il héberge les microservices dans des conteneurs déployés et activés sur le cluster Service Fabric. Le passage de machines virtuelles à des conteneurs rend possible une multiplication par dix de la densité. De même, une nouvelle multiplication par dix de la densité devient possible en passant de conteneurs à des microservices inclus dans ces mêmes conteneurs. Par exemple, un cluster unique pour Azure SQL Database comprend des centaines d’ordinateurs exécutant des dizaines de milliers de conteneurs qui hébergent au total des centaines de milliers de bases de données. Chaque base de données est un microservice Service Fabric avec état. 

Pour plus d’informations sur l’approche microservices, consultez [Pourquoi adopter une approche microservices de la création d’applications ?](service-fabric-overview-microservices.md)

## <a name="container-deployment-and-orchestration"></a>Orchestration et le déploiement de conteneurs
Service Fabric est [l’orchestrateur de conteneurs](service-fabric-cluster-resource-manager-introduction.md) de Microsoft permettant de déployer des microservices sur un cluster de machines. Les microservices peuvent être développés de nombreuses manières, par le biais des [modèles de programmation de Service Fabric](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), ou en déployant [n’importe quel code de votre choix](service-fabric-guest-executables-introduction.md). Mais surtout, vous pouvez combiner des services dans des processus et dans des conteneurs au sein de la même application. Si vous souhaitez simplement [déployer et gérer des conteneurs](service-fabric-containers-overview.md) Service Fabric est le choix d’orchestrateur de conteneurs idéal.

## <a name="any-os-any-cloud"></a>Tous les systèmes d’exploitation, tous les clouds
Service Fabric peut être exécuté partout. Vous pouvez créer des clusters pour Service Fabric dans de nombreux environnements, notamment Azure ou locaux, sous Windows Server ou sous Linux. Vous pouvez même créer des clusters sur d’autres clouds publics. En outre, l’environnement de développement dans le Kit de développement logiciel (SDK) est **identique** à l’environnement de production, sans aucun émulateur impliqué. En d’autres termes, ce qui est exécuté sur votre cluster de développement local est déployé sur les clusters dans les autres environnements.

![Plateforme Service Fabric][Image1]

Pour le développement Windows, le Kit de développement logiciel (SDK) .NET Service Fabric est intégré à Visual Studio et à PowerShell. Consultez la page [Préparer un environnement de développement sous Windows](service-fabric-get-started.md). Pour le développement Linux, le Kit SDK Java Service Fabric est intégré à Eclipse, et Yeoman permet de générer des modèles pour des applications Java, .NET Core et conteneurs. Consultez la page [Préparer un environnement de développement sous Linux](service-fabric-get-started.md).

Pour en savoir plus sur la création de clusters, consultez la page [Créer un cluster sous Windows Server ou Linux](service-fabric-deploy-anywhere.md) ou, pour Azure, Créer un cluster [par l’intermédiaire du Portail Azure](service-fabric-cluster-creation-via-portal.md).

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microservices avec et sans état pour Service Fabric
Service Fabric permet de créer des applications composées de microservices ou de conteneurs. Les microservices sans état (tels que les passerelles de protocole et les proxys web) ne conservent pas un état mutable en dehors d’une demande du service et de sa réponse. Les rôles de travail Azure Cloud Services sont un exemple de service sans état. Les microservices avec état (tels que les comptes d’utilisateur, les bases de données, les appareils, les paniers d’achat et les files d’attente) conservent un état mutable faisant autorité au-delà de la demande et la réponse. Actuellement, les applications Internet sont constituées d’une combinaison de microservices avec et sans état. 

La principale différence avec Service Fabric consiste en l’importance accordée à la création de services avec état, que ce soit avec les [modèles de programmation intégrés](service-fabric-choose-framework.md) ou avec des services avec état en conteneur. Les [scénarios d’application](service-fabric-application-scenarios.md) décrivent les scénarios dans lesquels les services avec état sont utilisés.


## <a name="application-lifecycle-management"></a>Gestion du cycle de vie des applications
Service Fabric assure la prise en charge totale du cycle de vie et CI/CD des applications cloud incluant des conteneurs. Ce cycle de vie va du développement à la mise hors service, en passant par le déploiement, la gestion quotidienne et la maintenance.

Les fonctionnalités de gestion du cycle de vie des applications offertes par Service Fabric permettent aux administrateurs d’applications et aux opérateurs informatiques d’utiliser des flux de travail simples et automatisés pour configurer, déployer, corriger et surveiller les applications. Ces flux de travail intégrés réduisent considérablement la charge pesant sur les opérateurs informatiques pour que vos applications soient disponibles en permanence.

La plupart des applications sont constituées d’une combinaison de microservices avec et sans état, de conteneurs et d’autres exécutables déployés ensemble. Service Fabric, grâce à des types forts sur les applications, permet le déploiement de plusieurs instances d’applications. Chaque instance est gérée et mise à niveau indépendamment. Plus important encore, Service Fabric peut déployer des conteneurs ou n’importe quel exécutable et les rendre fiables. Par exemple, Service Fabric peut déployer .NET, ASP.NET Core, node.js, des conteneurs Windows, des conteneurs Linux, des machines virtuelles Java, des scripts, Angular ou littéralement n’importe quel élément qui compose votre application.

Service Fabric est intégré avec des outils de CI/CD tels que [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html), et [Octopus Deploy](https://octopus.com/) et peut être utilisé avec n’importe quel autre outil de CI/CD populaire.

Pour plus d’informations sur la gestion du cycle de vie des applications, consultez [Cycle de vie des applications](service-fabric-application-lifecycle.md). Pour plus d’informations sur le déploiement de code quelconque, consultez [déployer un exécutable invité](service-fabric-deploy-existing-app.md).

## <a name="key-capabilities"></a>Fonctionnalités clés
Service Fabric vous permet d'effectuer les opérations suivantes :

* Déployer dans Azure ou dans des centres de données locaux qui exécutent Windows ou Linux sans aucune modification de code. Écrire une fois, puis déployer sur n’importe quel cluster Service Fabric.
* Développer des applications évolutives composées de microservices à l’aide des modèles de programmation, des conteneurs, ou de n’importe quel code Service Fabric.
* Développer des microservices avec et sans état très fiables. Simplifier la conception de votre application à l’aide de microservices avec état. 
* Utiliser le nouveau modèle de programmation Reliable Actors pour créer des objets de cloud avec code et état intégrés.
* Déployer et orchestrer les conteneurs incluant des conteneurs Windows et Linux. Service Fabric est un orchestrateur de conteneurs avec état capable d’analyser et d’indexer automatiquement les données.
* Déployer des applications en quelques secondes, à haute densité et avec des centaines, voire des milliers d’applications ou de conteneurs par machine.
* Déployer des versions différentes de la même application côte à côte et mettre à niveau chaque application indépendamment.
* Gérer le cycle de vie de vos applications sans interruption de service, y compris les mises à niveau avec rupture et sans rupture.
* Augmenter ou diminuez le nombre de nœuds dans un cluster. Lorsque vous mettez vos nœuds à l’échelle, vos applications sont automatiquement mises à l’échelle.
* Surveiller et diagnostiquer l’intégrité de vos applications et définir des stratégies pour effectuer des réparations automatiques.
* Observer l’équilibreur de ressources orchestrer la redistribution des applications au sein du cluster. Service Fabric récupère après des défaillances et optimise la distribution de la charge en fonction des ressources disponibles.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations :
  * [Pourquoi une approche de microservices pour la conception d’applications ?](service-fabric-overview-microservices.md)
  * [Vue d'ensemble de la terminologie](service-fabric-technical-overview.md)
* Configurer un [environnement de développement Windows](service-fabric-get-started.md)  
* Configurer un [environnement de développement Linux](service-fabric-get-started-linux.md)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
