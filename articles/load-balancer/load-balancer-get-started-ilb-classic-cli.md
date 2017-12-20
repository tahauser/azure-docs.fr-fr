---
title: "Créer un équilibrage de charge interne à l’aide de la CLI Azure dans le modèle de déploiement classique | Microsoft Docs"
description: "Découvrez comment créer un équilibreur de charge interne à l'aide de l’interface de ligne de commande Azure (CLI) dans le modèle de déploiement classique"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: becbbbde-a118-4269-9444-d3153f00bf34
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: f740633230b2479f77d7d09a31dbbf3f72ffb174
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-the-azure-cli"></a>Créer un équilibreur de charge interne (classique) à l’aide de l’interface de ligne de commande (CLI) Azure

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [interface de ligne de commande Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Services cloud](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md).  Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](load-balancer-get-started-ilb-arm-cli.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>Pour créer un jeu d’équilibrage de charge interne pour les machines virtuelles

Pour créer un jeu d’équilibrage de charge interne et les serveurs qui y envoient leur trafic, vous devez procéder comme suit :

1. Créez une instance d’équilibrage de charge interne comme point de terminaison du trafic entrant qui doit être équilibré entre les serveurs d’un jeu d’équilibrage de charge.
2. Ajoutez les points de terminaison correspondants aux machines virtuelles qui reçoivent le trafic entrant.
3. Configurez les serveurs qui envoient le trafic à l’adresse IP virtuelle (VIP) de l’instance d’équilibrage de charge interne.

## <a name="step-by-step-creating-an-internal-load-balancer-using-cli"></a>Créer par étapes un équilibreur de charge interne à l’aide de l’interface de ligne de commande CLI

Ce guide indique comment créer un équilibreur de charge interne selon le scénario ci-dessus.

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Exécutez la commande **azure config mode** pour passer en mode classique, comme illustré ci-dessous.

    ```azurecli
    azure config mode asm
    ```

    Sortie attendue :

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>Création d'un point de terminaison et d'un jeu d'équilibrage de charge

Le scénario suppose que les machines virtuelles « DB1 » et « DB2 » figurent dans un service cloud appelé « mytestcloud ». Ces deux machines virtuelles utilisent un réseau virtuel appelé mon « testvnet » avec un sous-réseau « sous-réseau-1 ».

Ce guide crée un jeu d’équilibrage de charge en utilisant le port 1433 comme port privé et 1433 comme port local.

Il s'agit d'un scénario courant dans lequel vous utilisez des machines virtuelles SQL sur le serveur principal avec un équilibreur de charge interne afin de garantir que les serveurs de base de données ne soient pas exposés directement à l'aide d'une adresse IP publique.

### <a name="step-1"></a>Étape 1

Créer un jeu d’équilibrage de charge interne avec `azure network service internal-load-balancer add`.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

Pour plus d'informations, consultez `azure service internal-load-balancer --help` .

Vous pouvez vérifier les propriétés de l’équilibreur de charge interne à l'aide de la commande `azure service internal-load-balancer list` *nom du service cloud*.

Voici un exemple de sortie :

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>Étape 2

Vous configurez le jeu d'équilibrage de charge interne lorsque vous ajoutez le premier point de terminaison. Vous pouvez associer le point de terminaison, la machine virtuelle et le port de sonde au jeu d’équilibrage de charge interne au cours de cette étape.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>Étape 3 :

Vérifier la configuration de l’équilibreur de charge avec `azure vm show` *nom de la machine virtuelle*

```azurecli
azure vm show DB1
```

La sortie se présente comme suit :

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>Création d'un point de terminaison de Bureau à distance pour une machine virtuelle

Vous pouvez créer un point de terminaison de Bureau à distance pour transférer le trafic réseau à partir d’un port public vers un port local pour une machine virtuelle spécifique à l’aide d’ `azure vm endpoint create`.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>Suppression d’une machine virtuelle de l’équilibreur de charge

Vous pouvez supprimer une machine virtuelle d'un jeu d’équilibrage de charge interne en supprimant le point de terminaison associé. Une fois le point de terminaison supprimé, la machine virtuelle n'appartient plus au jeu d'équilibrage de charge.

À l’aide de l’exemple ci-dessus, vous pouvez supprimer le point de terminaison créé pour la machine virtuelle « DB1 » de l’équilibreur de charge interne « ilbset » à l’aide de la commande `azure vm endpoint delete`.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

Pour plus d'informations, consultez `azure vm endpoint --help` .

## <a name="next-steps"></a>Étapes suivantes

[Configurer le mode de distribution d’équilibreur de charge à l’aide de l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
