---
title: Créer des groupes de sécurité réseau - Azure CLI | Microsoft Docs
description: Découvrez comment créer et déployer des groupes de sécurité réseau avec Azure CLI.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 9ea82c09-f4a6-4268-88bc-fc439db40c48
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/17/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4827fabf1d8cde366dda8b3a782a2fefe01db8d5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2018
---
# <a name="create-network-security-groups-using-the-azure-cli"></a>Créer des groupes de sécurité réseau avec Azure CLI

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Les exemples de commandes Azure CLI suivants attendent un environnement simple existant, basé sur le scénario précédent. 

## <a name="create-the-nsg-for-the-frontend-subnet"></a>Créer le groupe de sécurité réseau du sous-réseau `FrontEnd`

Pour créer un groupe de sécurité réseau nommé *NSG-FrontEnd* conformément au scénario ci-dessus, suivez les étapes ci-dessous :

1. Si vous ne l’avez pas encore fait, installez et configurez la dernière version d’[Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure par le biais de la commande [az login](/cli/azure/reference-index#az_login). 

2. Créez un groupe de sécurité réseau par le biais de la commande [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create). 

    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-FrontEnd \
    --location centralus 
    ```

    Paramètres :
   
   * `--resource-group` : nom du groupe de ressources dans lequel le groupe de sécurité réseau est créé. Pour notre scénario, *TestRG*.
   * `--location` : région Azure dans laquelle le groupe de sécurité réseau est créé. Pour notre scénario, *westus*.
   * `--name` : nom du nouveau groupe de sécurité réseau. Pour notre scénario, *NSG-FrontEnd*.

    La sortie attendue comporte diverses informations, parmi lesquelles la liste de toutes les règles par défaut. L’exemple suivant présente les règles par défaut au moyen d’un filtre de requête JMESPATH avec le format de sortie `table` :

    ```azurecli
    az network nsg show \
    -g testrg \
    -n nsg-frontend \
    --query 'defaultSecurityRules[].{Access:access,Desc:description,DestPortRange:destinationPortRange,Direction:direction,Priority:priority}' \
    -o table
    ```
   
   Output:

        Access    Desc                                                    DestPortRange    Direction      Priority
        
        Allow     Allow inbound traffic from all VMs in VNET              *                Inbound           65000
        Allow     Allow inbound traffic from azure load balancer          *                Inbound           65001
        Deny      Deny all inbound traffic                                *                Inbound           65500
        Allow     Allow outbound traffic from all VMs to all VMs in VNET  *                Outbound          65000
        Allow     Allow outbound traffic from all VMs to Internet         *                Outbound          65001
        Deny      Deny all outbound traffic                               *                Outbound          65500



3. Créez une règle permettant d’accéder au port 3389 (RDP) à partir d’Internet par le biais de la commande [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create).

    > [!NOTE]
    > En fonction de l’interpréteur de commandes que vous utilisez, vous devrez peut-être modifier le caractère `*` dans les arguments suivants afin de ne pas développer l’argument avant exécution.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name rdp-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 3389
    ```
   
    Sortie attendue :
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "3389",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
        "name": "rdp-rule",
        "priority": 100,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

    Paramètres :

    * `--resource-group testrg` : groupe de ressources à utiliser. Ce paramètre est sensible à la casse.
    * `--nsg-name NSG-FrontEnd` : nom du groupe de sécurité réseau dans lequel la règle est créée.
    * `--name rdp-rule` : nom de la nouvelle règle.
    * `--access Allow` : niveau d’accès de la règle (Deny ou Allow).
    * `--protocol Tcp`: protocole (TCP, UDP ou *).
    * `--direction Inbound` : direction de la connexion (Inbound ou Outbound).
    * `--priority 100` : priorité de la règle.
    * `--source-address-prefix Internet` : préfixe de l’adresse source dans CIDR ou à l’aide de balises par défaut.
    * `--source-port-range "*"` : port source ou plage de ports. Port qui a ouvert la connexion.
    * `--destination-address-prefix "*"` : préfixe de l’adresse de destination dans CIDR ou à l’aide de balises par défaut.
    * `--destination-port-range 3389` : port de destination ou plage de ports. Port qui reçoit la requête de connexion.



4. Créez une règle permettant d’accéder au port 80 (HTTP) à partir d’Internet par le biais de la commande **az network nsg rule create**.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-FrontEnd \
    --name web-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 200 \
    --source-address-prefix Internet \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 80
    ```
   
    Sortie attendue :
   
    ```json
    {
        "access": "Allow",
        "description": null,
        "destinationAddressPrefix": "*",
        "destinationPortRange": "80",
        "direction": "Inbound",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
        "name": "web-rule",
        "priority": 200,
        "protocol": "Tcp",
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "sourceAddressPrefix": "Internet",
        "sourcePortRange": "*"
    }
    ```

5. Liez le groupe de sécurité réseau au sous-réseau**FrontEnd** par le biais de la commande [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).
        
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name FrontEnd \
    --resource-group testrg \
    --network-security-group NSG-FrontEnd
    ```
   
    Sortie attendue :
   
    ```json
    {
        "addressPrefix": "192.168.1.0/24",
        "etag": "W/\"<guid>\"",
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
        "ipConfigurations": [
            {
            "etag": null,
            "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
            "name": null,
            "privateIpAddress": null,
            "privateIpAllocationMethod": null,
            "provisioningState": null,
            "publicIpAddress": null,
            "resourceGroup": "TestRG",
            "subnet": null
            }
        ],
        "name": "FrontEnd",
        "networkSecurityGroup": {
            "defaultSecurityRules": null,
            "etag": null,
            "id": "/subscriptions/<guid>f/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
            "location": null,
            "name": null,
            "networkInterfaces": null,
            "provisioningState": null,
            "resourceGroup": "testrg",
            "resourceGuid": null,
            "securityRules": null,
            "subnets": null,
            "tags": null,
            "type": null
        },
        "provisioningState": "Succeeded",
        "resourceGroup": "testrg",
        "resourceNavigationLinks": null,
        "routeTable": null
    }
    ```

## <a name="create-the-nsg-for-the-backend-subnet"></a>Créer le groupe de sécurité réseau du sous-réseau `BackEnd`
Pour créer un groupe de sécurité réseau nommé *NSG-BackEnd* conformément au scénario ci-dessus, suivez les étapes ci-dessous.

1. Créez le groupe de sécurité réseau `NSG-BackEnd` par le biais de la commande **az network nsg create**.
   
    ```azurecli
    az network nsg create \
    --resource-group testrg \
    --name NSG-BackEnd \
    --location centralus
    ```
   
    Comme dans l’étape 2 ci-dessus, la sortie attendue est assez volumineuse et elle comprend les règles par défaut.
   
2. Créez une règle permettant d’accéder au port 1433 (SQL) à partir du sous-réseau `FrontEnd` par le biais de la commande **az network nsg rule create**.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name sql-rule \
    --access Allow \
    --protocol Tcp \
    --direction Inbound \
    --priority 100 \
    --source-address-prefix 192.168.1.0/24 \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range 1433
    ```
   
    Sortie attendue :

    ```json  
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "1433",
    "direction": "Inbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule",
    "name": "sql-rule",
    "priority": 100,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "192.168.1.0/24",
    "sourcePortRange": "*"
    }
    ```

3. Créez une règle qui refuse l’accès à Internet par le biais de la commande **az network nsg rule create**.
   
    ```azurecli
    az network nsg rule create \
    --resource-group testrg \
    --nsg-name NSG-BackEnd \
    --name web-rule \
    --access Deny \
    --protocol Tcp  \
    --direction Outbound  \
    --priority 200 \
    --source-address-prefix "*" \
    --source-port-range "*" \
    --destination-address-prefix "*" \
    --destination-port-range "*"
    ```
   
    Sortie attendue :
   
    ```json
    {
    "access": "Deny",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "*",
    "direction": "Outbound",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
    "name": "web-rule",
    "priority": 200,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

4. Liez le groupe de sécurité réseau au sous-réseau `BackEnd` par le biais de la commande **az network vnet subnet set**.
   
    ```azurecli
    az network vnet subnet update \
    --vnet-name TestVNET \
    --name BackEnd \
    --resource-group testrg \
    --network-security-group NSG-BackEnd
    ```
   
    Sortie attendue :
   
    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/BackEnd",
    "ipConfigurations": null,
    "name": "BackEnd",
    "networkSecurityGroup": {
        "defaultSecurityRules": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd",
        "location": null,
        "name": null,
        "networkInterfaces": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "resourceGuid": null,
        "securityRules": null,
        "subnets": null,
        "tags": null,
        "type": null
    },
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": null
    }
    ```
