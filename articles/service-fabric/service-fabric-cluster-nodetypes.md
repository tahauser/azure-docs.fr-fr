---
title: Types de nœuds Azure Service Fabric et groupes de machines virtuelles identiques | Microsoft Docs
description: Découvrez la relation entre les types de nœuds Azure Service Fabric et les groupes de machines virtuelles identiques, ainsi que la méthode permettant de se connecter à distance à une instance de groupe identique ou à un nœud de cluster.
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/23/2018
ms.author: chackdan
ms.openlocfilehash: ce3b3e6f3c7ee0c7198c27ed0ca0e3610d26f143
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Types de nœuds Azure Service Fabric et groupes de machines virtuelles identiques
Les [groupes de machines virtuelles identiques](/azure/virtual-machine-scale-sets) représentent une ressource de calcul Azure. Vous pouvez utiliser des groupes identiques pour déployer et gérer une collection de machines virtuelles comme un groupe. Chaque type de nœud que vous définissez dans un cluster Azure Service Fabric configure une mise à l’échelle différente.  Le runtime Service Fabric installé sur chaque machine virtuelle du groupe identique. Vous pouvez faire monter ou descendre en puissance chaque type de nœud de manière indépendante, modifier la référence SKU du système d’exploitation s’exécutant sur chaque nœud de cluster, avoir différents ensembles de ports ouverts et utiliser différentes métriques de capacité.

La figure suivante illustre un cluster constitué de deux types de nœuds nommés FrontEnd et BackEnd. Chaque type de nœud compte cinq nœuds.

![Cluster constitué de deux types de nœuds][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapper des instances de groupe de machines virtuelles identiques à des nœuds
Comme l’illustre la figure précédente, les instances de groupe identique commencent à l’instance 0 et augmentent de 1. Les noms de nœuds reflètent la numérotation. Par exemple, le nœud BackEnd_0 représente l’instance 0 du groupe identique BackEnd. Ce groupe identique spécifique compte cinq instances nommées BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 et BackEnd_4.

Quand vous faites monter en puissance un groupe identique, une nouvelle instance est créée. En règle générale, le nom de la nouvelle instance du groupe identique est le nom du groupe identique suivi du numéro d’instance suivant. Dans notre exemple, il s’agit de BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapper des équilibreurs de charge de groupe identique à des types de nœuds et des groupes identiques
Si vous avez déployé votre cluster dans le portail Azure ou si vous avez utilisé l’exemple de modèle Azure Resource Manager, toutes les ressources d’un groupe de ressources sont répertoriées. Vous pouvez voir les équilibreurs de charge de chaque groupe identique ou type de nœud. Le nom d’un équilibreur de charge utilise le format suivant : **LB-&lt;nom du type de nœud&gt;**. La figure suivante en propose un exemple (LB-sfcluster4doc-0) :

![Ressources][Resources]


## <a name="next-steps"></a>Étapes suivantes
* Consultez [Vue d’ensemble de la fonction « Déployer n’importe où » et comparaison avec les clusters gérés par Azure](service-fabric-deploy-anywhere.md).
* Découvrez plus en détail la [sécurité des clusters](service-fabric-cluster-security.md).
* [Se connecter à distance](service-fabric-cluster-remote-connect-to-azure-cluster-node.md) à une instance de groupe identique spécifique
* [Mettre à jour les valeurs de plages de port RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) sur des machines virtuelles de cluster après le déploiement
* [Modifier le nom d’utilisateur et le mot de passe administrateur](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pour les machines virtuelles de cluster

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
