---
title: "Registres de conteneurs Docker privés dans Azure"
description: "Présentation du service Azure Container Registry, proposant des registres Docker privés, gérés et basés sur le cloud."
services: container-registry
author: stevelas
manager: timlt
ms.service: container-registry
ms.topic: overview
ms.date: 01/22/2018
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: 7d9c259e6fb48189faef2a49fdba2578c9accfe9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Présentation des registres de conteneurs Docker privés dans Azure

Azure Container Registry est un service de [registre Docker](https://docs.docker.com/registry/) géré basé sur le Registre open source Docker 2.0. Créez et gérez des registres de conteneur Azure pour stocker et gérer vos images de [conteneur Docker](https://www.docker.com/what-docker) privées. Utilisez des registres de conteneur dans Azure avec vos pipelines de développement et de déploiement existants, et exploitez l’expertise de la communauté de Docker.

Pour en savoir plus Docker et des conteneurs, consultez le [guide d’utilisation Docker](https://docs.docker.com/engine/userguide/).

## <a name="use-cases"></a>Cas d'utilisation
Extrayez des images à partir d’un registre de conteneur Azure pour différents objectifs de déploiement :

* Des **systèmes d’orchestration évolutifs** qui gèrent des applications en conteneur sur des clusters d’hôtes, y compris du [contrôleur de domaine/système d’exploitation](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) et [Kubernetes](http://kubernetes.io/docs/).
* Des **services Azure** qui prennent en charge la création et l’exécution des applications à grande échelle, y compris [Container Service](../container-service/index.yml), [App Service](/app-service/index.md), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) et d’autres services.

Les développeurs peuvent également effectuer un push vers un registre de conteneur dans le cadre d’un flux de travail de développement de conteneur. Par exemple, ciblez un registre de conteneur à partir d’un outil de développement et d’intégration continu comme [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) ou [Jenkins](https://jenkins.io/).

## <a name="key-concepts"></a>Concepts clés
* **Registre** : créez au moins un registre de conteneur dans votre abonnement Azure. Les registres sont disponibles dans trois références (SKU) : [De base, Standard et Premium](container-registry-skus.md), chacune prenant en charge l’intégration de Webhook, l’authentification de registres avec Azure Active Directory et la fonctionnalité de suppression. Tirez parti du stockage local proche du réseau de vos images de conteneur en créant un registre dans le même emplacement Azure que vos déploiements. Utilisez la fonctionnalité [géoréplication](container-registry-geo-replication.md) des registres Premium pour les scénarios avancés de réplication et de distribution d’image conteneur. Un nom de Registre complet se présente sous la forme `myregistry.azurecr.io`.

  Vous [contrôlez l’accès](container-registry-authentication.md) à un registre de conteneur à l’aide d’un [principal de service](../active-directory/active-directory-application-objects.md) pris en charge par Azure Active Directory ou un compte d’administration fourni. Exécutez la commande `docker login` standard pour vous authentifier auprès d’un registre.

* **Référentiel** : un registre contient un ou plusieurs référentiels, qui sont des groupes d’images de conteneur. Azure Container Registry prend en charge les espaces de noms de référentiel à plusieurs niveaux. Dans le cas des espaces de noms à plusieurs niveaux, vous pouvez regrouper des collections d’images liées à une application spécifique, ou une collection d’applications à des équipes opérationnelles ou de développement précises. Par exemple : 

  * `myregistry.azurecr.io/aspnetcore:1.0.1` représente une image de l’entreprise.
  * `myregistry.azurecr.io/warrantydept/dotnet-build` représente une image utilisée pour créer des applications .NET, partagées par le service de garantie.
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` représente une image web, regroupée dans l’application des envois du client, détenue par le service de garantie.

* **Image** : stockée dans un référentiel, chaque image est une capture instantanée en lecture seule d’un conteneur Docker. Les registres de conteneur Azure peuvent inclure des images de Windows et Linux. Vous contrôlez les noms d’images pour tous les déploiements de votre conteneur. Utilisez des [commandes Docker](https://docs.docker.com/engine/reference/commandline/) standard pour envoyer les images dans un référentiel ou extraire une image d’un référentiel.

* **Conteneur** : un conteneur définit une application logicielle et ses dépendances encapsulées dans un système de fichiers complet, y compris le code, l’exécution, les outils système et les bibliothèques. Exécutez des conteneurs Docker basés sur des images Windows ou Linux que vous extrayez à partir d’un registre de conteneur. Les conteneurs en cours d’exécution sur un même ordinateur partagent le noyau du système d’exploitation. Les conteneurs Docker sont entièrement portables sur toutes les principales distributions Linux, macOS et Windows.

## <a name="next-steps"></a>Étapes suivantes
* [Créer un registre de conteneur à l’aide du portail Azure](container-registry-get-started-portal.md)
* [Créer un registre de conteneur à l’aide de l’interface de ligne de commande Azure](container-registry-get-started-azure-cli.md)
* [Effectuer un push de votre première image à l’aide de l’interface CLI Docker](container-registry-get-started-docker-cli.md)
