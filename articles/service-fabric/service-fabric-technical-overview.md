---
title: Découvrir la terminologie de Service Fabric | Microsoft Docs
description: Présentation de la terminologie de Service Fabric. Décrit les concepts et termes principaux utilisés dans le reste de la documentation.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/26/2018
ms.author: ryanwi
ms.openlocfilehash: bd57b6344baef3bdf97c850564ae2d3afa9c811e
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="service-fabric-terminology-overview"></a>Présentation de la terminologie Service Fabric
Azure Service Fabric est une plateforme de systèmes distribués qui permet d’empaqueter, de déployer et de gérer facilement des microservices scalables et fiables. Cet article décrit en détail la terminologie utilisée dans Service Fabric pour que vous compreniez les termes utilisés dans la documentation.

Les concepts de cette section sont également traités dans les vidéos suivantes de Microsoft Virtual Academy : <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">Principaux concepts</a>, <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965">Concepts de conception</a> et <a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">Concepts d’exécution</a>.

## <a name="infrastructure-concepts"></a>Concepts d’infrastructure
**Cluster**: groupe de machines virtuelles ou physiques connectées au réseau sur lequel vos microservices sont déployés et gérés.  Les clusters peuvent être mis à l’échelle pour des milliers de machines.

**Nœud** : machine ou machine virtuelle faisant partie d’un cluster. Un nom (chaîne) est affecté à chaque nœud. Les nœuds présentent des caractéristiques, telles que des propriétés de placement. Chaque machine ou machine virtuelle a un service Windows à démarrage automatique, `FabricHost.exe`, qui commence à s’exécuter dès le démarrage, puis démarre deux exécutables : `Fabric.exe` et `FabricGateway.exe`. Ces deux exécutables constituent le nœud. Pour les scénarios de test, vous pouvez héberger plusieurs nœuds sur une seule et même machine ou sur une seule et même machine virtuelle en exécutant plusieurs instances de `Fabric.exe` et `FabricGateway.exe`.

## <a name="application-concepts"></a>Concepts d’application
**Type d’application** : nom/version affectés à une collection de types de service. Il est défini dans un fichier `ApplicationManifest.xml` et incorporé dans un répertoire de package d’application. Le répertoire est ensuite copié dans le magasin d’images du cluster Service Fabric. Vous pouvez ensuite créer une application nommée à partir de ce type d’application au sein du cluster.

Pour plus d’informations, consultez l’article [Modèle d’application](service-fabric-application-model.md).

**Package d’application** : répertoire de disque contenant le fichier `ApplicationManifest.xml` du type d’application. Référence les packages de services pour chaque type de service qui constitue le type d’application. Les fichiers du répertoire de package d’application sont copiés dans le magasin d’images du cluster Service Fabric. Par exemple, un package d’un type d’application de messagerie peut contenir des références à un package de services de File d’attente, un package de services frontaux et un package de services de base de données.

**Application nommée** : une fois qu’un package d’application est copié dans le magasin d’images, vous créez une instance de l’application au sein du cluster. Vous créez une instance quand vous spécifiez le type d’application du package d’application à l’aide de son nom ou de sa version. Un nom d’URI est affecté à chaque instance de type d’application, qui ressemble à ceci : `"fabric:/MyNamedApp"`. Dans un cluster, vous pouvez créer plusieurs applications nommées à partir d’un seul type d’application. Vous pouvez également en créer à partir de différents types d’application. Chaque application nommée est gérée, et sa version est gérée indépendamment.      

**Type de service** : nom/version affectés aux packages de code, packages de données et packages de configuration d’un service. Le type de service est défini dans le fichier `ServiceManifest.xml` et incorporé dans un répertoire de package de services. Ce répertoire est ensuite référencé par le fichier `ApplicationManifest.xml` du package d’application. Au sein du cluster, après avoir créé une application nommée, vous pouvez créer un service nommé à partir d’un des types de service du type d’application. Le fichier `ServiceManifest.xml` du type de service décrit le service.

