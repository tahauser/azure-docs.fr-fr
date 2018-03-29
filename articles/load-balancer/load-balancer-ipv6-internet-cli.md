---
title: Créer un équilibreur de charge public avec IPv6 - Azure CLI | Microsoft Docs
description: Découvrez comment créer un équilibreur de charge public avec IPv6 dans Azure Resource Manager à l’aide d’Azure CLI.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
tags: azure-resource-manager
keywords: IPv6, équilibreur de charge azure, double pile, adresse ip publique, ipv6 natif, mobile, iot
ms.assetid: a1957c9c-9c1d-423e-9d5c-d71449bc1f37
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 62f22ccadfabd2f3d6906beb3c241703d4e6383f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2018
---
# <a name="create-a-public-load-balancer-with-ipv6-in-azure-resource-manager-by-using-azure-cli"></a>Créer un équilibreur de charge public avec IPv6 dans Azure Resource Manager à l’aide d’Azure CLI

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Interface de ligne de commande Azure](load-balancer-ipv6-internet-cli.md)
> * [Modèle](load-balancer-ipv6-internet-template.md)


Un équilibrage de charge Azure est de type Couche 4 (TCP, UDP). Les équilibreurs de charge offrent une disponibilité élevée en distribuant le trafic entrant parmi les instances de service saines dans les services cloud ou les machines virtuelles dans un jeu d’équilibreur de charge. Les équilibreurs de charge peuvent également présenter ces services sur plusieurs ports, plusieurs adresses IP ou les deux.

## <a name="example-deployment-scenario"></a>Exemple de scénario de déploiement

Le diagramme suivant illustre la solution d’équilibrage de charge déployée à l’aide de l’exemple de modèle décrit dans cet article.

