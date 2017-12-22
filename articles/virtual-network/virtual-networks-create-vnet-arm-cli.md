---
title: "Créer un réseau virtuel - Azure CLI | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel à l’aide d’Azure CLI."
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 659a791124eab002290ac0b7f0898cf1c06c2951
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Créer un réseau virtuel à l’aide d’Azure CLI

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure propose deux modèles de déploiement : Azure Resource Manager et classique. Microsoft recommande la création de ressources via le modèle de déploiement Resource Manager. Pour en savoir plus sur les différences entre deux modèles, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md).

Vous pouvez également créer un réseau virtuel via Resource Manager à l’aide d’autres outils ou créer un réseau virtuel via le modèle de déploiement classique en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
> * [Portail](virtual-networks-create-vnet-arm-pportal.md)
> * [PowerShell](virtual-networks-create-vnet-arm-ps.md)
> * [INTERFACE DE LIGNE DE COMMANDE](virtual-networks-create-vnet-arm-cli.md)
> * [Modèle](virtual-networks-create-vnet-arm-template-click.md)
> * [Portail (classique)](virtual-networks-create-vnet-classic-pportal.md)
> * [PowerShell (classique)](virtual-networks-create-vnet-classic-netcfg-ps.md)
> * [Interface de ligne de commande (classique)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Pour créer un réseau virtuel à l’aide d’Azure CLI, procédez comme suit :

1. Installez et configurez la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure avec la commande [az login](/cli/azure/#login).

2. Créez un groupe de ressources pour votre réseau virtuel à l’aide de commande [az group create](/cli/azure/group#create) avec les arguments `--name` et `--location` :

    ```azurecli
    az group create --name TestRG --location centralus
    ```

3. Créez un réseau virtuel et un sous-réseau :

    ```azurecli
    az network vnet create \
    --name TestVNet \
    --resource-group TestRG \
    --location centralus \
    --address-prefix 192.168.0.0/16 \
    --subnet-name FrontEnd \
    --subnet-prefix 192.168.1.0/24
    ```

    Sortie attendue :
    
    ```json
    {
        "newVNet": {
            "addressSpace": {
            "addressPrefixes": [
            "192.168.0.0/16"
            ]
            },
            "dhcpOptions": {
            "dnsServers": []
            },
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>",
            "subnets": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                "name": "FrontEnd",
                "properties": {
                "addressPrefix": "192.168.1.0/24",
                "provisioningState": "Succeeded"
                },
                "resourceGroup": "TestRG"
            }
            ]
            }
    }
    ```

    Paramètres utilisés :

    - `--name TestVNet` : Nom du réseau virtuel à créer.
    - `--resource-group TestRG` : # Nom du groupe de ressources qui contrôle la ressource. 
    - `--location centralus` : l’emplacement vers lequel effectuer le déploiement.
    - `--address-prefix 192.168.0.0/16` : préfixe et bloc d’adresse source.  
    - `--subnet-name FrontEnd` : nom du sous-réseau.
    - `--subnet-prefix 192.168.1.0/24` : préfixe et bloc d’adresse source.

    Pour répertorier les informations de base à utiliser dans la commande suivante, vous pouvez interroger le réseau virtuel à l’aide un [filtre de requête](/cli/azure/query-az-cli2) :

    ```azurecli
    az network vnet list --query '[?name==`TestVNet`].{Where:location,Name:name,Group:resourceGroup}' -o table
    ```

    Ce qui génère la sortie suivante :

        Where      Name      Group

        centralus  TestVNet  TestRG

4. Créez un sous-réseau :

    ```azurecli
    az network vnet subnet create \
    --address-prefix 192.168.2.0/24 \
    --name BackEnd \
    --resource-group TestRG \
    --vnet-name TestVNet
    ```

    Sortie attendue :

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid> \"",
    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "TestRG",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```

    Paramètres utilisés :

    - `--address-prefix 192.168.2.0/24` : bloc CIDR de sous-réseau.
    - `--name BackEnd` : nom du nouveau sous-réseau.
    - `--resource-group TestRG` : le groupe de ressources.
    - `--vnet-name TestVNet` : le nom du réseau virtuel propriétaire.

5. Interrogez les propriétés du nouveau réseau virtuel :

    ```azurecli
    az network vnet show \
    -g TestRG \
    -n TestVNet \
    --query '{Name:name,Where:location,Group:resourceGroup,Status:provisioningState,SubnetCount:subnets | length(@)}' \
    -o table
    ```

    Sortie attendue :

        Name      Where      Group    Status       SubnetCount

        TestVNet  centralus  TestRG   Succeeded              2

6. Interrogez les propriétés des sous-réseaux :

    ```azurecli
    az network vnet subnet list \
    -g TestRG \
    --vnet-name testvnet \
    --query '[].{Name:name,CIDR:addressPrefix,Status:provisioningState}' \
    -o table
    ```

    Sortie attendue :

        Name      CIDR            Status

        FrontEnd  192.168.1.0/24  Succeeded
        BackEnd   192.168.2.0/24  Succeeded

## <a name="next-steps"></a>Étapes suivantes

Apprenez à connecter :

- Une machine virtuelle à un réseau virtuel en lisant l’article [Création d’une machine virtuelle Linux](../virtual-machines/linux/quick-create-cli.md). Au lieu de créer un réseau virtuel et un sous-réseau comme indiqué dans les procédures de ces articles, vous pouvez sélectionner un réseau virtuel et un sous-réseau existants pour établir la connexion à une machine virtuelle.
- Le réseau virtuel à d’autres réseaux virtuels en lisant l’article sur la [connexion des réseaux virtuels](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- Le réseau virtuel à un réseau local à l’aide d’un réseau privé virtuel (VPN) site à site ou d’un circuit ExpressRoute. Découvrez comment en lisant [Connecter un réseau virtuel à un réseau local à l’aide d’un VPN de site à site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) et [Lier un réseau virtuel à un circuit ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).