---
title: "Créer un équilibreur de charge public - Azure CLI | Microsoft Docs"
description: "Apprendre à créer un équilibreur de charge public à l’aide de Azure CLI"
services: load-balancer
documentationcenter: na
author: KumudD
manager: jennoc
editor: 
tags: azure-resource-manager
ms.assetid: a8bcdd88-f94c-4537-8143-c710eaa86818
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: bd8c2703a1b43834e1c82e0776e2dee807bb3192
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2018
---
# <a name="creating-a-public-load-balancer-using-the-azure-cli"></a>Créer un équilibreur de charge public à l’aide de Azure CLI

> [!div class="op_single_selector"]
> * [Portail](../load-balancer/load-balancer-get-started-internet-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-arm-ps.md)
> * [interface de ligne de commande Azure](../load-balancer/load-balancer-get-started-internet-arm-cli.md)
> * [Modèle](../load-balancer/load-balancer-get-started-internet-arm-template.md)


[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [découvrir comment créer un équilibreur de charge public à l’aide du déploiement classique](load-balancer-get-started-internet-classic-portal.md)

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Déploiement de la solution à l’aide de l’interface de ligne de commande (CLI) Azure

Les étapes suivantes expliquent comment créer un équilibreur de charge public à l’aide d’Azure Resource Manager avec CLI. Avec Azure Resource Manager, chaque ressource est créée et configurée individuellement, puis rassemblées pour créer une ressource.

Vous devez créer et configurer les objets suivants pour déployer un équilibrage de charge :

* Configuration d’adresses IP frontales : contient les adresses IP publiques pour le trafic réseau entrant.
* Pool d’adresses principales : contient des interfaces réseau (NIC) pour que les machines virtuelles puissent recevoir le trafic réseau de l’équilibrage de charge.
* Règles d’équilibrage de charge : contient des règles de mappage d’un port public situé sur l’équilibrage de charge pour le pool d’adresses principales.
* Règles NAT entrantes : contient des règles de mappage d’un port public situé sur l’équilibrage de charge vers le port d’une machine virtuelle spécifique située dans le pool d’adresses principales.
* Sondes : contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machines virtuelles du pool d’adresses principales.

Pour plus d’informations, consultez [Prise en charge d’un équilibrage de charge par Azure Resource Manager](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurer l’interface de ligne de commande pour utiliser le gestionnaire de ressources

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md) et suivez les instructions jusqu’à l’étape où vous sélectionnez votre compte et votre abonnement Azure.
2. Exécutez la commande **azure config mode** pour passer en mode Resource Manager, comme illustré ci-dessous.

    ```azurecli
        azure config mode arm
    ```

    Sortie attendue :

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Créez un réseau virtuel et une adresse IP publique pour le pool d’adresses IP frontales

1. Créez un réseau virtuel (VNet) nommé *NRPVnet* dans l’emplacement Est des États-Unis à l’aide d’un groupe de ressources nommé *NRPRG*.

    ```azurecli
        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16
    ```

    Créez un sous-réseau nommé *NRPVnetSubnet* avec un bloc CIDR 10.0.0.0/24 dans *NRPVnet*.

    ```azurecli
        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24
    ```

2. Créez une adresse IP publique nommée *NRPPublicIP* pour qu’elle soit utilisée par un pool d’adresses IP frontales avec le nom DNS *loadbalancernrp.westus.cloudapp.azure.com*. La commande ci-dessous utilise le type d’allocation statique et un délai d’inactivité de 4 minutes.

    ```azurecli
        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4
    ```

   > [!IMPORTANT]
   > L’équilibreur de charge utilise l’étiquette du domaine de l’adresse IP publique en tant que nom de domaine complet (FQDN). Cet usage diffère d’un déploiement classique qui utilise le service cloud en tant que nom de domaine complet de l’équilibrage de charge.
   > Dans cet exemple, le nom de domaine complet est *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Créer un équilibrage de charge

La commande suivante crée un équilibrage de charge nommé *NRPlb* dans le groupe de ressources *NRPRG*, dans la zone Azure *Est des États-Unis*.

    ```azurecli
    azure network lb create NRPRG NRPlb eastus
    ```

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Création d’un pool d’adresses IP frontales et d’un pool d’adresses principales
Cet exemple démontre comment créer le pool d’adresses IP frontales qui reçoit le trafic réseau entrant pour l’équilibrage de charge et le pool d’adresses IP principales où le pool frontal envoie le trafic de réseau équilibré.

1. Créez un pool d’IP frontal associant l’IP publique créée lors de l’étape précédente et l’équilibrage de charge.

    ```azurecli
        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip
    ```

2. Configurez un pool d’adresses principal utilisé pour recevoir le trafic entrant à partir du pool d’adresses IP frontal.

    ```azurecli
        azure network lb address-pool create NRPRG NRPlb NRPbackendpool
    ```

## <a name="create-lb-rules-nat-rules-and-probe"></a>Créer des règles LB, des règles NAT et de sonde

Cet exemple crée les éléments suivants.

* une règle NAT pour transférer l’ensemble du trafic sur le port 21 vers le port 22<sup>1</sup> ;
* une règle NAT transférer l’ensemble du trafic sur le port 23 vers le port 22 ;
* une règle d’équilibrage de charge pour équilibrer tout le trafic entrant sur le port 80 vers le port 80 des adresses du pool principal ;
* une règle de sonde qui vérifie l'intégrité dans une page nommée *HealthProbe.aspx*.

<sup>1</sup> Les règles NAT sont associées à une instance de machine virtuelle spécifique derrière l’équilibreur de charge. Le trafic réseau arrivant sur le port 21 est envoyé à une machine virtuelle spécifique sur le port 22 associée à cette règle NAT. Vous devez spécifier un protocole (TCP ou UDP) pour une règle NAT. Il est impossible d'attribuer les deux protocoles au même port.

1. Créez les règles NAT.

    ```azurecli
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22
    ```

2. Créez une règle d’équilibreur de charge.

    ```azurecli
        azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name NRPfrontendpool --backend-address-pool-name NRPbackendpool
    ```

3. Créer une sonde d’intégrité.

    ```azurecli
        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4
    ```

4. Enregistrer vos paramètres.

    ```azurecli
        azure network lb show nrprg nrplb
    ```

    Sortie attendue :

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Créer des cartes réseau

Vous devez créer des cartes réseau (ou modifier des cartes existantes) et les associer à des règles NAT, des règles d’équilibreur de charge et des sondes.

1. Créez une carte réseau nommée *lb-nic1-be*, puis associez-la à la règle NAT *rdp1* et au premier (et unique) pool d’adresses principales *NRPbackendpool*.

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus
    ```

    Sortie attendue :

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Créez une carte réseau nommée *lb-nic2-be*, puis associez-la à la règle NAT *rdp2* et au premier (et unique) pool d’adresses principales *NRPbackendpool*.

    ```azurecli
        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus
    ```

3. Créez une machine virtuelle nommée *web1*, puis associez-la à la carte réseau nommée *lb-nic1-be*. Un compte de stockage appelé *web1nrp* a été créé avant d’exécuter la commande ci-dessous.

    ```azurecli
        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

    > [!IMPORTANT]
    > Les machines virtuelles d’un équilibreur de charge doivent se trouver dans le même groupe à haute disponibilité. Utilisez `azure availset create` pour créer un groupe à haute disponibilité.

    Le résultat doit ressembler à ce qui suit :

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    > [!NOTE]
    > Le message vous informant qu’ **il s’agit d’une carte réseau sans publicIP configuré** est un comportement attendu, car la carte réseau créée pour l’équilibreur de charge se connecte à Internet à l’aide de l’adresse IP publique de l’équilibreur de charge.

    Si la carte réseau *lb-nic1-be* est associée à la règle NAT *rdp1*, vous pouvez vous connecter à *web1* en utilisant le protocole RDP via le port 3441 de l’équilibreur de charge.

4. Créez une machine virtuelle nommée *web2*, puis associez-la à la carte réseau nommée *lb-nic2-be*. Un compte de stockage appelé *web1nrp* a été créé avant d’exécuter la commande ci-dessous.

    ```azurecli
        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="update-an-existing-load-balancer"></a>Mettre à jour un équilibreur de charge existant
Vous pouvez ajouter des règles faisant référence à l’équilibrage de charge existant. Dans l’exemple suivant, une nouvelle règle d’équilibrage de charge est ajoutée à un équilibrage de charge existant **NRPlb**

```azurecli
azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool
```

## <a name="delete-a-load-balancer"></a>Suppression d’un équilibreur de charge
Pour supprimer un équilibrage de charge, utilisez la commande suivante :

```azurecli
azure network lb delete --resource-group nrprg --name nrplb
```

## <a name="next-steps"></a>Étapes suivantes
[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-cli.md)

[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
