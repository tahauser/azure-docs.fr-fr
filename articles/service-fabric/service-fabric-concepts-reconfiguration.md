---
title: Reconfiguration dans Azure Service Fabric | Microsoft Docs
description: Comprendre la reconfiguration des partitions dans Service Fabric
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: 
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: 8371c4b268e1181e61542261ad7fc5fd04f6e59c
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2018
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Reconfiguration dans Azure Service Fabric
Une *configuration* se définit comme étant les réplicas et leurs rôles pour une partition d’un service avec état.

Une *reconfiguration* est le processus de passage d’une configuration à une autre configuration. Cela revient à opérer un changement dans le jeu de réplicas pour une partition d’un service avec état. L’ancienne configuration est appelée la *configuration précédente*, et la nouvelle configuration la *configuration actuelle*. Le protocole de reconfiguration dans Azure Service Fabric préserve la cohérence et la disponibilité quand un changement est apporté au jeu de réplicas.

Failover Manager lance les reconfigurations en réponse à différents événements survenus dans le système. Par exemple, si le réplica principal échoue, une reconfiguration est lancée pour promouvoir un réplica secondaire actif à la place du réplica principal. Ce peut être aussi une réponse à des mises à niveau d’application, quand le déplacement du réplica principal s’avère nécessaire sur un autre nœud en vue de la mise à niveau du nœud.

## <a name="reconfiguration-types"></a>Types de reconfiguration
Les reconfigurations peuvent être classées selon deux types :

- Reconfigurations où le réplica principal est changé :
    - **Basculement**: les basculements sont des reconfigurations faisant suite à la défaillance d’un principal en cours d’exécution.
    - **ÉchangePrincipal** : les échanges sont des reconfigurations où Service Fabric doit déplacer un réplica principal en cours d’exécution d’un nœud à un autre, généralement en réponse à un équilibrage de charge ou à une mise à niveau.

- Reconfigurations où le réplica principal ne change pas :

## <a name="reconfiguration-phases"></a>Phases de reconfiguration
Une reconfiguration se décompose en plusieurs phases :

- **Phase0** : cette phase se produit dans les reconfigurations d’échange de principal, où le réplica principal actuel transfère son état au nouveau réplica principal avant de passer réplica secondaire actif.

- **Phase1** : cette phase se produit pendant les reconfigurations où le réplica principal est changé. Au cours de cette phase, Service Fabric identifie le principal approprié parmi les réplicas actuels. Cette phase est inutile pendant les reconfigurations d’échange de principal, car le nouveau réplica principal a déjà été choisi. 

- **Phase2** : au cours de cette phase, Service Fabric permet de s’assurer que toutes les données sont disponibles dans la majorité des réplicas de la configuration actuelle.

Il existe plusieurs autres phases qui sont à usage interne uniquement.

## <a name="stuck-reconfigurations"></a>Reconfigurations bloquées
Les reconfigurations peuvent se *bloquer* pour diverses raisons. Voici quelques-unes des raisons les plus courantes :

- **Réplicas arrêtés** : certaines phases de reconfiguration nécessitent que la majorité des réplicas au sein de la configuration soient opérationnels.
- **Problèmes de réseau ou de communication** : les reconfigurations exigent une connectivité réseau entre les différents nœuds.
- **Échecs d’API** : le protocole de reconfiguration a besoin que des implémentations de service terminent certaines API. Par exemple, le fait de ne pas respecter le jeton d’annulation dans un service fiable entraîne le blocage des reconfigurations ÉchangePrincipal.

Utilisez les rapports d’intégrité à partir des composants système, tels que System.FM, System.RA et System.RAP, pour diagnostiquer où le blocage d’une reconfiguration a eu lieu. La [page sur les rapports d’intégrité du système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) décrit ces rapports.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les concepts propres à Service Fabric, consultez les articles suivants :

- [Cycle de vie des Reliable Services - C#](service-fabric-reliable-services-lifecycle.md)
- [Rapports d’intégrité du système](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Réplicas et instances](service-fabric-concepts-replica-lifecycle.md)
