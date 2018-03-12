---
title: "Vue d’ensemble d’Azure Container Instances"
description: Comprendre Azure Container Instances
services: container-instances
author: seanmck
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 01/02/2018
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 01e539856adbdcf02dc4e49087a3ab71b328db5a
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="azure-container-instances"></a>Azure Container Instances

Les conteneurs deviennent rapidement le meilleur moyen pour mettre en package, déployer et gérer des applications cloud. Azure Container Instances propose la façon la plus simple et rapide d’exécuter un conteneur dans Azure, sans avoir à configurer des machines virtuelles ni adopter un service de niveau supérieur.

Azure Container Instances est une excellente solution pour les scénarios qui peuvent fonctionner dans des conteneurs isolés, y compris les applications basiques, automatiser des tâches et créer des travaux. Pour les scénarios où vous devez disposer d’orchestration de conteneur complète (détection de service dans plusieurs conteneurs, mise à l’échelle automatique et mises à niveau d’application coordonnées), nous vous recommandons le service [Azure Container Service (AKS)](../aks/index.yml).

## <a name="fast-startup-times"></a>Temps de démarrage rapide

Le service Containers offre des avantages de démarrage conséquents sur les machines virtuelles. Avec Azure Container Instances, vous pouvez démarrer un conteneur dans Azure en quelques secondes, sans avoir à configurer ni gérer des machines virtuelles.

## <a name="hypervisor-level-security"></a>Sécurité au niveau de l’hyperviseur

D’un point de vue historique, les conteneurs ont offert l’isolation de dépendance d’application et la gouvernance des ressources, mais n’ont pas été considérés suffisamment renforcés pour une utilisation de plusieurs locataires hostile. Avec Azure Container Instances, votre application se retrouve aussi isolée dans un conteneur que dans une machine virtuelle.

## <a name="custom-sizes"></a>Tailles personnalisées

Les conteneurs sont généralement optimisés pour n’exécuter qu’une application, mais les besoins précis de ces applications peuvent grandement varier. Avec Azure Container Instances, vous pouvez demander ce dont vous avez besoin en termes de mémoire et de noyaux processeur. Vous payez pour ce que vous demandez et vous êtes facturé immédiatement, pour pouvoir optimiser avec précision vos dépenses selon vos besoins.

## <a name="public-ip-connectivity"></a>Connectivité IP publique

Avec Azure Container Instances, vous pouvez exposer vos conteneurs sur internet avec une adresse IP publique. Prochainement, nous comptons étendre nos fonctionnalités réseau afin d’inclure l’intégration avec les réseaux virtuels, des équilibreurs de charges et d’autres parties essentielles de l’infrastructure de mise en réseau Azure.

## <a name="persistent-storage"></a>Stockage persistant

Pour récupérer et conserver des états avec Azure Container Instances, nous proposons le [montage direct des partages de fichiers Azure](container-instances-mounting-azure-files-volume.md).

## <a name="linux-and-windows-containers"></a>Conteneurs Windows et Linux

Avec Azure Container Instances, vous pouvez programmer des conteneurs Windows et Linux avec la même API. Spécifiez simplement le type de système d’exploitation lorsque vous créez vos [groupes de conteneurs](container-instances-container-groups.md).

Certaines fonctionnalités sont actuellement restreintes aux conteneurs Linux. Nous travaillons actuellement à proposer toutes ces fonctionnalités dans des conteneurs Windows. En attendant, nous vous invitons à découvrir les différences actuelles de la plateforme dans [Disponibilité des régions et quotas pour Azure Container Instances](container-instances-quotas.md).

## <a name="co-scheduled-groups"></a>Groupes co-planifiés

Azure Container Instances prend en charge la planification de [groupes de plusieurs conteneurs](container-instances-container-groups.md) qui partagent un même hôte, réseau local, stockage et cycle de vie. Vous pouvez ainsi combiner votre application principale avec d’autres ayant un rôle d’assistance, tel que la journalisation.

## <a name="next-steps"></a>Étapes suivantes

Essayez de déployer un conteneur dans Azure avec une seule commande à l’aide de notre [guide de démarrage rapide](container-instances-quickstart.md).
