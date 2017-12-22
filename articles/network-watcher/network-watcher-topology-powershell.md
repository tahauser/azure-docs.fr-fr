---
title: Afficher la topologie Azure Network Watcher - PowerShell | Microsoft Docs
description: "Cet article explique comment utiliser PowerShell pour interroger la topologie de votre réseau."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: bd0e882d-8011-45e8-a7ce-de231a69fb85
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 84e925b4461e55e570e9848bf03d3d352bfff898
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="view-network-watcher-topology-with-powershell"></a>Afficher la topologie Network Watcher par le biais de PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [API REST](network-watcher-topology-rest.md)

La fonctionnalité Topologie de Network Watcher fournit une représentation visuelle des ressources réseau dans un abonnement. Dans le portail, cette visualisation vous est présentée automatiquement. Les informations relatives à la vue Topologie du portail peuvent être récupérées par le biais de PowerShell.
Cette fonctionnalité rend les informations de topologie plus polyvalentes, car les données peuvent être utilisées par d’autres outils pour générer la visualisation.

L’interconnexion est modélisée sous deux relations.

- **Relation d’imbrication** - exemple : le réseau virtuel contient un sous-réseau qui contient une carte réseau
- **Associé** - exemple : une carte réseau est associée à une machine virtuelle

La liste suivante répertorie les propriétés renvoyées lors de l’interrogation de l’API REST Topologie.

* **name** : nom du groupe de ressources
* **id** : URI de la ressource.
* **location** : emplacement de la ressource.
* **associations** : liste des associations réalisées vis-à-vis de l’objet référencé.
    * **name** : nom de la ressource référencée.
    * **resourceId** : URI de la ressource référencée dans l’association.
    * **associationType** : cette valeur fait référence à la relation entre l’objet enfant et le parent. Les valeurs valides sont **Contains** et **Associated**.

## <a name="before-you-begin"></a>Avant de commencer

Dans ce scénario, vous utilisez l’applet de commande `Get-AzureRmNetworkWatcherTopology` pour récupérer les informations de topologie. Un article explique aussi comment [récupérer la topologie du réseau avec l’API REST](network-watcher-topology-rest.md).

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article récupère la réponse de la topologie pour un groupe de ressources donné.

## <a name="retrieve-network-watcher"></a>Récupérer Network Watcher

La première étape consiste à récupérer l’instance de Network Watcher. La variable `$networkWatcher` est transmise à l’applet de commande `Get-AzureRmNetworkWatcherTopology`.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="retrieve-topology"></a>Récupérer la topologie

L’applet de commande `Get-AzureRmNetworkWatcherTopology` récupère la topologie pour un groupe de ressources donné.

```powershell
Get-AzureRmNetworkWatcherTopology -NetworkWatcher $networkWatcher -TargetResourceGroupName testrg
```

## <a name="results"></a>Résultats

Les résultats renvoyés affichent une propriété nommée « Resources », qui contient le corps de réponse json pour l’applet de commande `Get-AzureRmNetworkWatcherTopology`.  La réponse contient les ressources du groupe de sécurité réseau et leurs associations (autrement dit, Contains et Associated).

```json
Id              : 00000000-0000-0000-0000-000000000000
CreatedDateTime : 2/1/2017 7:52:21 PM
LastModified    : 2/1/2017 7:46:18 PM
Resources       : [
                    {
                      "Name": "testrg-vnet",
                      "Id":
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet/subnets/default"
                        }
                      ]
                    },
                    {
                      "Name": "default",
                      "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testr
                  g-vnet/subnets/default",
                      "Location": "westcentralus",
                      "Associations": []
                    },
                    {
                      "Name": "testrg-vnet2",
                      "Id": 
                  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet2",
                      "Location": "westcentralus",
                      "Associations": [
                        {
                          "AssociationType": "Contains",
                          "Name": "default",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/default"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "GatewaySubnet",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworks/testrg-vnet2/subnets/GatewaySubnet"
                        },
                        {
                          "AssociationType": "Contains",
                          "Name": "gateway2",
                          "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNe
                  tworkGateways/gateway2"
                        }
                      ]
                    },
                    ...
                  ]
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment visualiser vos journaux de flux NSG avec Power BI en consultant la page [Visualize NSG flows logs with Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) (Visualiser les journaux de flux NSG avec Power BI)


