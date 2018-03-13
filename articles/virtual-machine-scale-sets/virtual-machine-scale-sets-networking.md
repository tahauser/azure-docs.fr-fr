---
title: "Mise en réseau pour les groupes de machines virtuelles identiques Azure | Microsoft Docs"
description: "Propriétés de mise en réseau de configuration pour des groupes de machines virtuelles identiques Azure."
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: negat
ms.openlocfilehash: 27f1ec18026b38d5cdb2aecfde2d01f32a86349e
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Mise en réseau pour des groupes de machines virtuelles identiques Azure

Lorsque vous déployez un groupe de machines virtuelles identiques Azure via le portail, certaines propriétés de réseau sont définies par défaut, comme un équilibrage de charge Azure avec des règles NAT entrantes. Cet article explique comment utiliser certaines des fonctionnalités avancées de mise en réseau, que vous pouvez configurer avec les groupes identiques.

Vous pouvez configurer toutes les fonctionnalités abordées dans cet article à l’aide des modèles Azure Resource Manager. Des exemples d’interfaces de ligne de commande Azure et Powershell sont également inclus pour les fonctionnalités sélectionnées. Utilisez CLI 2.10 et PowerShell 4.2.0 ou version ultérieure.

## <a name="accelerated-networking"></a>Mise en réseau accélérée
La mise en réseau accélérée Azure améliore les performances du réseau en activant la virtualisation d’E/S de racine unique (SR-IOV) sur une machine virtuelle. Pour plus d’informations sur la mise en réseau accélérée, consultez Mise en réseau accélérée pour machines virtuelles [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md) ou [Linux](../virtual-network/create-vm-accelerated-networking-cli.md). Pour utiliser la mise en réseau accélérée avec des groupes identiques, définissez enableAcceleratedNetworking sur **true** dans les paramètres networkInterfaceConfigurations du groupe identique. Par exemple : 
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="create-a-scale-set-that-references-an-existing-azure-load-balancer"></a>Créer un groupe identique qui fait référence à un équilibrage de charge Azure existant
Lorsqu’un groupe identique est créé à l’aide du portail Azure, un nouvel équilibrage de charge est créé pour la plupart des options de configuration. Si vous créez un groupe identique qui doit faire référence à un équilibrage de charge existant, vous pouvez le faire à l’aide d’une interface CLI. L’exemple de script suivant crée un équilibrage de charge et crée ensuite un groupe identique qui y fait référence :
```bash
az network lb create -g lbtest -n mylb --vnet-name myvnet --subnet mysubnet --public-ip-address-allocation Static --backend-pool-name mybackendpool

az vmss create -g lbtest -n myvmss --image Canonical:UbuntuServer:16.04-LTS:latest --admin-username negat --ssh-key-value /home/myuser/.ssh/id_rsa.pub --upgrade-policy-mode Automatic --instance-count 3 --vnet-name myvnet --subnet mysubnet --lb mylb --backend-pool-name mybackendpool

```

## <a name="configurable-dns-settings"></a>Paramètres DNS configurables
Par défaut, les groupes identiques adoptent les paramètres DNS spécifiques du réseau virtuel et du sous-réseau dans lesquels ils ont été créés. Toutefois, vous pouvez configurer les paramètres DNS directement pour un groupe identique.
~
### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Création d’un groupe identique avec les serveurs DNS configurables
Pour créer un groupe identique avec une configuration DNS personnalisée à l’aide de CLI 2.0, ajoutez l’argument **--dns-servers** à la commande **vmss create**, suivi des adresses IP des serveurs, séparées par un espace. Par exemple : 
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
Pour configurer des serveurs DNS personnalisés dans un modèle Azure, ajoutez une propriété dnsSettings à la section networkInterfaceConfigurations du groupe identique. Par exemple : 
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Création d’un groupe identique avec des noms de domaine de machines virtuelles configurables
Pour créer un groupe identique avec un nom DNS personnalisé pour des machines virtuelles avec CLI 2.0, ajoutez l’argument **--vm-domain-name** à la commande **vmss create**, suivi d’une chaîne représentant le nom de domaine.

Pour définir le nom de domaine dans un modèle Azure, ajoutez une propriété **dnsSettings** à la section **networkInterfaceConfigurations** du groupe identique. Par exemple : 

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

