---
title: Connexion distante à un nœud de cluster Azure Service Fabric | Microsoft Docs
description: Découvrez comment vous connecter à distance à une instance de groupe identique (un nœud de cluster Service Fabric).
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
ms.openlocfilehash: 8c7d5446429089a0fc931175b55e81e1ad0c97a0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Connexion distante à une instance de groupe de machines virtuelles identiques ou à un nœud de cluster
Dans un cluster Service Fabric s’exécutant dans Azure, chaque type de nœud de cluster que vous définissez [définit une échelle mise à l’échelle séparée des machines virtuelles](service-fabric-cluster-nodetypes.md).  Vous pouvez vous connecter à distance à des instances de groupes identiques (ou à des nœuds de cluster) spécifiques.  Contrairement aux machines virtuelles à une seule instance, les instances de groupe identique ne possèdent pas leurs propres adresses IP virtuelles. Cela peut poser des problèmes quand il s’agit de rechercher une adresse IP et un port permettant de se connecter à distance à une instance spécifique.

Pour rechercher une adresse IP et un port permettant de se connecter à distance à une instance spécifique, effectuez les étapes suivantes.

1. Recherchez l’adresse IP virtuelle du type de nœud en obtenant les règles NAT de trafic entrant pour le protocole RDP (Remote Desktop Protocol).

    Dans un premier temps, obtenez les valeurs des règles NAT de trafic entrant qui ont été définies pendant la définition des ressources de `Microsoft.Network/loadBalancers`.
    
    Dans le portail Azure, dans la page de l’équilibreur de charge, sélectionnez **Paramètres** > **Règles NAT de trafic entrant**. Cela vous donne l’adresse IP et le port qui vous permettent de vous connecter à distance à la première instance de groupe identique. 
    
    ![Équilibrage de charge][LBBlade]
    
    Dans la figure suivante, l’adresse IP et le port sont **104.42.106.156** et **3389**.
    
    ![Règles NAT][NATRules]

2. Recherchez le port vous permettant de vous connecter à distance à un nœud ou à une instance de groupe identique spécifique.

    Les instances de groupe identique se mappent aux nœuds. Utilisez les informations de groupe identique pour déterminer exactement quel port utiliser.
    
    Les ports sont alloués dans un ordre croissant qui correspond à l’instance de groupe identique. Pour l’exemple précédent du type de nœud FrontEnd, le tableau suivant répertorie les ports de chacune des cinq instances de nœud. Appliquez le même mappage à votre instance de groupe identique.
    
    | **Instance de groupe de machines virtuelles identiques** | **Port** |
    | --- | --- |
    | FrontEnd_0 |3389 |
    | FrontEnd_1 |3390 |
    | FrontEnd_2 |3391 |
    | FrontEnd_3 |3392 |
    | FrontEnd_4 |3393 |
    | FrontEnd_5 |3394 |

3. Connectez-vous à distance à l’instance de groupe identique spécifique.

    Dans la figure suivante, le service Connexion Bureau à distance est utilisé pour se connecter à l’instance de groupe identique FrontEnd_1 :
    
    ![Connexion Bureau à distance][RDP]


Pour les prochaines étapes, lisez les articles suivants :
* Consultez [Vue d’ensemble de la fonction « Déployer n’importe où » et comparaison avec les clusters gérés par Azure](service-fabric-deploy-anywhere.md).
* Découvrez plus en détail la [sécurité des clusters](service-fabric-cluster-security.md).
* [Mettre à jour les valeurs de plages de port RDP](./scripts/service-fabric-powershell-change-rdp-port-range.md) sur des machines virtuelles de cluster après le déploiement
* [Modifier le nom d’utilisateur et le mot de passe administrateur](./scripts/service-fabric-powershell-change-rdp-user-and-pw.md) pour les machines virtuelles de cluster

<!--Image references-->
[LBBlade]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/NATRules.png
[RDP]: ./media/service-fabric-cluster-remote-connect-to-azure-cluster-node/RDP.png
