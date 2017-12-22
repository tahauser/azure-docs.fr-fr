---
title: Afficher la topologie Azure Network Watcher - API REST | Microsoft Docs
description: "Cet article explique comment utiliser l’API REST pour interroger la topologie de votre réseau."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: de9af643-aea1-4c4c-89c5-21f1bf334c06
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: fefeae4e816994d3ee69d6ac1c1cbe6cf8bbd06e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="view-network-watcher-topology-with-rest-api"></a>Afficher la topologie Azure Network Watcher par le biais de l’API REST

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

Dans ce scénario, vous récupérez les informations de topologie. ARMclient permet d’appeler l’API REST à l’aide de PowerShell. ARMClient est accessible sur le site chocolatey à partir de la page [ARMClient sur Chocolatey](https://chocolatey.org/packages/ARMClient).

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Créer une instance d’Azure Network Watcher](network-watcher-create.md) pour créer un Network Watcher.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article récupère la réponse de la topologie pour un groupe de ressources donné.

## <a name="log-in-with-armclient"></a>Se connecter à ARMClient

Connectez-vous à armclient avec vos informations d’identification Azure.

```PowerShell
armclient login
```

## <a name="retrieve-topology"></a>Récupérer la topologie

L’exemple suivant demande la topologie à partir de l’API REST.  L’exemple est paramétré pour permettre une certaine flexibilité dans la création d’un exemple.  Remplacez toutes les valeurs avec \< \> qui les entoure.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>" # Resource group name to run topology on
$NWresourceGroupName = "<resource group name>" # Network Watcher resource group name
$networkWatcherName = "<network watcher name>"
$requestBody = @"
{
    'targetResourceGroupName': '${resourceGroupName}'
}
"@

armclient POST "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/topology?api-version=2016-07-01" $requestBody
```

La réponse suivante est un exemple de réponse abrégée qui est renvoyée lors de la récupération de la topologie pour un groupe de ressources :

```json
{
  "id": "ecd6c860-9cf5-411a-bdba-512f8df7799f",
  "createdDateTime": "2017-01-18T04:13:07.1974591Z",
  "lastModified": "2017-01-17T22:11:52.3527348Z",
  "resources": [
    {
      "name": "virtualNetwork1",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{virtualNetworkName}",
      "location": "westcentralus",
      "associations": [
        {
          "name": "{subnetName}",
          "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/(virtualNetworkName)/subnets
/{subnetName}",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "webtestnsg-wjplxls65qcto",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}
s65qcto",
      "associationType": "Associated"
    },
    ...
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment visualiser vos journaux de flux NSG avec Power BI en consultant la page [Visualize NSG flows logs with Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) (Visualiser les journaux de flux NSG avec Power BI)

