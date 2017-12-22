---
title: "Rechercher le tronçon suivant avec la fonction Tronçon suivant Azure Network Watcher - REST | Microsoft Docs"
description: "Cet article explique comment rechercher le type de tronçon suivant et l’adresse IP à l’aide de la fonction Tronçon suivant par le biais de l’API REST"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 2216c059-45ba-4214-8304-e56769b779a6
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b541cd5cb7e49468af2c522b16c3a3b9fe75fd54
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="find-out-what-the-next-hop-type-is-using-the-next-hop-capability-in-aure-network-watcher-using-azure-rest-api"></a>Découvrez le type de tronçon suivant grâce à la fonction Tronçon suivant Azure Network Watcher à l’aide de l’API REST

> [!div class="op_single_selector"]
> - [portail Azure](network-watcher-check-next-hop-portal.md)
> - [PowerShell](network-watcher-check-next-hop-powershell.md)
> - [CLI 1.0](network-watcher-check-next-hop-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-next-hop-cli.md)
> - [API REST Azure](network-watcher-check-next-hop-rest.md)

Tronçon suivant est une fonctionnalité de Network Watcher qui permet d’obtenir le type de tronçon suivant et l’adresse IP à partir d’une machine virtuelle spécifiée. Cette fonctionnalité est utile pour déterminer si le trafic sortant d’une machine virtuelle passe par une passerelle, Internet ou des réseaux virtuels pour atteindre sa destination.

## <a name="before-you-begin"></a>Avant de commencer

ARMclient permet d’appeler l’API REST à l’aide de PowerShell. ARMClient est accessible sur le site chocolatey à partir de la page [ARMClient sur Chocolatey](https://chocolatey.org/packages/ARMClient).

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Créer une instance d’Azure Network Watcher](network-watcher-create.md) pour créer un Network Watcher.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article utilise Tronçon suivant, une fonctionnalité de Network Watcher qui détecte le type de tronçon suivant et l’adresse IP d’une ressource. Pour en savoir plus sur Tronçon suivant, consultez [Next Hop Overview (Vue d’ensemble de la fonctionnalité Tronçon suivant)](network-watcher-next-hop-overview.md).

Dans ce scénario, vous allez :

* Récupérer le tronçon suivant pour une machine virtuelle.

## <a name="log-in-with-armclient"></a>Vous connecter à ARMClient

Vous connecter à armclient avec vos informations d’identification Azure.

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Récupérer une machine virtuelle

Exécutez le script suivant pour renvoyer une machine virtuelle. Ces informations sont nécessaires pour l’exécution de la fonctionnalité Tronçon suivant.

Les codes suivants ont besoin de valeurs pour les variables suivantes :

- **subscriptionId** : l’ID d’abonnement à utiliser.
- **resourceGroupName** : le nom d’un groupe de ressources qui contient les machines virtuelles.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

À partir de la sortie suivante, l’ID de la machine virtuelle est utilisé dans l’exemple suivant :

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="get-next-hop"></a>Obtenir le tronçon suivant

Une fois que l’en-tête d’autorisation est créé, le tronçon suivant d’une machine virtuelle peut être récupéré. Les valeurs suivantes doivent être remplacées pour que l’exemple de code fonctionne.

> [!Important]
> Pour les appels de l’API REST de Network Watcher, le nom du groupe de ressources dans l’URI de requête correspond au groupe de ressources qui contient Network Watcher, mais pas les ressources sur lesquelles vous exécutez les actions de diagnostic.

```powershell
$sourceIP = "10.0.0.4"
$destinationIP = "8.8.8.8"
$resourceGroupName = <resource group name>
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'sourceIpAddress': '${sourceIP}',
    'destinationIpAddress': '${destinationIP}',
    'targetNicResourceId' : '${targetNic}'
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/nextHop?api-version=2016-12-01" $requestBody
```

> [!NOTE]
> L’exécution de la fonctionnalité Tronçon suivant nécessite l’allocation de la ressource de machine virtuelle.

## <a name="results"></a>Résultats

L’extrait de code suivant est un exemple de sortie reçue. Les résultats contiennent les valeurs suivantes :

* **nextHopType** : cette valeur est l’une des valeurs suivantes : Internet, VirtualAppliance, VirtualNetworkGateway, VnetLocal, HyperNetGateway ou None.
* **nextHopIpAddress** : l’adresse IP du tronçon suivant.
* **routeTableId** : la valeur est un URI pour la table de routage associée à l’itinéraire ou, aucun itinéraire défini par l’utilisateur n’est défini, la valeur d’*itinéraire système* est renvoyée.

Voici les résultats au format json.

```json
{
  "nextHopType": "Internet",
  "routeTableId": "System Route"
}
```

## <a name="next-steps"></a>Étapes suivantes

Lorsque vous avez trouvé le tronçon suivant pour une machine virtuelle, vous pouvez afficher la sécurité de vos ressources réseau en vous rendant dans la [vue d’ensemble de la vue de sécurité](network-watcher-security-group-view-overview.md)














