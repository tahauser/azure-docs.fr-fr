---
title: "Gérer les groupes de sécurité réseau - Azure CLI | Microsoft Docs"
description: "Découvrez comment gérer les groupes de sécurité réseau à l’aide de l’interface de ligne de commande Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: ed17d314-07e6-4c7f-bcf1-a8a2535d7c14
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2017
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36ff6d8fc956f5c863884e4591cbcb2909fcb200
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="manage-network-security-groups-using-the-azure-cli"></a>Gérer les groupes de sécurité réseau avec Azure CLI

[!INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../resource-manager-deployment-model.md). Cet article traite de l’utilisation du modèle de déploiement Resource Manager que Microsoft recommande pour la plupart des nouveaux déploiements à la place du modèle de déploiement classique.
> 

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="prerequisite"></a>Configuration requise
Si vous ne l’avez pas encore fait, installez et configurez la dernière version d’[Azure CLI 2.0](/cli/azure/install-az-cli2) et connectez-vous à un compte Azure par le biais de la commande [az login](/cli/azure/#az_login). 

## <a name="view-existing-nsgs"></a>Afficher les groupes de sécurité réseau existants
Pour afficher la liste des groupes de sécurité réseau d’un groupe de ressources spécifique, exécutez la commande [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) avec un format de sortie `-o table` :

```azurecli
az network nsg list -g RG-NSG -o table
```

Sortie attendue :

    Location    Name          ProvisioningState    ResourceGroup    ResourceGuid
    ----------  ------------  -------------------  ---------------  ------------------------------------
    centralus   NSG-BackEnd   Succeeded            RG-NSG           <guid>
    centralus   NSG-FrontEnd  Succeeded            RG-NSG           <guid>

## <a name="list-all-rules-for-an-nsg"></a>Répertorier toutes les règles pour un groupe de sécurité réseau
Pour afficher les règles d’un groupe de sécurité réseau nommé **NSG-FrontEnd**, exécutez la commande [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show) avec un [filtre de requête JMESPATH](/cli/azure/query-az-cli2) et le format de sortie `-o table` :

```azurecli
    az network nsg show \
    --resource-group RG-NSG \
    --name NSG-FrontEnd \
    --query '[defaultSecurityRules[],securityRules[]][].{Name:name,Desc:description,Access:access,Direction:direction,DestPortRange:destinationPortRange,DestAddrPrefix:destinationAddressPrefix,SrcPortRange:sourcePortRange,SrcAddrPrefix:sourceAddressPrefix}' \
    -o table
```

Sortie attendue :

    Name                           Desc                                                    Access    Direction    DestPortRange    DestAddrPrefix    SrcPortRange    SrcAddrPrefix
    -----------------------------  ------------------------------------------------------  --------  -----------  ---------------  ----------------  --------------  -----------------
    AllowVnetInBound               Allow inbound traffic from all VMs in VNET              Allow     Inbound      *                VirtualNetwork    *               VirtualNetwork
    AllowAzureLoadBalancerInBound  Allow inbound traffic from azure load balancer          Allow     Inbound      *                *                 *               AzureLoadBalancer
    DenyAllInBound                 Deny all inbound traffic                                Deny      Inbound      *                *                 *               *
    AllowVnetOutBound              Allow outbound traffic from all VMs to all VMs in VNET  Allow     Outbound     *                VirtualNetwork    *               VirtualNetwork
    AllowInternetOutBound          Allow outbound traffic from all VMs to Internet         Allow     Outbound     *                Internet          *               *
    DenyAllOutBound                Deny all outbound traffic                               Deny      Outbound     *                *                 *               *
    rdp-rule                                                                               Allow     Inbound      3389             *                 *               Internet
    web-rule                                                                               Allow     Inbound      80               *                 *               Internet
> [!NOTE]
> Vous pouvez également utiliser [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) pour répertorier uniquement les règles personnalisées d’un groupe de sécurité réseau.
>

## <a name="view-nsg-associations"></a>Afficher les associations de groupes de sécurité réseau

Pour afficher les ressources auxquelles le groupe de sécurité réseau **NSG-FrontEnd** est associé, exécutez la commande `az network nsg show` : 

```azurecli
az network nsg show -g RG-NSG -n nsg-frontend --query '[subnets,networkInterfaces]'
```

Recherchez les propriétés **NetworkInterfaces** et **Subnets**, comme indiqué dans l’exemple de sortie suivant :

```json
[
  [
    {
      "addressPrefix": null,
      "etag": null,
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNET/subnets/FrontEnd",
      "ipConfigurations": null,
      "name": null,
      "networkSecurityGroup": null,
      "provisioningState": null,
      "resourceGroup": "RG-NSG",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  null
]
```

Dans l’exemple précédent, le groupe de sécurité réseau n’est associé à aucune interface réseau (carte réseau), mais il est associé à un sous-réseau nommé **FrontEnd**.

## <a name="add-a-rule"></a>Ajouter une règle
Pour ajouter une règle autorisant le trafic **entrant** sur le port **443** d’une machine vers le groupe de sécurité réseau **NSG-FrontEnd**, entrez la commande suivante :

```azurecli
az network nsg rule create  \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd  \
--name allow-https \
--description "Allow access to port 443 for HTTPS" \
--access Allow \
--protocol Tcp  \
--direction Inbound \
--priority 102 \
--source-address-prefix "*"  \
--source-port-range "*"  \
--destination-address-prefix "*" \
--destination-port-range "443"
```

Sortie attendue :

```json
{
  "access": "Allow",
  "description": "Allow access to port 443 for HTTPS",
  "destinationAddressPrefix": "*",
  "destinationPortRange": "443",
  "direction": "Inbound",
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
  "name": "allow-https",
  "priority": 102,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

## <a name="change-a-rule"></a>Modifier une règle
Pour modifier la règle créée précédemment qui permet d’autoriser le trafic entrant d’**Internet** uniquement, exécutez la commande [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) :

```azurecli
az network nsg rule update \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https \
--source-address-prefix Internet
```

Sortie attendue :

```json
{
"access": "Allow",
"description": "Allow access to port 443 for HTTPS",
"destinationAddressPrefix": "*",
"destinationPortRange": "443",
"direction": "Inbound",
"etag": "W/\"<guid>\"",
"id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https",
"name": "allow-https",
"priority": 102,
"protocol": "Tcp",
"provisioningState": "Succeeded",
"resourceGroup": "RG-NSG",
"sourceAddressPrefix": "Internet",
"sourcePortRange": "*"
}
```

## <a name="delete-a-rule"></a>Supprimer une règle
Pour supprimer la règle créée précédemment, exécutez la commande suivante :

```azurecli
az network nsg rule delete \
--resource-group RG-NSG \
--nsg-name NSG-FrontEnd \
--name allow-https
```


## <a name="associate-an-nsg-to-a-nic"></a>Associer un groupe de sécurité réseau à une carte réseau
Pour associer le groupe de sécurité réseau **NSG-FrontEnd** à la carte réseau **TestNICWeb1**, exécutez la commande [az network nic update](/cli/azure/network/nic#az_network_nic_update) :

```azurecli
az network nic update \
--resource-group RG-NSG \
--name TestNICWeb1 \
--network-security-group NSG-FrontEnd    
```

Sortie attendue :

```json
{
  "dnsSettings": {
    "appliedDnsServers": [],
    "dnsServers": [],
    "internalDnsNameLabel": null,
    "internalDomainNameSuffix": "k0wkaguidnqrh0ud.gx.internal.cloudapp.net",
    "internalFqdn": null
  },
  "enableAcceleratedNetworking": false,
  "enableIpForwarding": false,
  "etag": "W/\"<guid>\"",
  "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1",
  "ipConfigurations": [
    {
      "applicationGatewayBackendAddressPools": null,
      "etag": "W/\"<guid>\"",
      "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1",
      "loadBalancerBackendAddressPools": null,
      "loadBalancerInboundNatRules": null,
      "name": "ipconfig1",
      "primary": true,
      "privateIpAddress": "192.168.1.6",
      "privateIpAddressVersion": "IPv4",
      "privateIpAllocationMethod": "Static",
      "provisioningState": "Succeeded",
      "publicIpAddress": null,
      "resourceGroup": "RG-NSG",
      "subnet": {
        "addressPrefix": null,
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
        "ipConfigurations": null,
        "name": null,
        "networkSecurityGroup": null,
        "provisioningState": null,
        "resourceGroup": "RG-NSG",
        "resourceNavigationLinks": null,
        "routeTable": null
      }
    }
  ],
  "location": "centralus",
  "macAddress": "00-0D-3A-91-A9-60",
  "name": "TestNICWeb1",
  "networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/<guid>/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  },
  "primary": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "RG-NSG",
  "resourceGuid": "<guid>",
  "tags": {},
  "type": "Microsoft.Network/networkInterfaces",
  "virtualMachine": null
}
```

## <a name="dissociate-an-nsg-from-a-nic"></a>Dissocier un groupe de sécurité réseau d’une carte réseau

Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** de la carte réseau **TestNICWeb1**, exécutez à nouveau la commande [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) en remplaçant l’argument `--network-security-group` par une chaîne vide (`""`).

```azurecli
az network nic update --resource-group RG-NSG --name TestNICWeb3 --network-security-group ""
```

Dans la sortie, la clé `networkSecurityGroup` présente la valeur null.

## <a name="dissociate-an-nsg-from-a-subnet"></a>Dissocier un groupe de sécurité réseau d’un sous-réseau
Pour dissocier le groupe de sécurité réseau **NSG-FrontEnd** du sous-réseau **FrontEnd**, exécutez à nouveau la commande [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) en remplaçant l’argument `--network-security-group` par une chaîne vide (`""`).

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group ""
```

Dans la sortie, la clé `networkSecurityGroup` présente la valeur null.

## <a name="associate-an-nsg-to-a-subnet"></a>Association d’un groupe de sécurité réseau à un sous-réseau
Pour associer à nouveau le groupe de sécurité réseau **NSG-FrontEnd** au sous-réseau **FrontEnd**, exécutez la commande suivante :

```azurecli
az network vnet subnet update \
--resource-group RG-NSG \
--vnet-name testvnet \
--name FrontEnd \
--network-security-group NSG-FrontEnd
```

Dans la sortie, la clé `networkSecurityGroup` présente une valeur similaire à ce qui suit :

```json
"networkSecurityGroup": {
    "defaultSecurityRules": null,
    "etag": null,
    "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd",
    "location": null,
    "name": null,
    "networkInterfaces": null,
    "provisioningState": null,
    "resourceGroup": "RG-NSG",
    "resourceGuid": null,
    "securityRules": null,
    "subnets": null,
    "tags": null,
    "type": null
  }
  ```

## <a name="delete-an-nsg"></a>Suppression d'un groupe de sécurité réseau
Vous ne pouvez supprimer un groupe de sécurité réseau que s’il n’est associé à aucune ressource. Pour supprimer un groupe de sécurité réseau, effectuez les étapes suivantes :

1. Pour consulter les ressources associées à un groupe de sécurité réseau, exécutez `azure network nsg show` comme illustré dans [Afficher les associations de groupes de sécurité réseau](#View-NSGs-associations).
2. Si le groupe de sécurité réseau est associé à des cartes réseau, exécutez `azure network nic set` comme illustré dans [Dissocier un groupe de sécurité réseau d’une carte réseau](#Dissociate-an-NSG-from-a-NIC) pour chaque carte réseau. 
3. Si le groupe de sécurité réseau est associé à un sous-réseau, exécutez `azure network vnet subnet set` comme illustré dans [Dissocier un groupe de sécurité réseau d’un sous-réseau](#Dissociate-an-NSG-from-a-subnet) pour chaque sous-réseau.
4. Pour supprimer le groupe de sécurité réseau, exécutez la commande suivante :

    ```azurecli
    az network nsg delete --resource-group RG-NSG --name NSG-FrontEnd
    ```
## <a name="next-steps"></a>Étapes suivantes
* [Activez la journalisation](virtual-network-nsg-manage-log.md) des groupes de sécurité réseau.