Pour plus d’informations, consultez l’article [Modèle d’application](service-fabric-application-model.md).

Il existe deux types de service :

* **Sans état** : utilisez un service sans état si l’état persistant du service est stocké dans un service de stockage externe, par exemple Stockage Azure, Azure SQL Database ou Azure Cosmos DB. Utilisez un service sans état si le service est dépourvu de stockage persistant. Par exemple, pour un service de calculatrice où des valeurs sont transmises au service, un calcul est effectué à l’aide de ces valeurs, et un résultat est retourné.
* **Avec état** : utilisez un service avec état si vous souhaitez que Service Fabric gère l’état de votre service via ses modèles de programmation Collections fiables ou Reliable Actors. Spécifiez le nombre de partitions sur lesquelles vous souhaitez répartir votre état (pour la scalabilité) lors de la création d’un service nommé. Spécifiez également le nombre de réplications de votre état sur plusieurs nœuds (pour la fiabilité). Chaque service nommé possède un seul réplica principal et plusieurs réplicas secondaires. Vous modifiez l’état de votre service nommé en écrivant dans le réplica principal. Service Fabric réplique ensuite cet état sur tous les réplicas secondaires pour préserver la synchronisation de votre état. Service Fabric détecte automatiquement l’échec d’un réplica principal et promeut un réplica secondaire existant en réplica principal. Service Fabric crée ensuite un nouveau réplica secondaire.  

**Réplicas ou instances** : code (et état pour les services avec état) d’un service déployé et en cours d’exécution. Consultez [Réplicas et instances](service-fabric-concepts-replica-lifecycle.md).

**Reconfiguration** : processus de changement (quel qu’il soit) dans le jeu de réplicas d’un service. Consultez [Reconfiguration](service-fabric-concepts-reconfiguration.md).

**Package de services** : répertoire de disque contenant le fichier `ServiceManifest.xml` du type de service. Ce fichier référence le code, les données statiques et les packages de configuration correspondant au type de service. Les fichiers contenus dans le répertoire de package de services sont référencés par le fichier `ApplicationManifest.xml` du type d’application. Par exemple, un package de services peut faire référence au code, aux données statiques et aux packages de configuration qui composent un service de base de données.

**Service nommé** : après avoir créé une application nommée, vous pouvez créer une instance de l’un de ses types de service au sein du cluster. Vous spécifiez le type de service à l’aide de son nom/sa version. Chaque instance du type de service reçoit un nom d’URI inclus dans l’étendue de l’URI de son application nommée. Par exemple, si vous créez un service nommé « MyDatabase » dans une application nommée « MyNamedApp », l’URI ressemble à ceci : `"fabric:/MyNamedApp/MyDatabase"`. Dans une application nommée, vous pouvez créer plusieurs services nommés. Chaque service nommé peut posséder son propre schéma de partition et son propre nombre d’instances/de réplicas.

**Package de code** : répertoire de disque contenant les fichiers exécutables du type de service (généralement des fichiers DLL/EXE). Les fichiers contenus dans le répertoire de package de code sont référencés par le fichier `ServiceManifest.xml` du type de service. Quand vous créez un service nommé, le package de code est copié vers le ou les nœuds sélectionnés pour exécuter le service nommé. Ensuite, le code commence à s’exécuter. Il existe deux types d’exécutable de code de package :

* **Exécutables invités** : exécutables qui s’exécutent tels quels sur le système d’exploitation hôte (Windows ou Linux). Ces exécutables ne sont pas liés ou ne font pas référence aux fichiers exécutables de Service Fabric. Par conséquent, ils n’utilisent pas les modèles de programmation de Service Fabric. Ces fichiers exécutables ne peuvent pas utiliser certaines des fonctionnalités de Service Fabric, notamment le service de nommage pour la découverte du point de terminaison. Les exécutables invités ne peuvent pas rapporter des métriques de charge propres à chaque instance de service.
* **Exécutables d’hôte de service** : exécutables qui utilisent les modèles de programmation de Service Fabric en les liant aux fichiers exécutables de Service Fabric, donnant ainsi accès à des fonctionnalités de Service Fabric. Par exemple, une instance de service nommé peut inscrire les points de terminaison avec le service d’affectation de noms de Service Fabric et indiquer les mesures de chargement.      

