---
title: "Afficher la topologie Azure Network Watcher - Azure CLI 1.0 | Microsoft Docs"
description: "Cet article explique comment utiliser Azure CLI 1.0 pour interroger la topologie de votre réseau."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 5cd279d7-3ab0-4813-aaa4-6a648bf74e7b
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: bf12e1bde56c06e496d29ad27ba3da65cd94629e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="view-network-watcher-topology-with-azure-cli-10"></a>Afficher la topologie Azure Network Watcher avec Azure CLI 1.0

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-topology-powershell.md)
> - [CLI 1.0](network-watcher-topology-cli-nodejs.md)
> - [CLI 2.0](network-watcher-topology-cli.md)
> - [API REST](network-watcher-topology-rest.md)

La fonctionnalité Topologie de Network Watcher fournit une représentation visuelle des ressources réseau dans un abonnement. Dans le portail, cette visualisation vous est présentée automatiquement. Les informations relatives à la vue Topologie du portail peuvent être récupérées par le biais de PowerShell.
Cette fonctionnalité rend les informations de topologie plus polyvalentes, car les données peuvent être utilisées par d’autres outils pour générer la visualisation.

Cet article utilise l’interface Azure CLI 1.0 interplateforme, disponible pour Windows, Mac et Linux. 

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

Dans ce scénario, vous utilisez l’applet de commande `network watcher topology` pour récupérer les informations de topologie. Un article explique aussi comment [récupérer la topologie du réseau avec l’API REST](network-watcher-topology-rest.md).

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article récupère la réponse de la topologie pour un groupe de ressources donné.

## <a name="retrieve-topology"></a>Récupérer la topologie

L’applet de commande `network watcher topology` récupère la topologie pour un groupe de ressources donné. Ajoutez l’argument « --json » pour afficher la sortie au format json

```azurecli
azure network watcher topology -g resourceGroupName -n networkWatcherName -r topologyResourceGroupName --json
```

## <a name="results"></a>Résultats

Les résultats renvoyés affichent une propriété nommée « Resources », qui contient le corps de réponse json pour l’applet de commande `network watcher topology`.  La réponse contient les ressources du groupe de sécurité réseau et leurs associations (autrement dit, Contains et Associated).

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "createdDateTime": "2017-02-17T22:20:59.461Z",
  "lastModified": "2016-12-19T22:23:02.546Z",
  "resources": [
    {
      "name": "testrg-vnet",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet",
      "location": "westcentralus",
      "associations": [
        {
          "name": "default",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
          "associationType": "Contains"
        }
      ]
    },
    {
      "name": "default",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testrg-vnet/subnets/default",
      "location": "westcentralus",
      "associations": []
    },
    {
      "name": "testclient",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testclient",
      "location": "westcentralus",
      "associations": [
        {
          "name": "testNic",
          "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testNic",
          "associationType": "Contains"
        }
      ]
    },
    ...    
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les règles de sécurité appliquées à vos ressources réseau, consultez la page [Security group view overview (Vue d’ensemble de la vue Groupe de sécurité)](network-watcher-security-group-view-overview.md)
