---
title: "Hébergement du conteneur Docker dans Azure Cloud"
description: "Azure Container Service offre un moyen de simplifier la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications en conteneur."
services: container-service
author: rgardler
manager: timlt
ms.service: container-service
ms.topic: overview
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 94ae759803bc3a65ec7dc9a8e01486625a638368
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="introduction-to-docker-container-hosting-solutions-with-azure-container-service"></a>Introduction aux solutions d’hébergement de conteneur Docker solutions avec Azure Container Service 

Azure Container Service simplifie la création, la configuration et la gestion d’un cluster de machines virtuelles préconfigurées pour exécuter des applications en conteneur. Ce service utilise une configuration optimisée d’outils de planification et d’orchestration open source bien connus. Il vous permet d’exploiter vos compétences existantes ou de faire appel à une large communauté d’experts toujours plus nombreux pour déployer et gérer des applications en conteneur sur Microsoft Azure.

![Azure Container Service offre un moyen de gérer les applications en conteneur sur plusieurs hôtes dans Azure.](./media/acs-intro/acs-cluster-new.png)

Azure Container Service utilise le format de conteneur Docker pour assurer la portabilité complète de vos conteneurs d’application. Par ailleurs, il prend en charge votre choix d’utiliser Marathon et DC/OS, Docker Swarm ou Kubernetes pour vous permettre de mettre à l’échelle ces applications pour des milliers, voire des dizaines de milliers de conteneurs.

En utilisant Azure Container Service, vous pouvez tirer parti des fonctionnalités d’entreprise d’Azure tout en conservant la portabilité des applications, notamment au niveau des couches d’orchestration.

## <a name="using-azure-container-service"></a>Utilisation d’Azure Container Service
L’objectif d’Azure Container Service est de proposer un environnement d’hébergement de conteneurs basé sur des outils et des technologies open source déjà bien connus de nos clients. À cette fin, nous exposons les points de terminaison des API standard pour l’orchestrator de votre choix (DC/OS, Docker Swarm ou Kubernetes). Vous pouvez alors exploiter n’importe quel logiciel capable de communiquer avec ces points de terminaison. Par exemple, dans le cas du point de terminaison Docker Swarm, vous pouvez choisir d’utiliser l’interface de ligne de commande Docker. Pour DC/OS, vous pouvez choisir l’interface CLI DCOS. Pour Kubernetes, vous pouvez choisir `kubectl`.

## <a name="creating-a-docker-cluster-by-using-azure-container-service"></a>Création d’un cluster Docker en utilisant Azure Container Service
Pour commencer à utiliser Azure Container Service, vous déployez un cluster Azure Container Service via le portail (recherchez **Azure Container Service** sur Marketplace), à l’aide d’un modèle Azure Resource Manager ([Docker Swarm](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-swarm), [DC/OS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-dcos) ou [Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes)) ou à l’aide [d’Azure CLI 2.0](container-service-create-acs-cluster-cli.md). Les modèles de démarrage rapide fournis peuvent être modifiés pour inclure une configuration Azure supplémentaire ou avancée. Pour plus d’informations, consultez [Déployer un cluster Azure Container Service](container-service-deployment.md).

## <a name="deploying-an-application"></a>Déploiement d’une application
Azure Container Service propose le choix entre Docker Swarm, DC/OS ou Kubernetes pour l’orchestration. Le déploiement de votre application dépend de votre choix en termes d’orchestrateur.

