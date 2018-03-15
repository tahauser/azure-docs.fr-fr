---
title: "Créer des clusters Azure Service Fabric sur Windows Server et Linux | Microsoft Docs"
description: "Les clusters Service Fabric peuvent être exécutés sous Windows Server et Linux, ce qui vous permet de déployer et d’héberger des applications Service Fabric partout où vous pouvez exécuter Windows Server ou Linux."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/28/2018
ms.author: dekapur
ms.openlocfilehash: 63b7bfa5ca357470f5ed36a4cdf943cee779f0fc
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Créer des clusters Service Fabric sur un système Windows Server ou Linux
Un cluster Azure Service Fabric est un groupe de machines virtuelles ou physiques connectées au réseau, dans lequel vos microservices sont déployés et gérés. Une machine ou machine virtuelle faisant partie d’un cluster est appelée un nœud de cluster. Les clusters peuvent être mis à l’échelle pour des milliers de nœuds. Si vous ajoutez des nœuds au cluster, Service Fabric rééquilibre les réplicas de partition du service et les instances sur le nombre de nœuds augmenté. Les performances globales de l’application s’améliorent tandis que le conflit d’accès à la mémoire diminue. Si les nœuds du cluster ne sont pas utilisés efficacement, vous pouvez diminuer le nombre de nœuds dans le cluster. Service Fabric rééquilibre à nouveau les réplicas de partition et les instances sur le nombre réduit de nœuds afin de mieux utiliser le matériel sur chaque nœud.

Service Fabric permet la création de clusters Service Fabric sur toute machine virtuelle ou tout ordinateur exécutant Windows Server ou Linux. Cela signifie que vous pouvez déployer et exécuter des applications Service Fabric dans n’importe quel environnement dans lequel des ordinateurs Windows Server ou Linux sont interconnectés, que ce soit en local, sur Microsoft Azure ou via un autre fournisseur cloud.

