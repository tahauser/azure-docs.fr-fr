---
title: "Créer un équilibrage de charge interne à l’aide de la CLI Azure | Microsoft Docs"
description: "Découvrez comment créer un équilibrage de charge interne dans Resource Manager à l’aide de l’interface de ligne de commande Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
ms.assetid: c7a24e92-b4da-43c0-90f2-841c1b7ce489
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 920ddecbf81296fd83606f2908e432f5327d4b7e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Créer un équilibreur de charge interne à l’aide de l’interface de ligne de commande Azure

> [!div class="op_single_selector"]
> * [Portail Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-arm-ps.md)
> * [Interface de ligne de commande Azure](../load-balancer/load-balancer-get-started-ilb-arm-cli.md)
> * [Modèle](../load-balancer/load-balancer-get-started-ilb-arm-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Déployer la solution à l’aide de l’interface de ligne de commande Azure

Les étapes suivantes expliquent comment créer un équilibrage de charge accessible sur Internet à l’aide d’Azure Resource Manager avec l’interface de ligne de commande. Avec Azure Resource Manager, toutes les ressources sont créées et configurées individuellement, puis rassemblées pour en créer une unique.

Vous devez créer et configurer les objets suivants pour déployer un équilibreur de charge :

* **Configuration d’adresses IP frontales**: contient les adresses IP publiques pour le trafic réseau entrant.
* **Pool d’adresses principales**: contient des cartes réseau (NIC) pour que les machines virtuelles puissent recevoir le trafic réseau de l’équilibrage de charge
* **Règles d’équilibrage de charge**: contient des règles qui mappent un port public situé sur l’équilibrage de charge à un port du pool d’adresses principales.
* **Règles NAT entrantes**: contient des règles qui mappent un port public situé sur l’équilibrage de charge vers le port d’une machine virtuelle spécifique située dans le pool d’adresses principales.
* **Sondes**: contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machines virtuelles du pool d’adresses principales.

Pour plus d’informations, consultez [Prise en charge d’un équilibreur de charge par Azure Resource Manager](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurer l’interface de ligne de commande pour utiliser le gestionnaire de ressources

1. Si vous n’avez jamais utilisé l’interface de ligne de commande Azure, consultez la section [Installer l’interface de ligne de commande Microsoft Azure](../cli-install-nodejs.md). Suivez les instructions jusqu’à l’étape de sélection du compte et de l’abonnement Azure.
2. Exécutez la commande **azure config mode** afin de passer au mode Resource Manager, comme suit :

    ```azurecli
    azure config mode arm
    ```

    Sortie attendue :

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Créer un équilibrage de charge interne, étape par étape

1. Connectez-vous à Azure.

    ```azurecli
    azure login
    ```

    À l’invite, entrez vos informations d’identification Azure.

2. Remplacez les outils de commande par le mode Azure Resource Manager.

    ```azurecli
    azure config mode arm
    ```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Toutes les ressources d’Azure Resource Manager sont associées à un groupe de ressources. Le cas échéant, créez un groupe de ressources.

```azurecli
azure group create <resource group name> <location>
```

## <a name="create-an-internal-load-balancer-set"></a>Créer un jeu d’équilibrage de charge interne

1. Créer un équilibrage de charge interne

    Dans le scénario suivant, un groupe de ressources nommé nrprg est créé dans la région Est des États-Unis.

    ```azurecli
    azure network lb create --name nrprg --location eastus
    ```

   > [!NOTE]
   > Toutes les ressources d’un équilibrage de charge interne, telles que les réseaux virtuels et les sous-réseaux du réseau virtuel doivent figurer dans le même groupe de ressources et dans la même région.

2. Créez une adresse IP frontale pour l’équilibrage de charge interne.

    L’adresse IP utilisée doit se trouver dans la plage de sous-réseau de votre réseau virtuel.

    ```azurecli
    azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet
    ```

3. Créer le pool d’adresses principal.

    ```azurecli
    azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb
    ```

    Après avoir défini une adresse IP frontale et un pool d’adresses principales, vous pouvez créer des règles d’équilibrage de charge, des règles NAT entrantes et des sondes d’intégrité personnalisées.

4. Créez une règle d’équilibrage de charge pour l’équilibrage de charge interne.

    Suivant le scénario ci-dessus, la commande crée une règle d’équilibrage de charge écoutant sur le port 1433 du pool frontal et envoyant le trafic de réseau à charge équilibrée au pool d’adresses principales en utilisant également le port 1433.

    ```azurecli
    azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb
    ```

5. Créez des règles NAT entrantes.

    Les règles NAT entrantes sont utilisées pour créer des points de terminaison dans un équilibrage de charge qui pointera vers une instance d’ordinateur virtuel spécifique. Les étapes précédentes ont permis de créer deux règles NAT pour le Bureau à distance.

    ```azurecli
    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

    azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389
    ```

6. Créez des sondes d'intégrité pour l'équilibreur de charge.

    Une sonde d’intégrité vérifie toutes les instances de machine virtuelle pour s’assurer qu’elles peuvent transmettre le trafic réseau. L’instance de machine virtuelle présentant des contrôles de sonde défaillants est supprimée de l’équilibrage de charge jusqu’à ce qu’elle revienne en ligne et que la sonde valide son intégrité.

    ```azurecli
    azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4
    ```

    > [!NOTE]
    > La plateforme Microsoft Azure utilise une adresse IPv4 statique routable publiquement pour divers scénarios d’administration. L’adresse IP est 168.63.129.16. Cette adresse IP ne doit pas être bloquée par les pare-feu, car cela peut entraîner un comportement inattendu.
    > En ce qui concerne l’équilibrage de charge interne Azure, cette adresse IP est utilisée par les sondes de l’équilibreur de charge, pour déterminer l’état de santé pour les machines virtuelles dans un jeu d’équilibrage de charge. Si un groupe de sécurité réseau est utilisé pour limiter le trafic vers les machines virtuelles Azure dans un jeu d’équilibrage de charge interne, ou est appliqué à un sous-réseau de réseau virtuel, vérifiez qu’une règle de sécurité de réseau n’est ajoutée pour autoriser le trafic à partir de 168.63.129.16.

## <a name="create-nics"></a>Créer des cartes réseau

Vous devez créer des cartes réseau (ou modifier des cartes existantes) et les associer à des règles NAT, des règles d’équilibreur de charge et des sondes.

1. Créez une carte réseau nommée *lb-nic1-be*, puis associez-la à la règle NAT *rdp1* et au pool d’adresses principales *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus
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

2. Créez une carte réseau nommée *lb-nic2-be*, puis associez-la à la règle NAT *rdp2* et au pool d’adresses principales *beilb*.

    ```azurecli
    azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus
    ```

3. Créez une machine virtuelle nommée *DB1*, puis associez-la à la carte réseau nommée *lb-nic1-be*. Un compte de stockage appelé *web1nrp* est créé avant d’exécuter la commande ci-dessous :

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```
    > [!IMPORTANT]
    > Les machines virtuelles d’un équilibreur de charge doivent se trouver dans le même groupe à haute disponibilité. Utilisez `azure availset create` pour créer un groupe à haute disponibilité.

4. Créez une machine virtuelle nommée *DB2*, puis associez-la à la carte réseau nommée *lb-nic2-be*. Un compte de stockage appelé *web1nrp* a été créé avant d’exécuter la commande ci-dessous.

    ```azurecli
    azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825
    ```

## <a name="delete-a-load-balancer"></a>Suppression d’un équilibreur de charge

Pour supprimer un équilibrage de charge, utilisez la commande suivante :

```azurecli
azure network lb delete --resource-group nrprg --name ilbset
```

## <a name="next-steps"></a>Étapes suivantes

[Configurer le mode de distribution d’équilibrage de charge à l’aide de l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

