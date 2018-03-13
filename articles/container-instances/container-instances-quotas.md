---
title: "Disponibilité des régions et quotas pour Azure Container Instances"
description: "La disponibilité des régions et les quotas par défaut du service Azure Container Instances."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 02/27/2018
ms.author: marsma
ms.openlocfilehash: 008b07402cb0b937174488982cac64811baa5d5d
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Disponibilité des régions et quotas pour Azure Container Instances

Tous les services Azure incluent certains quotas et limites par défaut pour les ressources et fonctionnalités. Les sections suivantes détaillent les limites de ressources par défaut pour plusieurs ressources Azure Container Instances (ACI), ainsi que la disponibilité du service ACI dans les régions Azure.

## <a name="service-quotas-and-limits"></a>Quotas et limites du service

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Disponibilité des régions

Azure Container Instances est disponible dans les régions suivantes avec les limites de processeur et de mémoire spécifiées.

| Lieu | SE | UC | Mémoire (Go) |
| -------- | -- | :---: | :-----------: |
| Europe de l’Ouest, Ouest des États-Unis, Est des États-Unis | Linux | 4 | 14 |
| Asie du Sud-Est  | Linux | 1 | 1.5 |
| Europe de l’Ouest, Ouest des États-Unis, Est des États-Unis | Windows | 4 | 14 |
| Asie du Sud-Est  | Windows | 2 | 3,5 |

Les instances de conteneur créées dans les limites de ces ressources sont soumises à la disponibilité dans la région de déploiement. Quand une région a une charge importante, vous pouvez rencontrer un échec durant le déploiement des instances. Pour atténuer ce type d’échec de déploiement, essayez de déployer des instances avec des paramètres de mémoire et de processeur inférieurs, ou essayez d’effectuer le déploiement plus tard.

Informez l’équipe des régions supplémentaires nécessaires ou des limites de mémoire/processeur augmentées à l’adresse [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Pour plus d’informations sur la résolution des problèmes de déploiement d’instances de conteneur, consultez [Résoudre les problèmes de déploiement avec Azure Container Instances](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Étapes suivantes

Certains quotas et limites par défaut peuvent être augmentés. Pour demander une augmentation d’une ou de plusieurs ressources qui peuvent prendre en charge cette augmentation, veuillez envoyer une [demande de support Azure][azure-support] (sélectionnez « Quota » pour **Type de problème**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