### <a name="using-dcos"></a>Utilisation de DC/OS
DC/OS est un système d’exploitation distribué basé sur le noyau de systèmes distribués Apache Mesos. Hébergé par l’Apache Software Foundation, Apache Mesos répertorie certains des [plus grands noms des technologies de l’information](http://mesos.apache.org/documentation/latest/powered-by-mesos/) en tant qu’utilisateurs et contributeurs.

![Azure Container Service configuré pour DC/OS présentant des agents et des maîtres.](media/acs-intro/dcos.png)

DC/OS et Apache Mesos intègrent un impressionnant ensemble de fonctionnalités :

* Extensibilité éprouvée
* Serveurs maître et subordonnés répliqués et à tolérance de panne avec Apache ZooKeeper
* Prise en charge des conteneurs de format Docker
* Isolement natif entre les tâches avec des conteneurs Linux
* Planification de ressources multiples (mémoire, UC, disque et ports)
* API Java, Python et C++ pour le développement de nouvelles applications parallèles
* Interface utilisateur web pour l’affichage de l’état du cluster

Par défaut, le système DC/OS exécuté sur le service de conteneur Azure inclut la plateforme d’orchestration Marathon pour la planification des charges de travail. Toutefois, l’ensemble de services Mesosphere Universe est inclus avec le déploiement DC/OS d’ACS et il peut être ajouté à votre service. Ces services incluent Spark, Hadoop, Cassandra et bien plus encore.

![DC/OS Universe dans Azure Container Service](media/dcos/universe.png)

#### <a name="using-marathon"></a>Utilisation de Marathon
Marathon est un système d’initialisation et de contrôle à l’échelle du cluster destiné à des services de groupes de contrôle (ou « cgroups ») ou, dans le cas d’Azure Container Service, des conteneurs de format Docker. Marathon propose une interface utilisateur web à partir de laquelle vous pouvez déployer vos applications. L’URL qui permet d’y accéder ressemble à `http://DNS_PREFIX.REGION.cloudapp.azure.com`, où DNS\_PREFIX et REGION sont deux valeurs définies au moment du déploiement. Bien sûr, vous pouvez également fournir votre propre nom DNS. Pour plus d’informations sur l’exécution d’un conteneur à l’aide de l’interface utilisateur web Marathon, consultez [Gestion de conteneur DC/OS via l’interface utilisateur web Marathon](container-service-mesos-marathon-ui.md).

![Liste des applications Marathon](media/dcos/marathon-applications-list.png)

Vous pouvez également utiliser des API REST pour communiquer avec Marathon. Plusieurs bibliothèques clientes sont disponibles pour chaque outil. Elles couvrent une variété de langages et il va de soi que vous pouvez utiliser le protocole HTTP dans n’importe quel langage. De plus, de nombreux outils DevOps bien connus prennent en charge Marathon. Votre équipe en charge des opérations profite ainsi d’une flexibilité maximale quand vous utilisez un cluster Azure Container Service. Pour plus d’informations sur l’exécution d’un conteneur à l’API REST Marathon, consultez [Gestion de conteneur DC/OS via l’API REST Marathon](container-service-mesos-marathon-rest.md).

### <a name="using-docker-swarm"></a>Utilisation de Docker Swarm
Docker Swarm assure un clustering natif pour Docker. Étant donné que Docker Swarm dessert l’API Docker standard, tout outil qui communique déjà avec un démon Docker peut utiliser Swarm pour évoluer de façon transparente vers plusieurs hôtes sur Azure Container Service.

![Azure Container Service configuré pour utiliser Swarm.](media/acs-intro/acs-swarm2.png)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Les outils pris en charge pour la gestion des conteneurs sur un cluster Swarm incluent, sans s’y limiter, les suivants :

* Dokku
* Interface de ligne de commande Docker et Docker Compose
* Krane
* Jenkins

### <a name="using-kubernetes"></a>Utilisation de Kubernetes
Kubernetes est un outil orchestrator de conteneur populaire, open source, à l’échelle de la production. Kubernetes automatise le déploiement, la mise à l’échelle et la gestion des applications en conteneur. Étant donné qu’il s’agit d’une solution Open Source pilotée par la communauté Open Source, elle s’exécute de manière transparente sur Azure Container Service et peut être utilisée pour déployer des conteneurs à grande échelle sur Azure Container Service.

![Azure Container Service configuré pour utiliser Kubernetes.](media/acs-intro/kubernetes.png)

Il possède un jeu complet de fonctionnalités, notamment :
* Mise à l’échelle horizontale
* Détection de service et équilibrage de charge
* Secrets et gestion de la configuration
* API basée sur les déploiements et restaurations automatisés
* Réparation spontanée

## <a name="videos"></a>Vidéos
Prise en main d’Azure Container Service (101) :  

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Container-Service-101/player]
>
>

Création d’applications à l’aide du service de conteneur Azure (Build 2016)

> [!VIDEO https://channel9.msdn.com/Events/Build/2016/B822/player]
>
>

## <a name="next-steps"></a>Étapes suivantes

Déployez un cluster de service de conteneur à l’aide du [portail](container-service-deployment.md) ou [d’Azure CLI 2.0](container-service-create-acs-cluster-cli.md).