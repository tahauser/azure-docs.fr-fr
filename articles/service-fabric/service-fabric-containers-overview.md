---
title: Vue d’ensemble de Service Fabric et des conteneurs | Microsoft Docs
description: Voici une vue d’ensemble de Service Fabric et de la méthode à suivre pour déployer des applications de microservices au moyen de conteneurs. Cet article fournit une vue d’ensemble de l’utilisation de conteneurs et des fonctionnalités disponibles dans Service Fabric.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/20/2017
ms.author: msfussell
ms.openlocfilehash: f12dc08953372b2dfae773df11cf1f47b42a1b89
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="service-fabric-and-containers"></a>Service Fabric et conteneurs
> [!NOTE]
> Le déploiement de conteneurs sur un cluster Service Fabric sous Windows 10 ou avec Docker CE n’est pas pris en charge. 
>   

> [!NOTE]
> Service Fabric version 6.1 prend en charge la version préliminaire de Windows Server version 1709. La mise en réseau ouvert et le service DNS dans Service Fabric ne fonctionnent pas avec Windows Server version 1709. 
> 

## <a name="introduction"></a>Introduction
Azure Service Fabric est un [orchestrateur](service-fabric-cluster-resource-manager-introduction.md) de services sur un cluster de machines. Il profite des nombreuses années d’expérience de Microsoft en matière d’utilisation et d’optimisation de services à très grande échelle. Les services peuvent être développés de nombreuses façons, via des [modèles de programmation de Service Fabric](service-fabric-choose-framework.md) ou via le déploiement [d’exécutables invités](service-fabric-guest-executables-introduction.md). Par défaut, Service Fabric déploie et active ces services en tant que processus. Ces processus assurent l’activation la plus rapide et offrent la densité la plus élevée en matière de ressources dans un cluster. Service Fabric peut également déployer des services dans les images de conteneur. Important : dans les conteneurs, vous pouvez combiner des processus et des services au sein de la même application.   

## <a name="what-are-containers"></a>Qu’est-ce qu’un conteneur ?
Les conteneurs sont des composants encapsulés pouvant être déployés au cas par cas, qui sont exécutés en tant qu’instances isolées sur le même noyau. Ils tirent parti de la virtualisation fournie par le système d’exploitation. Cela signifie que chaque application, son runtime, ses dépendances et ses bibliothèques système s’exécutent au sein d’un conteneur bénéficiant d’un accès privé complet à leur propre vue isolée du conteneur sur les constructions du système d’exploitation. En parallèle avec la portabilité, ce degré de sécurité et d’isolement des ressources est le principal avantage associé à l’utilisation de conteneurs avec Service Fabric, qui exécute les services dans des processus, par ailleurs.

La technologie d’un conteneur virtualise le système d’exploitation sous-jacent par rapport aux applications. Les conteneurs offrent un environnement immuable permettant aux applications de s’exécuter avec un certain degré d’isolation. Les conteneurs s’exécutent directement sur le noyau et disposent d’une vue isolée sur le système de fichiers et d’autres ressources. Par rapport aux machines virtuelles, les conteneurs présentent les avantages suivants :

* **Une taille réduite** : les conteneurs utilisent un espace de stockage unique ainsi que les versions et mises à jour de la couche pour une efficacité accrue.
* **Démarrage rapide** : comme les conteneurs n’ont pas besoin d’initialiser l’intégralité d’un système d’exploitation, ils peuvent démarrer beaucoup plus rapidement, généralement en quelques secondes.
* **Portabilité** : une image d’application en conteneur peut être portée de manière à s’exécuter dans le cloud ou sur site, à l’intérieur de machines virtuelles ou directement sur des machines physiques.
* **Gouvernance des ressources** : le nombre de ressources physiques qu’un conteneur peut consommer sur son hôte peut être limité.

## <a name="container-types-and-supported-environments"></a>Types de conteneurs et environnements pris en charge
Service Fabric prend en charge les conteneurs à la fois sur Linux et Windows, et prend également en charge le mode d’isolation Hyper-V sur ce dernier. 

> [!NOTE]
> Le déploiement de conteneurs sur un cluster Service Fabric sous Windows 10 n’est pas pris en charge actuellement. 
> 

### <a name="docker-containers-on-linux"></a>Conteneurs Docker sur Linux
Docker fournit des API de haut niveau pour créer et gérer des conteneurs en plus des conteneurs du noyau Linux. Docker Hub est un référentiel central permettant de stocker et de récupérer des images de conteneur.
Pour obtenir un didacticiel, consultez [Déployer un conteneur Docker sur Service Fabric](service-fabric-get-started-containers-linux.md).