Pour le nom dns d’une machine virtuelle individuelle, le résultat devrait être similaire à : 
```
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>IPv4 publique par machine virtuelle
En règle générale, les machines virtuelles des groupes identiques Azure ne nécessitent pas leurs propres adresses IP publiques. Dans la plupart des cas, l’association d’une adresse IP publique à un équilibrage de charge ou à une machine virtuelle individuelle (ou jumpbox) qui achemine les connexions entrantes vers les machines virtuelles de groupes identiques selon les besoins (par exemple, via des règles NAT entrantes) constitue une solution plus économique et plus sûre.

Toutefois, dans certains cas, les machines virtuelles de groupes identiques doivent posséder leurs propres adresses IP publiques. Par exemple, dans le cas des jeux vidéo, lorsqu’une console doit être directement connectée à une machine virtuelle sur Cloud qui procède à un traitement physique du jeu. Autre exemple : lorsque des machines virtuelles doivent établir des connexions externes entre elles, dans différentes régions, dans une base de données distribuée.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Création d’un groupe identique avec IP public par machine virtuelle
Pour créer un groupe identique qui attribue une adresse IP publique à chaque machine virtuelle avec CLI 2.0, ajoutez le paramètre **--public-ip-per-vm** à la commande **vmss create**. 

Pour créer un groupe identique à l’aide d’un modèle Azure, assurez-vous que la version API de la ressource Microsoft.Compute/virtualMachineScaleSets correspond au moins à la version du **30/03/2017**, et ajoutez une propriété JSON **publicIpAddressConfiguration** à la section ipConfigurations du groupe identique. Par exemple : 

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
Exemple de modèle : [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Interrogation des adresses IP publiques des machines virtuelles dans un groupe identique
Pour répertorier les adresses IP publiques attribuées à des machines virtuelles d’un groupe identique avec CLI 2.0, utilisez la commande **az vmss list-instance-public-ips**.

Pour répertorier les adresses IP publiques d’un groupe identique à l’aide de PowerShell, utilisez la commande _Get-AzureRmPublicIpAddress_. Par exemple : 
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Vous pouvez également interroger les adresses IP publiques en référençant directement l’ID de ressource de la configuration d’adresse IP publique. Par exemple : 
```PowerShell
PS C:\> Get-AzureRmPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Pour interroger les adresses IP publiques attribuées à des machines virtuelles d’un groupe identique en utilisant [Azure Resource Explorer](https://resources.azure.com) ou l’API REST Azure avec la version **30/03/2017** ou ultérieure.

Pour afficher les adresses IP publiques pour un groupe identique avec Resource Explorer, reportez-vous à la section **publicipaddresses** de votre groupe identique. Par exemple : https://resources.azure.com/subscriptions/_your_sub_id_/resourceGroups/_your_rg_/providers/Microsoft.Compute/virtualMachineScaleSets/_your_vmss_/publicipaddresses

```
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Exemple de sortie :
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Plusieurs adresses IP par carte réseau
Une ou plusieurs configurations IP peuvent être associées à chaque carte réseau attachée à une machine virtuelle, dans un groupe identique. Une adresse IP privée est affectée à chaque configuration. Une ressource d’adresse IP publique peut également être associée à chaque configuration. Pour comprendre combien d’adresses IP peuvent être attribuées à une carte réseau et combien d’adresses IP publiques vous pouvez utiliser dans un abonnement Azure, consultez [Limites Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Plusieurs cartes réseau par machine virtuelle
Chaque machine virtuelle peut compter jusqu’à 8 cartes réseau, en fonction de la taille de la machine. Le nombre maximal de cartes réseau par machine est indiqué dans l’[article sur la taille des machines virtuelles](../virtual-machines/windows/sizes.md). L’exemple suivant est un profil réseau de groupe identique illustrant plusieurs entrées de cartes réseau et plusieurs adresses IP publiques par machine virtuelle :
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg-per-scale-set"></a>Groupes de sécurité réseau par groupe identique
Des groupes de sécurité réseau peuvent être appliqués directement à un groupe identique en ajoutant une référence à la section de configuration de l’interface réseau des propriétés des machines virtuelles du groupe identique.

Par exemple :  
```
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            }
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les réseaux virtuels Azure, consultez la page [Présentation du réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).