![Scénario d’équilibreur de charge](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Dans ce scénario, vous créez les ressources Azure suivantes :

* Deux machines virtuelles
* Une interface de réseau virtuel pour chaque machine virtuelle avec des adresses IPv4 et IPv6 affectées
* Un équilibreur de charge public avec une adresse IP publique IPv4 et IPv6
* Un groupe à haute disponibilité contenant les deux machines virtuelles
* Deux règles d’équilibrage de charge pour mapper les adresses IP virtuelles publiques sur les points de terminaison privés

## <a name="deploy-the-solution-by-using-azure-cli"></a>Déployer la solution à l’aide d’Azure CLI

Les étapes suivantes expliquent comment créer un équilibreur de charge public à l’aide d’Azure Resource Manager avec Azure CLI. Avec Azure Resource Manager, vous créez et configurez chaque objet individuellement, puis vous les rassemblez pour créer une ressource.

Créez et configurez les objets suivants pour déployer un équilibreur de charge :

* **Configuration d’adresses IP frontales** : contient les adresses IP publiques pour le trafic réseau entrant.
* **Pool d’adresses principales** : contient des interfaces réseau pour que les machines virtuelles puissent recevoir le trafic réseau de l’équilibreur de charge.
* **Règles d’équilibrage de charge** : contient des règles qui mappent un port public situé sur l’équilibreur de charge à un port du pool d’adresses principales.
* **Règles NAT entrantes**: contient des règles de traduction d’adresses réseau (NAT) qui mappent un port public situé sur l’équilibreur de charge vers le port d’une machine virtuelle spécifique située dans le pool d’adresses principales.
* **Sondes** : contient les sondes d’intégrité utilisées pour vérifier la disponibilité des instances de machines virtuelles du pool d’adresses principales.

Pour plus d’informations, consultez [Prise en charge d’un équilibreur de charge par Azure Resource Manager](load-balancer-arm.md).

## <a name="set-up-your-azure-cli-environment-to-use-azure-resource-manager"></a>Configurer votre environnement Azure CLI pour utiliser Azure Resource Manager

Dans cet exemple, vous exécutez les outils Azure CLI dans une fenêtre de commande PowerShell. Pour améliorer la lisibilité et la réutilisation, vous utilisez les fonctionnalités de script de PowerShell et non les cmdlets Azure PowerShell.

1. Si vous n’avez jamais utilisé Azure CLI, consultez [Installer l’interface de ligne de commande Azure CLI 1.0](../cli-install-nodejs.md) et suivez les instructions jusqu’à l’étape vous invitant à sélectionner votre compte et votre abonnement Azure.

2. Pour basculer en mode Resource Manager, exécutez la commande **azure config mode** :

    ```azurecli
    azure config mode arm
    ```

    Sortie attendue :

        info:    New mode is arm

3. Connectez-vous à Azure et obtenez une liste de vos abonnements :

    ```azurecli
    azure login
    ```

4. À l’invite, entrez vos informations d’identification Azure :

    ```azurecli
    azure account list
    ```

5. Sélectionnez l’abonnement que vous souhaitez utiliser et notez l’ID d’abonnement à utiliser à l’étape suivante.

6. Configurez des variables PowerShell à utiliser avec les commandes Azure CLI :

    ```powershell
    $subscriptionid = "########-####-####-####-############"  # enter subscription id
    $location = "southcentralus"
    $rgName = "pscontosorg1southctrlus09152016"
    $vnetName = "contosoIPv4Vnet"
    $vnetPrefix = "10.0.0.0/16"
    $subnet1Name = "clicontosoIPv4Subnet1"
    $subnet1Prefix = "10.0.0.0/24"
    $subnet2Name = "clicontosoIPv4Subnet2"
    $subnet2Prefix = "10.0.1.0/24"
    $dnsLabel = "contoso09152016"
    $lbName = "myIPv4IPv6Lb"
    ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Créer un groupe de ressources, un équilibrage de charge, un réseau virtuel et des sous-réseaux

1. Créez un groupe de ressources :

    ```azurecli
    azure group create $rgName $location
    ```

2. Créez un équilibreur de charge :

    ```azurecli
    $lb = azure network lb create --resource-group $rgname --location $location --name $lbName
    ```

3. Créez un réseau virtuel :

    ```azurecli
    $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix
    ```

4. Dans ce réseau virtuel, créez deux sous-réseaux :

    ```azurecli
    $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
    $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName
    ```

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Créer des adresses IP publiques pour le pool frontal

1. Configurez les variables PowerShell :

    ```powershell
    $publicIpv4Name = "myIPv4Vip"
    $publicIpv6Name = "myIPv6Vip"
    ```

2. Créez une adresse IP publique pour le pool frontal :

    ```azurecli
    $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
    $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel
    ```

    > [!IMPORTANT]
    > L’équilibreur de charge utilise l’étiquette du domaine de l’adresse IP publique en tant que nom de domaine complet (FQDN). Cet usage diffère d’un déploiement classique qui utilise le service cloud en tant que nom de domaine complet de l’équilibrage de charge.
    >
    > Dans cet exemple, le nom de domaine complet est *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Créer ds pools frontaux et principaux

Dans cette section, vous créez les pools IP suivants :
* Le pool IP frontal qui reçoit le trafic réseau entrant sur l’équilibreur de charge.
* Le pool d’IP principal où le pool frontal envoie le trafic réseau dont la charge a été équilibrée.

1. Configurez les variables PowerShell :

    ```powershell
    $frontendV4Name = "FrontendVipIPv4"
    $frontendV6Name = "FrontendVipIPv6"
    $backendAddressPoolV4Name = "BackendPoolIPv4"
    $backendAddressPoolV6Name = "BackendPoolIPv6"
    ```

2. Créez un pool d’IP frontal et associez-le à l’IP publique créée lors de l’étape précédente et à l’équilibreur de charge.

    ```azurecli
    $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
    $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
    $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
    $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName
    ```

## <a name="create-the-probe-nat-rules-and-load-balancer-rules"></a>Créer la sonde, les règles NAT et les règles d’équilibreur de charge

Cet exemple crée les éléments suivants :

* Une règle de sonde dédiée à la détection de connectivité sur le port TCP 80.
* Une règle NAT pour transférer l’ensemble du trafic entrant sur le port 3389 vers le port 3389 pour RDP.\*
* Une règle NAT pour transférer l’ensemble du trafic entrant sur le port 3391 vers le port 3389 pour le protocole RDP (Remote Desktop Protocol).\*
* Une règle d’équilibreur de charge pour équilibrer tout le trafic entrant sur le port 80 vers le port 80 des adresses du pool principal.

\* Les règles NAT sont associées à une instance de machine virtuelle spécifique derrière l’équilibreur de charge. Le trafic réseau arrivant sur le port 3389 est envoyé à la machine virtuelle spécifique sur le port associé à la règle NAT. Vous devez spécifier un protocole (TCP ou UDP) pour une règle NAT. Vous ne pouvez pas attribuer ces deux protocoles au même port.

1. Configurez les variables PowerShell :

    ```powershell
    $probeV4V6Name = "ProbeForIPv4AndIPv6"
    $natRule1V4Name = "NatRule-For-Rdp-VM1"
    $natRule2V4Name = "NatRule-For-Rdp-VM2"
    $lbRule1V4Name = "LBRuleForIPv4-Port80"
    $lbRule1V6Name = "LBRuleForIPv6-Port80"
    ```

2. Créez la sonde.

    L’exemple suivant illustre la création d’une sonde TCP qui détecte la connectivité au port TCP principal 80 toutes les 15 secondes. Après deux échecs consécutifs, elle marque la ressource principale comme indisponible.

    ```azurecli
    $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName
    ```

3. Créez des règles NAT entrantes qui prennent en charge les connexions RDP aux ressources principales :

    ```azurecli
    $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
    $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName
    ```

4. Créez des règles d’équilibreur de charge qui transmettent le trafic sur différents ports principaux en fonction du port frontal de réception de la requête.

    ```azurecli
    $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
    $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName
    ```

5. Vérifiez vos paramètres :

    ```azurecli
    azure network lb show --resource-group $rgName --name $lbName
    ```

    Sortie attendue :

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show

## <a name="create-nics"></a>Créer des cartes réseau

Créez des cartes réseau et associez-les à des règles NAT, des règles d’équilibreur de charge et des sondes.

1. Configurez les variables PowerShell :

    ```powershell
    $nic1Name = "myIPv4IPv6Nic1"
    $nic2Name = "myIPv4IPv6Nic2"
    $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
    $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
    $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
    $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
    $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
    $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"
    ```

2. Créez une carte réseau pour chaque instance principale et ajoutez une configuration IPv6 :

    ```azurecli
    $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
    $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

    $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule2V4Id
    $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name
    ```

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Créer les ressources de machines virtuelles principales et associer chaque carte réseau

Pour créer des machines virtuelles, vous devez disposer d’un compte de stockage. Pour l’équilibrage de charge, les machines virtuelles doivent être membres d’un groupe à haute disponibilité. Pour plus d’informations sur la création des machines virtuelles, consultez [Créer une machine virtuelle Windows avec PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json).

1. Configurez les variables PowerShell :

    ```powershell
    $storageAccountName = "ps08092016v6sa0"
    $availabilitySetName = "myIPv4IPv6AvailabilitySet"
    $vm1Name = "myIPv4IPv6VM1"
    $vm2Name = "myIPv4IPv6VM2"
    $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
    $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
    $disk1Name = "WindowsVMosDisk1"
    $disk2Name = "WindowsVMosDisk2"
    $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
    $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
    $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
    $vmUserName = "vmUser"
    $mySecurePassword = "PlainTextPassword*1"
    ```

    > [!WARNING]
    > Cet exemple utilise le nom d’utilisateur et le mot de passe pour les machines virtuelles, en texte en clair. Prenez les mesures nécessaires lorsque vous utilisez ces informations d’identification en texte en clair. Pour découvrir une méthode plus sécurisée de traitement des informations d’identification dans PowerShell, consultez la cmdlet [`Get-Credential`](https://technet.microsoft.com/library/hh849815.aspx).

2. Créez le compte de stockage et le groupe à haute disponibilité.

    Vous pouvez utiliser un compte de stockage existant lors de la création des machines virtuelles. Vous créez un compte de stockage avec la commande suivante :

    ```azurecli
    $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"
    ```

3. Créez le groupe à haute disponibilité :

    ```azurecli
    $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location
    ```

4. Créez les machines virtuelles avec les cartes réseau associées :

    ```azurecli
    $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

    $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension
    ```

## <a name="next-steps"></a>Étapes suivantes

[Prise en main de la configuration d’un équilibrage de charge interne](load-balancer-get-started-ilb-arm-cli.md)  
[Configuration d'un mode de distribution d'équilibrage de charge](load-balancer-distribution-mode.md)  
[Configuration des paramètres du délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
