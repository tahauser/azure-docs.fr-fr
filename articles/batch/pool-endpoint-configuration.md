---
title: "Configurer des points de terminaison de nœud dans le pool Azure Batch| Microsoft Docs"
description: "Comment configurer ou désactiver l’accès aux ports SSH ou RDP sur les nœuds de calcul dans un pool Azure Batch."
services: batch
author: dlepow
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 02/13/2018
ms.author: danlep
ms.openlocfilehash: fdc68744406c3e995a2764f93d4474b807337ff5
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="configure-or-disable-remote-access-to-compute-nodes-in-an-azure-batch-pool"></a>Configurer ou désactiver l’accès à distance aux nœuds de calcul dans un pool Azure Batch

Par défaut, Batch autorise les [utilisateurs du nœud](/rest/api/batchservice/computenode/adduser) disposant d’une connectivité réseau à se connecter en externe à un nœud de calcul dans un pool Batch. Par exemple, un utilisateur peut se connecter en utilisant un protocole RDP (Remote Desktop Protocol) sur le port 3389 pour un nœud de calcul dans un pool Windows. De même, par défaut, un utilisateur peut se connecter à un nœud de calcul dans un pool Linux en utilisant un protocole SSH (Secure Shell) sur le port 22. 

Dans votre environnement, vous devrez peut-être limiter ou désactiver ces paramètres d’accès externe par défaut. Vous pouvez modifier ces paramètres en utilisant les API Batch pour définir la propriété [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration). 

## <a name="about-the-pool-endpoint-configuration"></a>À propos de la configuration de point de terminaison de pool
La configuration de point de terminaison se compose d’un ou plusieurs [pools de traduction d’adresses réseau (NAT)](/rest/api/batchservice/pool/add#inboundnatpool) de ports frontaux. (Ne confondez pas un pool NAT avec le pool Batch des nœuds de calcul.) Vous configurez chaque pool NAT pour remplacer les paramètres de connexion par défaut sur les nœuds de calcul du pool. 

Chaque configuration de pool NAT inclut les [règles d’un ou plusieurs groupes de sécurité réseau (NSG)](/rest/api/batchservice/pool/add#networksecuritygrouprule). Chaque règle NSG autorise ou refuse certains trafics réseau au point de terminaison. Vous pouvez choisir d’autoriser ou de refuser tout le trafic, le trafic identifié par une [balise par défaut](../virtual-network/virtual-networks-nsg.md#default-tags) (par exemple, « Internet »), ou le trafic provenant d’adresses IP ou de sous-réseaux spécifiques.

### <a name="considerations"></a>Considérations
* La configuration de point de terminaison de pool fait partie de la [configuration réseau](/rest/api/batchservice/pool/add#NetworkConfiguration) du pool. La configuration réseau peut éventuellement inclure des paramètres permettant de joindre le pool à un [réseau virtuel Azure](batch-virtual-network.md). Si vous configurez le pool dans un réseau virtuel, vous pouvez créer des règles NSG qui utilisent des paramètres d’adresse dans le réseau virtuel.
* Vous pouvez configurer plusieurs règles NSG lorsque vous configurez un pool NAT. Les règles sont vérifiées dans l’ordre de priorité. Une fois qu’une règle s’applique, plus aucune correspondance de règle n’est testée.


## <a name="example-deny-all-rdp-traffic"></a>Exemple : refuser tout le trafic RDP

L’extrait de code C# suivant montre comment configurer le point de terminaison RDP sur les nœuds de calcul dans un pool Windows afin de refuser tout le trafic réseau. Le point de terminaison utilise un pool frontal de ports dans la plage *60000 - 60099*. 

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
      new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 60000, 60099, new NetworkSecurityGroupRule[]
        {
            new NetworkSecurityGroupRule(162, NetworkSecurityGroupRuleAccess.Deny, "*"),
        })
    })    
};
```

## <a name="example-deny-all-ssh-traffic-from-the-internet"></a>Exemple : refuser tout trafic SSH provenant d’Internet

L’extrait de code Python suivant montre comment configurer le point de terminaison SSH sur les nœuds de calcul dans un pool Linux afin de refuser tout le trafic Internet. Le point de terminaison utilise un pool frontal de ports dans la plage *4000 - 4100*. 

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access=batchmodels.NetworkSecurityGroupRuleAccess.deny,
                source_address_prefix='Internet'
                )
            ]
        )
        ]
    ) 
)
```

## <a name="example-allow-rdp-traffic-from-a-specific-ip-address"></a>Exemple : autoriser le trafic RDP à partir d’une adresse IP spécifique

L’extrait de code C# suivant montre comment configurer le point de terminaison RDP sur les nœuds de calcul dans un pool Windows afin d’autoriser l’accès RDP uniquement à partir de l’adresse IP *198.51.100.7*. La deuxième règle NSG refuse le trafic qui ne correspond pas à l’adresse IP.

```csharp
pool.NetworkConfiguration = new NetworkConfiguration
{
    EndpointConfiguration = new PoolEndpointConfiguration(new InboundNatPool[]
    {
        new InboundNatPool("RDP", InboundEndpointProtocol.Tcp, 3389, 7500, 8000, new NetworkSecurityGroupRule[]
        {   
            new NetworkSecurityGroupRule(179,NetworkSecurityGroupRuleAccess.Allow, "198.51.100.7"),
            new NetworkSecurityGroupRule(180,NetworkSecurityGroupRuleAccess.Deny, "*")
        })
    })    
};
```

## <a name="example-allow-ssh-traffic-from-a-specific-subnet"></a>Exemple : autoriser le trafic SSH à partir d’un sous-réseau spécifique

L’extrait de code Python suivant montre comment configurer le point de terminaison SSH sur les nœuds de calcul dans un pool Linux afin d’autoriser l’accès uniquement à partir du sous-réseau *192.168.1.0/24*. La deuxième règle NSG refuse le trafic qui ne correspond pas au sous-réseau.

```python
pool.network_configuration=batchmodels.NetworkConfiguration(
    endpoint_configuration=batchmodels.PoolEndpointConfiguration(
        inbound_nat_pools=[batchmodels.InboundNATPool(
            name='SSH',
            protocol='tcp',
            backend_port=22,
            frontend_port_range_start=4000,
            frontend_port_range_end=4100,
            network_security_group_rules=[
                batchmodels.NetworkSecurityGroupRule(
                priority=170,
                access='allow',
                source_address_prefix='192.168.1.0/24'
                ),
                batchmodels.NetworkSecurityGroupRule(
                priority=175,
                access='deny',
                source_address_prefix='*'
                )
            ]
        )
        ]
    )
)
```

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les règles NSG dans Azure, consultez l’article [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).

- Pour obtenir une présentation détaillée de Batch, consultez [Développer des solutions de calcul parallèles à grande échelle avec Batch](batch-api-basics.md).