### <a name="windows-server-containers"></a>Conteneurs Windows Server
Windows Server 2016 fournit deux types de conteneurs, qui proposent des niveaux d’isolation différents. Les conteneurs Windows Server sont similaires aux conteneurs Docker, en ce sens qu’ils proposent tous l’isolation des systèmes de fichiers et espaces de noms. Toutefois, ils partagent le noyau avec l’hôte sur lequel ils s’exécutent. Sur Linux, cette isolation est généralement fournie par `cgroups` et `namespaces`. Les conteneurs Windows Server se comportent de la même manière.

Les conteneurs Windows avec prise en charge Hyper-V proposent un niveau d’isolation et de sécurité plus élevé, car aucun d’entre eux ne partage le noyau du système d’exploitation avec les autres, ni avec l’hôte. Grâce à ce niveau élevé d’isolation à des fins de sécurité, les conteneurs compatibles Hyper-V sont destinés à des scénarios hostiles d’architecture mutualisée.
Pour obtenir un didacticiel, consultez [Déployer un conteneur Windows sur Service Fabric](service-fabric-get-started-containers.md).

La figure suivante illustre les différents types de virtualisation et niveaux d’isolation disponibles dans le système d’exploitation.
![Plateforme Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Scénarios d’utilisation des conteneurs
Voici des exemples pour lesquels le conteneur est un bon choix :

* **Opération lift-and-shift pour IIS** : si vous avez des applications [ASP.NET MVC](https://www.asp.net/mvc) et que vous souhaitez continuer à les utiliser, placez-les dans un conteneur au lieu de les migrer vers ASP.NET Core. Ces applications ASP.NET MVC dépendent des services Internet (IIS, Internet Information Services). Vous pouvez empaqueter ces applications dans des images de conteneur à partir de l’image IIS créée au préalable, puis les déployer avec Service Fabric. Pour plus d’informations sur les conteneurs Windows, consultez la page [Images conteneurs sur Windows Server](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server).
* **Mélange de conteneurs et de microservices Service Fabric** : utilisez une image de conteneur existante pour une partie de votre application. Par exemple, vous pouvez utiliser le [conteneur NGINX](https://hub.docker.com/_/nginx/) pour le système frontal web de votre application et les services avec état pour les calculs les plus intenses du back-end.
* **Réduction de l’impact des services de « voisins bruyants »** : vous pouvez utiliser la capacité de gouvernance des ressources des conteneurs pour limiter les ressources utilisées par un service sur un hôte. Si les services sont susceptibles de consommer un grand nombre de ressources et, de ce fait, d’affecter les performances d’autres services (opération de type requête exécutée sur le long terme, par exemple), vous pouvez envisager de les placer dans des conteneurs soumis à la gouvernance des ressources.

## <a name="service-fabric-support-for-containers"></a>Prise en charge des conteneurs par Service Fabric
Service Fabric prend en charge le déploiement de conteneurs Docker sous Linux et de conteneurs Windows Server sous Windows Server 2016. Il gère également le mode d’isolation Hyper-V. 

Dans le [modèle d’application](service-fabric-application-model.md)Service Fabric, un conteneur représente un hôte d’application sur lequel sont placés plusieurs réplicas de service. Service Fabric peut exécuter n’importe quel conteneur et le scénario est identique au [scénario des exécutables invités](service-fabric-guest-executables-introduction.md), dans lequel vous empaquetez une application existante au sein d’un conteneur. Ce scénario est le cas d’utilisation courant pour les conteneurs et les exemples incluent l’exécution d’une application écrite à l’aide de n’importe quel langage ou n’importe quelle infrastructure, mais pas à l’aide des modèles de programmation Service Fabric intégrés.

En outre, vous pouvez également exécuter des [services Service Fabric à l’intérieur de conteneurs](service-fabric-services-inside-containers.md). Cette prise en charge, pour l’instant limitée, sera améliorée dans les versions à venir.

Service Fabric dispose de plusieurs fonctionnalités de gestion des conteneurs, qui vous aident à créer des applications composées de microservices mis en conteneur. Service Fabric offre les fonctionnalités suivantes pour les services en conteneur :

* Activation et déploiement d’images de conteneur
* Gouvernance des ressources, notamment la définition des valeurs des ressources par défaut sur les clusters Azure.
* Authentification de référentiels.
* Mappage des ports de conteneur aux ports hôtes.
* Découverte et communication entre des conteneurs.
* Possibilité de configurer et de définir des variables d’environnement.
* Possibilité de définir des identifiants de sécurité sur le conteneur.
* Différents modes de mise en réseau pour les conteneurs.

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris ce qu’était un conteneur. Vous savez désormais que Service Fabric est un orchestrateur de conteneurs et qu’il fournit des fonctionnalités de prise en charge des conteneurs. Nous allons désormais parcourir ces différentes fonctionnalités, afin de vous indiquer comment les utiliser.

[Créer sa première application conteneur Service Fabric sous Windows](service-fabric-get-started-containers.md)

[Créer sa première application conteneur Service Fabric sous Linux](service-fabric-get-started-containers-linux.md)

[En savoir plus sur les conteneurs Windows](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