**Package de données** : répertoire de disque contenant les fichiers de données en lecture seule, statiques du type de service (en général, des fichiers audio et vidéo, et des fichiers de photos). Les fichiers contenus dans le répertoire de package de données sont référencés par le fichier `ServiceManifest.xml` du type de service. Quand vous créez un service nommé, le package de données est copié vers le ou les nœuds sélectionnés pour exécuter le service nommé. Le code s’exécute et peut désormais accéder aux fichiers de données.

**Package de configuration** : répertoire de disque contenant les fichiers de configuration en lecture seule, statiques du type de service (en général, des fichiers texte). Les fichiers contenus dans le répertoire de package de configuration sont référencés par le fichier `ServiceManifest.xml` du type de service. Quand vous créez un service nommé, les fichiers du package de configuration sont copiés vers un ou plusieurs nœuds sélectionnés pour exécuter le service nommé. Ensuite, le code commence à s’exécuter et peut désormais accéder aux fichiers de configuration.

**Conteneurs** : par défaut, Service Fabric déploie et active ces services en tant que processus. Service Fabric peut également déployer des services dans les images de conteneur. La technologie d’un conteneur virtualise le système d’exploitation sous-jacent par rapport aux applications. Chaque application, son runtime, ses dépendances et ses bibliothèques système s’exécutent au sein d’un conteneur. Le conteneur bénéficie d’un accès privé complet à la propre vue isolée du conteneur sur les constructions du système d’exploitation. Service Fabric prend en charge les conteneurs Docker sur Linux et les conteneurs Windows Server. Pour plus d’informations, consultez [Service Fabric et conteneurs](service-fabric-containers-overview.md).

**Schéma de partition** : lors de la création d’un service nommé, vous indiquez un schéma de partition. Les services comportant de grandes quantités d’état fractionnent les données entre les partitions, ce qui permet de répartir l’état entre les nœuds du cluster. Ce fractionnement permet d’adapter l’état de votre service nommé. Dans une partition, les services nommés sans état ont des instances tandis que les services nommés avec état ont des réplicas. En règle générale, les services nommés sans état ne possèdent qu’une partition, car ils sont dépourvus d’état interne. Les instances de partition assurent la disponibilité. En cas d’échec d’une instance, les autres instances continuent de fonctionner normalement, puis Service Fabric en crée une. Comme les services nommés avec état conservent leur état dans les réplicas et que chaque partition possède son propre jeu de réplicas, l’état reste synchronisé. En cas d’échec d’un réplica, Service Fabric en génère un nouveau à partir des réplicas existants.

Pour plus d’informations, consultez l’article [Partitionnement des services fiables Service Fabric](service-fabric-concepts-partitioning.md) .

## <a name="system-services"></a>Services système
Il existe des services système qui sont créés dans chaque cluster et qui fournissent les capacités de plateforme de Service Fabric.

**Service de nommage** : chaque cluster Service Fabric a un service de nommage qui résout le nom des services vers un emplacement du cluster. Vous gérez le nom et les propriétés des services, comme un système DNS (Domain Name System) Internet du cluster. Les clients communiquent en toute sécurité avec n’importe quel nœud du cluster grâce au service de nommage pour résoudre un nom de service et son emplacement. Les applications se déplacent au sein du cluster, suite, par exemple, à des échecs, à un équilibrage des ressources ou au redimensionnement du cluster. Vous pouvez développer des services et des clients capables de résoudre l’emplacement réseau actuel. Les clients obtiennent l’adresse IP réelle de l’ordinateur et le port utilisé.

Pour plus d’informations sur les API de communication de client et de service qui fonctionnent avec le service de nommage, consultez [Communiquer avec les services](service-fabric-connect-and-communicate-with-services.md).

