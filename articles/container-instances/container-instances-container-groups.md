---
title: Groupes de conteneurs Azure Container Instances
description: Découvrez comment fonctionnent les groupes de conteneurs dans Azure Container Instances
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/19/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 6f7f0d9aea86594140c302e6d12e6528e802b9e7
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="container-groups-in-azure-container-instances"></a>Groupes de conteneurs dans Azure Container Instances

La ressource de niveau supérieur dans Azure Container Instances est un *groupe de conteneurs*. Cet article décrit les groupes de conteneurs et les types de scénarios associés.

## <a name="how-a-container-group-works"></a>Fonctionnement d’un groupe de conteneurs

Un groupe de conteneurs est une collection de conteneurs qui sont planifiés sur le même ordinateur hôte. Les conteneurs d’un groupe de conteneurs partagent un cycle de vie, un réseau local et les volumes de stockage. Il s’apparente conceptuellement à un *pod* dans [Kubernetes][kubernetes-pod] et [DC/OS][dcos-pod].

Le diagramme suivant montre un exemple de groupe de conteneurs incluant plusieurs conteneurs :

![Diagramme de groupes de conteneurs][container-groups-example]

Le groupe de conteneurs donné en exemple :

* Est planifié sur un ordinateur hôte unique.
* Est affecté à une étiquette de nom DNS.
* Expose une adresse IP publique unique, avec exposition d’un seul port.
* Comprend deux conteneurs. Un conteneur écoute le port 80, l’autre le port 5000.
* Inclut deux partages de fichiers Azure en tant que montages de volume, et chaque conteneur monte l’un des partages localement.

> [!NOTE]
> Les groupes à plusieurs conteneurs sont actuellement restreints aux conteneurs Linux. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans [Disponibilité des régions et quotas pour Azure Container Instances](container-instances-quotas.md).

### <a name="deployment"></a>Déploiement

Les **groupes de conteneur** ont une allocation de ressources minimale de 1 processeur virtuel et de 1 Go de mémoire. Il est possible de provisionner des **conteneurs** individuels avec moins de 1 processeur virtuel et de 1 Go de mémoire. Dans un groupe de conteneurs, la distribution des ressources peut être personnalisée pour plusieurs conteneurs dans les limites établies au niveau du groupe de conteneurs. Par exemple, deux conteneurs disposant chacun de 0,5 processeur virtuel résidant dans un groupe de conteneurs auquel est alloué 1 processeur virtuel.

### <a name="networking"></a>Mise en réseau

Les groupes du conteneur partagent une adresse IP et un espace de noms de port sur cette adresse IP. Pour que les clients externes puissent atteindre un conteneur au sein du groupe, vous devez exposer le port sur l’adresse IP et à partir du conteneur. Étant donné que les conteneurs au sein du groupe partagent un espace de noms de port, le mappage de port n’est pas pris en charge. Les conteneurs au sein d’un groupe peuvent s’atteindre les uns les autres via localhost sur les ports qu’ils ont exposés, même si ces ports ne sont pas exposés en externe sur l’adresse IP du groupe.

### <a name="storage"></a>Stockage

Vous pouvez spécifier des volumes externes à monter dans un groupe de conteneurs. Vous pouvez mapper ces volumes à des chemins spécifiques dans les conteneurs individuels d’un groupe.

## <a name="common-scenarios"></a>Scénarios courants

Avec les groupes de plusieurs conteneurs, vous pouvez répartir une seule tâche fonctionnelle sur un petit nombre d’images conteneur. Ces images peuvent être fournies par différentes équipes et présenter des exigences spécifiques aux ressources.

Exemples d’utilisation :

* Un conteneur d’applications et un conteneur de journalisation. Le conteneur de journalisation collecte les journaux et les métriques générés par l’application principale, puis les écrit dans le stockage à long terme.
* Une application et un conteneur de surveillance. Le conteneur de surveillance émet régulièrement une demande à destination de l’application pour vérifier qu’elle s’exécute et répond correctement, et déclenche une alerte si ce n’est pas le cas.
* Un conteneur servant une application web et un conteneur extrayant le contenu le plus récent du contrôle de code source.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer un groupe de conteneurs](container-instances-multi-container-group.md) avec un modèle Azure Resource Manager.

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/
