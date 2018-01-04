---
title: "Disponibilité des ressources et des régions pour Azure Container Instances"
description: "Découvrez les régions Azure qui prennent en charge le déploiement d’instances de conteneur et les limites de processeur et de mémoire pour ces instances."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: overview
ms.date: 12/15/2017
ms.author: marsma
ms.openlocfilehash: ec7f469c47924f4ae22d6509996ca9cf498fc9ad
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2017
---
# <a name="region-availability-for-azure-container-instances"></a>Disponibilité des régions pour Azure Container Instances

En préversion, Azure Container Instances est disponible dans les régions suivantes avec les limites de processeur et de mémoire spécifiées.

| Lieu | SE | UC | Mémoire (Go) |
| -------- | -- | :---: | :-----------: |
| Europe de l’Ouest, Ouest des États-Unis, Est des États-Unis | Linux | 4 | 14 |
| Europe de l’Ouest, Ouest des États-Unis, Est des États-Unis | Windows | 4 | 14 |

## <a name="resource-availability"></a>Disponibilité des ressources

Les instances de conteneur créées dans les limites de ces ressources sont soumises à la disponibilité dans la région de déploiement. Quand une région a une charge importante, vous pouvez rencontrer un échec durant le déploiement des instances.

Pour atténuer ce type d’échec de déploiement, essayez de déployer des instances avec des paramètres de mémoire et de processeur inférieurs, ou essayez d’effectuer le déploiement plus tard.

## <a name="next-steps"></a>étapes suivantes

Pour plus d’informations sur la résolution des problèmes de déploiement d’instances de conteneur, consultez [Résoudre les problèmes de déploiement avec Azure Container Instances](container-instances-troubleshooting.md).