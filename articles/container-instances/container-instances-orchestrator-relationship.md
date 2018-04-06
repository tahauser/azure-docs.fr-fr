---
title: Azure Container Instances et l’orchestration de conteneur
description: Découvrez comment se passe l’interaction entre Azure Container Instances et les orchestrateurs de conteneurs.
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/23/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 79dcb4be49791e84efa99b36b1c0a0d474a372f4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances et les orchestrateurs de conteneurs

En raison de leur taille réduite et de leur orientation d’application, les conteneurs conviennent parfaitement aux environnements de distribution agiles et aux architectures basées sur les microservices. La tâche d’automatisation et de gestion d’un grand nombre de conteneurs et de leur interaction porte le nom d’*orchestration*. Les orchestrateurs de conteneurs les plus courants sont Kubernetes, DC/OS et Docker Swarm.

Azure Container Instances fournit certaines des fonctionnalités de planification de base des plateformes d’orchestration. Et bien qu’il ne couvre pas les services de valeur plus élevée fournis par ces plateformes, Azure Container Instances peut leur être complémentaire. Cet article décrit l’étendue des services gérés par Azure Container Instances et explique comment les orchestrateurs de conteneurs complets peuvent interagir avec lui.

## <a name="traditional-orchestration"></a>Orchestration traditionnelle

La définition standard de l’orchestration comprend les tâches suivantes :

- **Planification** : étant donné une image conteneur et une demande de ressource, rechercher un ordinateur adapté sur lequel exécuter le conteneur.
- **Affinité/Anti-affinité** : spécifier qu’un ensemble de conteneurs doivent s’exécuter à proximité les uns des autres (à des fins de performances) ou suffisamment éloignés les uns des autres (à des fins de disponibilité).
- **Contrôle d’intégrité** : détecter les échecs de conteneur et les replanifier automatiquement.
- **Basculement** : effectuer le suivi de ce qui s’exécute sur chaque machine et replanifier des conteneurs à partir des machines ayant échoué sur des nœuds intègres.
- **Mise à l’échelle** : ajouter ou supprimer des instances de conteneurs en fonction de la demande, manuellement ou automatiquement.
- **Mise en réseau** : fournir une surcouche réseau pour la coordination des conteneurs et la communication entre plusieurs ordinateurs hôtes.
- **Détection de service** : permettre aux conteneurs de se trouver mutuellement et automatiquement, même quand ils sont déplacés entre des machines hôtes et que leur adresse IP est modifiée.
- **Coordination des mises à niveau d’application** : gérer les mises à niveau des conteneurs pour éviter les temps d’arrêt des applications et autoriser une restauration en cas de problème.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Orchestration avec Azure Container Instances : une approche en couches

Azure Container Instances permet d’adopter une approche en couches de l’orchestration. Vous bénéficiez ainsi de toutes les fonctionnalités de planification et de gestion nécessaires pour exécuter un seul conteneur, tout en permettant aux plateformes d’orchestration de gérer les tâches à conteneurs multiples.

Toute l’infrastructure sous-jacente pour Container Instances étant managée par Azure, une plateforme d’orchestration n’a pas besoin de rechercher une machine hôte appropriée sur laquelle exécuter un conteneur unique. L’élasticité du cloud garantit qu’un ordinateur est toujours disponible. Au lieu de cela, l’orchestrateur peut se concentrer sur les tâches qui simplifient le développement des architectures multi-conteneurs, notamment la mise à l’échelle et la coordination des mises à niveau.

## <a name="potential-scenarios"></a>Scénarios potentiels

Bien que l’intégration d’orchestrateur avec Azure Container Instances en soit encore à sa phase initiale, nous prévoyons l’émergence de plusieurs environnements différents :

### <a name="orchestration-of-container-instances-exclusively"></a>Orchestration exclusive de Container Instances

Du fait du démarrage rapide et de la facturation à la seconde, un environnement basé exclusivement sur Azure Container Instances offre le moyen le plus rapide de bien démarrer et de faire face à des charges de travail très variables.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Combinaison de Container Instances et de conteneurs sur des machines virtuelles

Pour les charges de travail longues et stables, l’orchestration des conteneurs dans un cluster de machines virtuelles dédiées est généralement moins coûteuse que l’exécution des mêmes conteneurs avec Azure Container Instances. Toutefois, Container Instances offre une excellente solution pour développer et contracter rapidement votre capacité globale en cas de pics d’utilisation inattendus ou de courte durée.

Au lieu de mettre à l’échelle le nombre de machines virtuelles dans votre cluster et de déployer ensuite des conteneurs supplémentaires sur ces machines, l’orchestrateur peut simplement planifier les conteneurs supplémentaires à l’aide d’Azure Container Instances et les supprimer quand ils ne sont plus nécessaires.

## <a name="sample-implementation-azure-container-instances-connector-for-kubernetes"></a>Exemple d’implémentation : connecteur Azure Container Instances pour Kubernetes

Pour illustrer comment les plateformes d’orchestration de conteneurs peuvent s’intégrer à Azure Container Instances, nous avons commencé à créer un [exemple de connecteur pour Kubernetes][aci-connector-k8s].

Le connecteur pour Kubernetes imite le [kubelet][kubelet-doc] en s’inscrivant en tant que nœud avec une capacité illimitée et en répartissant la création de [pods][pod-doc] en tant que groupes de conteneurs dans Azure Container Instances.

Nous pourrions générer des connecteurs pour d’autres orchestrateurs qui s’intégreraient de la même façon avec des primitives de plateforme pour combiner la puissance de l’API d’orchestrateur avec la vitesse et la simplicité offertes par la gestion des conteneurs dans Azure Container Instances.

> [!WARNING]
> Le connecteur ACI pour Kubernetes est *expérimental* et ne doit pas être utilisé en production.

## <a name="next-steps"></a>Étapes suivantes

Créez votre premier conteneur avec Azure Container Instances à l’aide du [guide de démarrage rapide](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/virtual-kubelet/tree/master/providers/azure
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/