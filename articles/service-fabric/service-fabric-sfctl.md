---
title: "CLI Azure Service Fabric : sfctl | Microsoft Docs"
description: "Décrit les commandes sfctl de l’interface de ligne de commande (CLI) Service Fabric."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: 23adc7fa6ee53d1c5edd10b2772f8825dbcef2a8
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl"></a>sfctl 
Commandes permettant de gérer les clusters et entités Service Fabric. Cette version est compatible avec le runtime Service Fabric 6.0. Les commandes suivent le modèle nom-verbe. Pour plus d’informations, consultez les sous-groupes suivants.

## <a name="subgroups"></a>Sous-groupes
|Sous-groupe|DESCRIPTION|
| --- | --- |
| [application](service-fabric-sfctl-application.md)| Permet de créer, de supprimer et de gérer les applications et les types d’application.|
| [chaos](service-fabric-sfctl-chaos.md)   | Permet de démarrer, d’arrêter et de créer des rapports sur le service de test chaos.|
| [cluster](service-fabric-sfctl-cluster.md) | Permet de sélectionner, de gérer et d’utiliser les clusters Service Fabric.|
| [compose](service-fabric-sfctl-compose.md) | Permet de créer, de supprimer et de gérer les applications Docker Compose.|
| [is](service-fabric-sfctl-is.md)      | Interroge et envoie des commandes vers le service d’infrastructure.|
| [node](service-fabric-sfctl-node.md)    | Permet de gérer les nœuds qui forment un cluster.|
| [partition](service-fabric-sfctl-partition.md)  | Interroge et gère des partitions pour tout service.|
| [rpm](service-fabric-sfctl-rpm.md)        | Interroge et envoie des commandes vers le service gestionnaire de réparation.|
| [replica](service-fabric-sfctl-replica.md) | Permet de gérer les réplicas qui font partie des partitions de service.|
| [service](service-fabric-sfctl-service.md) | Permet de créer, de supprimer et de gérer le service, les types de service et les packages de services.|
| [store](service-fabric-sfctl-store.md)   | Effectue des opérations élémentaires au niveau des fichiers dans le magasin d’images de cluster.|

## <a name="next-steps"></a>étapes suivantes
- [Configurez](service-fabric-cli.md) l’interface de ligne de commande Service Fabric.
- Découvrez comment utiliser l’interface de ligne de commande (CLI) Service Fabric à l’aide d’[exemples de scripts](/azure/service-fabric/scripts/sfctl-upgrade-application).