---
title: "Contrôler le routage dans un réseau virtuel Azure - CLI - Classique | Microsoft Docs"
description: "Apprenez à contrôler le routage dans des réseaux VNet à l'aide de l'Interface de ligne de commande Azure dans le modèle de déploiement classique"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 6bfdfcdde39d64d662bb6d2ea55fadca24d49091
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Contrôle du routage et utilisation des appliances virtuelles (classiques) à l'aide de l'Interface de ligne de commande Azure

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [interface de ligne de commande Azure](tutorial-create-route-table-cli.md)
> * [Modèle](virtual-network-create-udr-arm-template.md)
> * [PowerShell (classique)](virtual-network-create-udr-classic-ps.md)
> * [Interface de ligne de commande (classique)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement classique. Vous pouvez également [contrôler le routage et utiliser des appliances virtuelles dans le modèle de déploiement Resource Manager](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes d’interface de ligne de commande PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessus. Si vous souhaitez exécuter les commandes telles qu'elles sont présentées dans ce document, créez l'environnement décrit dans [Créer un VNet (classique) à l'aide de l'Interface de ligne de commande Azure](virtual-networks-create-vnet-classic-cli.md).

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Création des itinéraires définis par l'utilisateur pour le sous-réseau frontal
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau frontal selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Exécutez la commande suivante pour basculer en mode Classic :

    ```azurecli
    azure config mode asm
    ```

    Output:

        info:    New mode is asm

2. Exécutez la commande suivante pour créer une table de routage pour le sous-réseau frontal :

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Output:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Paramètres :
   
   * **-l (ou --location)**. Région Azure où le groupe de sécurité réseau sera créé. Pour notre scénario, *westus*.
   * **-n (ou --name)**. Nom du nouveau groupe de sécurité réseau. Pour notre scénario, *NSG-FrontEnd*.
3. Exécutez la commande suivante pour créer un routage dans la table de routage pour envoyer tout le trafic destiné au sous-réseau Backend (192.168.2.0/24) à la machine virtuelle **FW1** (192.168.0.4) :

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Output:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Paramètres :
   
   * **-r (ou --route-table-name)**. Nom de la table de routage où l'itinéraire sera ajouté. Pour notre scénario, *UDR-FrontEnd*.
   * **-a (ou --address-prefix)**. Préfixe d'adresse pour le sous-réseau auquel les paquets sont destinés. Pour notre scénario, *192.168.2.0/24*.
   * **-t (ou --next-hop-type)**. Type d'objet vers lequel le trafic sera envoyé. Les valeurs possibles sont *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* ou *None*.
   * **-p (ou --next-hop-ip-address**). Adresse IP de saut suivant. Pour notre scénario, *192.168.0.4*.
4. Exécutez la commande suivante pour associer la table de routage créée au sous-réseau **FrontEnd** :

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Output:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Paramètres :
   
   * **-t (ou --vnet-name)**. Nom du réseau virtuel où le sous-réseau est situé. Pour notre scénario, *TestVNet*.
   * **-n (ou --subnet-name)**. Nom du sous-réseau auquel la table de routage sera ajoutée. Pour notre scénario, *FrontEnd*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Créer l’itinéraire défini par l’utilisateur pour le sous-réseau principal
Pour créer la table d’itinéraires et l’itinéraire nécessaires au sous-réseau principal, conformément au scénario, procédez comme suit :

1. Exécutez la commande suivante pour créer une table d’itinéraires pour le sous-réseau principal :

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Exécutez la commande suivante pour créer un itinéraire dans la table d’itinéraires pour envoyer tout le trafic destiné au sous-réseau frontal (192.168.1.0/24) à la machine virtuelle **FW1** (192.168.0.4) :

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Exécutez la commande suivante pour associer la table de routage au sous-réseau **Backend** :

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