## <a name="create-service-fabric-clusters-on-azure"></a>Créer des clusters Service Fabric sur Azure
La création d’un cluster dans Azure est effectuée via un modèle Resource Manager ou via le [portail Azure](https://portal.azure.com). Pour en savoir plus, voir [Créer un cluster Service Fabric à l’aide d’un modèle Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) ou [Créer un cluster Service Fabric à partir du portail Azure](service-fabric-cluster-creation-via-portal.md).

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Systèmes d’exploitation pris en charge pour les clusters dans Azure
Vous pouvez créer des clusters sur des machines virtuelles qui exécutent ces systèmes d’exploitation :

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Linux Ubuntu 16.04

> [!NOTE]
> Si vous décidez de déployer Service Fabric sur Windows Server 1709, notez que (1) ce n’est pas une branche destinée à des services à long terme, ce qui vous obligera probablement à changer de versions à l’avenir, et (2) si vous déployez des conteneurs, les conteneurs reposant sur Windows Server 2016 ne fonctionnent pas sur Windows Server 1709, et vice versa (vous devez les régénérer pour les déployer).
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>Créer des clusters Service Fabric autonomes locaux ou avec n’importe quel fournisseur cloud
Service Fabric fournit un package d’installation vous permettant de créer des clusters Service Fabric sur site ou sur n’importe que fournisseur cloud.

Pour en savoir plus sur la configuration de clusters Service Fabric autonomes sur Windows Server, consultez [Création d’un cluster Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md).

  > [!NOTE]
  > Les clusters autonomes ne sont actuellement pas pris en charge pour Linux. Linux est pris en charge avec les clusters à boîtier unique pour le développement et les clusters de plusieurs ordinateurs Azure Linux.
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Déploiements cloud ou en local
Le processus de création d’un cluster Service Fabric en local est similaire au processus de création d’un cluster sur un cloud de votre choix avec un ensemble de machines virtuelles. Les étapes initiales d’approvisionnement des machines virtuelles sont régies par le fournisseur de cloud ou l’environnement local que vous utilisez. Une fois que vous avez un ensemble de machines virtuelles avec une connectivité réseau activée entre elles, les étapes suivantes pour configurer le package Service Fabric, modifier les paramètres du cluster et exécuter les scripts de création et de gestion du cluster sont identiques. Cela garantit que vos connaissances et votre expérience en matière d’exploitation et de gestion des clusters Service Fabric peuvent être transférées lorsque vous choisissez de cibler de nouveaux environnements d’hébergement.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Avantages de la création de clusters Service Fabric autonomes
* Vous pouvez choisir n’importe quel fournisseur de cloud pour héberger votre cluster.
* Une fois écrites, les applications Service Fabric peuvent être exécutées dans plusieurs environnements d’hébergement sans modification ou avec des modifications minimales.
* Les connaissances en matière de génération d’applications Service Fabric s’appliquent d’un environnement d’hébergement à un autre.
* L’expérience opérationnelle d’exécution et de gestion de clusters Service Fabric s’applique d’un environnement à un autre.
* Ciblage étendu en termes de clientèle sans contrainte d’environnement d’hébergement.
* Une couche supplémentaire de fiabilité et de protection contre les pannes répandues existe, car vous pouvez déplacer les services vers un autre environnement de déploiement si un centre de données ou le fournisseur de services cloud est confronté à une panne d’électricité.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Systèmes d’exploitation pris en charge pour les clusters autonomes
Vous pouvez créer des clusters sur des machines virtuelles ou des ordinateurs qui exécutent ces systèmes d’exploitation (Linux n’est pas encore pris en charge) :

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Avantages des clusters Service Fabric sur Azure par rapport aux clusters Service Fabric créés en local
L’exécution de clusters Service Fabric sur Azure offre des avantages par rapport à l’option locale. Par conséquent, si vous n’avez pas d’exigences spécifiques concernant l’emplacement où vous exécutez vos clusters, nous vous suggérons de les exécuter en tant que clusters hébergés par Azure. Dans Azure, nous intégrons d’autres fonctionnalités et services Azure qui rendent l’exploitation et la gestion du cluster plus simple et plus fiable.

* **Portail Azure :** facilite la création et la gestion de clusters.
* **Azure Resource Manager :** l’utilisation d’Azure Resource Manager permet de faciliter la gestion de toutes les ressources utilisées par le cluster en tant qu’unité et de simplifier le suivi des coûts et la facturation.
* **Cluster Service Fabric en tant que ressource Azure :** un cluster Service Fabric est une ressource Azure, que vous pouvez modeler comme d’autres ressources dans Azure.
* **Intégration à l’infrastructure Azure :** Service Fabric se coordonne avec l’infrastructure Azure sous-jacente pour que le système d’exploitation, le réseau et d’autres mises à niveau améliorent la disponibilité et la fiabilité de vos applications.  
* **Diagnostics :** dans Azure, nous proposons l’intégration d’Azure Diagnostics et de Log Analytics.
* **Mise à l’échelle automatique :** pour les clusters sur Azure, nous fournissons une fonctionnalité de mise à l’échelle automatique intégrée provenant des jeux de mise à l’échelle de machine virtuelle. Dans des environnements locaux ou d’autres environnements cloud, vous devez créer votre propre fonctionnalité de mise à l’échelle automatique ou mettre à l’échelle manuellement à l’aide des API que Service Fabric expose pour la mise à l’échelle des clusters.

## <a name="next-steps"></a>étapes suivantes

* Créer un cluster sur des machines virtuelles ou des ordinateurs exécutant Windows Server : [Création d’un cluster Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md)
* Créer un cluster sur des machines virtuelles ou des ordinateurs exécutant Linux : [Créer un cluster Linux](service-fabric-cluster-creation-via-portal.md)
* En savoir plus sur les [options de prise en charge de Service Fabric](service-fabric-support.md)