**Service de magasin d’images** : chaque cluster Service Fabric a un service de magasin d’images où sont conservés les packages d’application avec version déployés. Copiez un package d’application dans le magasin d’images, puis inscrivez le type d’application contenu dans ce package d’application. Une fois le type d’application configuré, créez une application nommée à partir de celui-ci. Vous pouvez annuler l’inscription d’un type d’application dans le service de magasin d’images après que toutes ses applications nommées ont été supprimées.

Consultez [Comprendre le paramètre ImageStoreConnectionString](service-fabric-image-store-connection-string.md) pour plus d’informations sur le service de magasin d’images.

Pour plus d’informations sur le déploiement d’applications sur le service de magasin d’images, consultez l’article [Déployer une application](service-fabric-deploy-remove-applications.md).

**Service Failover Manager (FM)** : chaque cluster Service Fabric possède un service Failover Manager, chargé des actions suivantes :
   - fonctions liées à la haute disponibilité et à la cohérence des services ;
   - orchestrer les mises à niveau des applications et des clusters ;
   - interagir avec d’autres composants système.

**Service de gestion des réparations** : ce service système optionnel permet d’effectuer des réparations sur un cluster de manière sécurisée, automatisable et transparente. Le service de gestion des réparations est utilisé dans les cas suivants :
   - Maintenance d’Azure sur les clusters Azure Service Fabric avec les niveaux de [durabilité Silver et Gold](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster).
   - Réparations pour l’[application d’orchestration des correctifs](service-fabric-patch-orchestration-application.md)

## <a name="built-in-programming-models"></a>Modèles de programmation intégrés
Des modèles de programmation .NET Framework et Java vous permettent de générer des services Service Fabric :

**Reliable Services**: API permettant de générer des services avec et sans état. Les services avec état stockent leur état dans les collections fiables (par exemple, un dictionnaire ou une file d’attente). Vous pouvez également accéder à diverses piles de communication comme l’API web et WCF (Windows Communication Foundation).

**Reliable Actors**: API permettant de générer des objets avec et sans état via le modèle de programmation Actor virtuel. Ce modèle est utile si vous avez un grand nombre d’unités indépendantes d’état/de calcul. Comme ce modèle utilise un modèle de thread en alternance, il convient d’éviter un code qui appelle d’autres acteurs ou services dans la mesure où un acteur individuel ne peut pas traiter les autres demandes entrantes tant que toutes ses demandes sortantes ne sont pas terminées.

Vous pouvez également exécuter vos applications existantes sur Service Fabric :

**Conteneurs** : Service Fabric prend en charge le déploiement de conteneurs Docker sous Linux et de conteneurs Windows Server sous Windows Server 2016. Il gère également le mode d’isolation Hyper-V. Dans le [modèle d’application](service-fabric-application-model.md)Service Fabric, un conteneur représente un hôte d’application sur lequel sont placés plusieurs réplicas de service. Service Fabric peut exécuter n’importe quel conteneur et le scénario est identique au scénario des exécutables invités, dans lequel vous empaquetez une application existante au sein d’un conteneur. Vous pouvez également exécuter des [services Service Fabric à l’intérieur de conteneurs](service-fabric-services-inside-containers.md).

**Exécutables invités** : vous pouvez exécuter n’importe quel type de code, comme Node.js, Java ou C++ dans Azure Service Fabric en tant que service. Dans la terminologie Service Fabric, ces types de services sont appelés exécutables invités et traités comme des services sans état. La haute disponibilité, le contrôle d’intégrité, la gestion du cycle de vie des applications, la haute densité et la fonctionnalité de découverte font partie des avantages de l’exécution d’un exécutable invité dans un cluster Service Fabric.

Pour plus d’informations, consultez l’article [Choisir un modèle de programmation pour votre service](service-fabric-choose-framework.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Service Fabric :

* [Vue d'ensemble de Service Fabric](service-fabric-overview.md)
* [Pourquoi une approche de microservices pour la conception d’applications ?](service-fabric-overview-microservices.md)
* [Scénarios d’application](service-fabric-application-scenarios.md)


