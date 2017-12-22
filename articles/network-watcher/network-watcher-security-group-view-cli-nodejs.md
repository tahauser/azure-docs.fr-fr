---
title: "Analyser la sécurité réseau avec la vue Groupe de sécurité réseau Network Watcher - Azure CLI 1.0 | Microsoft Docs"
description: "Cet article décrit comment utiliser Azure CLI 1.0 pour analyser la sécurité des machines virtuelles par le biais de la vue Groupe de sécurité."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f27157129bea4e47a2e0e6cc1169b9e4887bdd78
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-10"></a>Analyser la sécurité de votre machine virtuelle par le biais de la vue Groupe de sécurité dans Azure CLI 1.0

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [CLI 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [CLI 2.0](network-watcher-security-group-view-cli.md)
> - [API REST](network-watcher-security-group-view-rest.md)

La vue Groupe de sécurité renvoie des règles de sécurité de réseau configurées et efficaces, appliquées à une machine virtuelle. Cette fonctionnalité permet d’auditer et de diagnostiquer les groupes de sécurité réseau ainsi que les règles configurées sur une machine virtuelle afin de garantir l’autorisation ou le refus appropriés du trafic. Dans cet article, nous vous montrons comment récupérer des règles de sécurité configurées et efficaces pour une machine virtuelle à l’aide de l’interface de ligne de commande Azure

Cet article utilise l’interface Azure CLI 1.0 interplateforme, disponible pour Windows, Mac et Linux. Network Watcher utilise actuellement Azure CLI 1.0 pour la prise en charge d’interface CLI.

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Create a Network Watcher (Créer une instance Network Watcher)](network-watcher-create.md) pour créer une instance Network Watcher.

## <a name="scenario"></a>Scénario

Le scénario décrit dans cet article récupère des règles de sécurité configurées et efficaces pour une machine virtuelle donnée.

## <a name="get-a-vm"></a>Obtenir une machine virtuelle

Une machine virtuelle est requise pour l’exécution de l’applet de commande `vm list`. La commande suivante répertorie les machines virtuelles dans un groupe de ressources :

```azurecli
azure vm list -g resourceGroupName
```

Une fois que vous connaissez la machine virtuelle, vous pouvez utiliser l’applet de commande `vm show` pour obtenir son identifiant de ressource :

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Récupérer la vue Groupe de sécurité

L’étape suivante consiste à récupérer le résultat de la vue Groupe de sécurité. L’ajout de l’indicateur « --json » convertit les résultats au format json.

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>Affichage des résultats

L’exemple suivant est une réponse abrégée des résultats renvoyés. Les résultats présentent toutes les règles de sécurité efficaces et appliquées sur la machine virtuelle, réparties en plusieurs groupes : **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** et **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Étapes suivantes

Consultez la page [Auditing Network Security Groups (NSG) with Network Watcher (Audit des groupes de sécurité réseau avec Network Watcher)](network-watcher-nsg-auditing-powershell.md) pour découvrir comment automatiser la validation des groupes de sécurité réseau.

Pour en savoir plus sur les règles de sécurité appliquées à vos ressources réseau, consultez la page [Security group view overview (Vue d’ensemble de la vue Groupe de sécurité)](network-watcher-security-group-view-overview.md)
