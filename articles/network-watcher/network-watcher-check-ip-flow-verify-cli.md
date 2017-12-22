---
title: "Vérifier le trafic avec la vérification des flux IP Azure Network Watcher - Azure CLI | Microsoft Docs"
description: "Cet article explique comment savoir si le trafic en direction ou en provenance d’une machine virtuelle est autorisé ou refusé à l’aide de la ligne de commande Azure"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 3f4a7d3f96a08b3296dd1abfec8abfbcb9759e9f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Vérifiez si le trafic est autorisé ou refusé en direction ou en provenance d’une machine virtuelle avec le composant de vérification des flux IP d’Azure Network Watcher

> [!div class="op_single_selector"]
> - [portail Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST Azure](network-watcher-check-ip-flow-verify-rest.md)


La vérification des flux IP est une fonctionnalité de Network Watcher qui vous permet de vérifier si le trafic en direction ou en provenance d’une machine virtuelle est autorisé. Cette fonctionnalité est très utile pour définir si une machine virtuelle peut actuellement communiquer avec une ressource externe ou un serveur back-end. La vérification des flux IP peut être utilisée pour vérifier si les règles de votre groupe de sécurité réseau sont correctement configurées et pour résoudre les problèmes de flux bloqués par les règles de groupe de sécurité réseau. La vérification des flux IP permet aussi de s’assurer que le trafic que vous souhaitez bloquer est correctement bloqué par le groupe de sécurité réseau.

Dans cet article, notre CLI nouvelle génération, Azure CLI 2.0, est utilisée pour le modèle de déploiement de gestion des ressources. Celle-ci est disponible pour Windows, Mac et Linux.

Pour exécuter la procédure indiquée dans cet article, vous devez [installer l’interface de ligne de commande Azure pour Mac, Linux et Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Avant de commencer

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Créer une instance d’Azure Network Watcher](network-watcher-create.md) pour créer un Network Watcher ou que vous disposez d’une instance existante de Network Watcher. Ce scénario suppose également qu’un groupe de ressources avec une machine virtuelle valide existe et peut être utilisé.

## <a name="scenario"></a>Scénario

Ce scénario utilise la vérification des flux IP pour savoir si une machine virtuelle peut communiquer avec une adresse IP Bing connue. Si le trafic est refusé, la règle de sécurité refusant ce trafic est renvoyée. Pour plus d’informations sur la vérification des flux IP, consultez la page [IP Flow Verify Overview (Vue d’ensemble de la fonction de vérification des flux IP)](network-watcher-ip-flow-verify-overview.md)

## <a name="get-a-vm"></a>Obtenir une machine virtuelle

La vérification des flux IP permet de tester le trafic en direction ou en provenance d’une adresse IP sur une machine virtuelle en direction ou en provenance d’une destination distante. Un identifiant de machine virtuelle est requis pour l’applet de commande. Si vous connaissez déjà l’identifiant de la machine virtuelle à utiliser, vous pouvez ignorer cette étape.

```azurecli
az vm show --resource-group MyResourceGroup5431 --name MyVM-Web
```

## <a name="get-the-nics"></a>Obtenir les cartes réseau

L’adresse IP d’une carte réseau sur la machine virtuelle est requise. Dans cet exemple, nous récupérons les cartes réseau sur une machine virtuelle. Si vous connaissez déjà l’adresse IP que vous souhaitez tester sur la machine virtuelle, vous pouvez ignorer cette étape.

```azurecli
az network nic show --resource-group MyResourceGroup5431 --name MyNic-Web
```

## <a name="run-ip-flow-verify"></a>Exécuter la vérification des flux IP

Maintenant que nous disposons des informations nécessaires pour exécuter l’applet de commande, nous pouvons exécuter l’applet de commande `az network watcher test-ip-flow` pour tester le trafic. Dans cet exemple, nous utilisons la première adresse IP sur la première carte réseau.

```azurecli
az network watcher test-ip-flow --resource-group resourceGroupName --direction directionInboundorOutbound --protocol protocolTCPorUDP --local ipAddressandPort --remote ipAddressandPort --vm vmNameorID --nic nicNameorID
```

> [!NOTE]
> La vérification des flux IP nécessite l’attribution de la ressource de machine virtuelle.

## <a name="review-results"></a>Analyser les résultats

Après l’exécution de `az network watcher test-ip-flow`, les résultats sont renvoyés. L’exemple suivant présente les résultats renvoyés à partir de l’étape précédente.

```azurecli
{
    "access": "Allow",
    "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

## <a name="next-steps"></a>Étapes suivantes

Si le trafic est bloqué alors qu’il ne devrait pas l’être, consultez [Gérer les groupes de sécurité réseau à partir du portail](../virtual-network/virtual-network-manage-nsg-arm-portal.md) afin de surveiller le groupe de sécurité réseau et les règles de sécurité définis.

Pour savoir comment auditer les paramètres de votre groupe de sécurité réseau, consultez [Auditing Network Security Groups (NSG) with Network Watcher (Audit des groupes de sécurité réseau avec Network Watcher)](network-watcher-nsg-auditing-powershell.md).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
