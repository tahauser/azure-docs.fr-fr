---
title: "Configurer des ports de haute disponibilité pour Azure Load Balancer | Microsoft Docs"
description: "Découvrir comment utiliser les ports haute disponibilité pour équilibrer la charge du trafic interne entre tous les ports"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/02/2017
ms.author: kumud
ms.openlocfilehash: 36bc3d7a35f41384706cbc7101457d00848639b2
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="configure-high-availability-ports-for-an-internal-load-balancer"></a>Configurer des ports haute disponibilité pour un équilibreur de charge interne

Cet article montre un exemple de déploiement de ports haute disponibilité sur un équilibreur de charge interne. Pour plus d’informations sur les configurations spécifiques aux appliances virtuelles réseau, consultez les sites web des fournisseurs correspondants.

>[!NOTE]
> Les ports haute disponibilité sont actuellement en préversion. Les niveaux de disponibilité et de fiabilité des fonctionnalités de la préversion peuvent différer de ceux de la version publique. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L’illustration montre la configuration suivante de l’exemple de déploiement décrit dans cet article :

- Les appliances virtuelles réseau sont déployées dans le pool backend d’un équilibreur de charge interne, derrière la configuration des ports haute disponibilité. 
- L’itinéraire défini par l’utilisateur qui est appliqué au sous-réseau DMZ achemine tout le trafic vers les appliances virtuelles réseau en faisant du tronçon suivant l’adresse IP virtuelle de l’équilibreur de charge interne. 
- L’équilibreur de charge interne distribue le trafic vers l’une des appliances virtuelles réseau actives en fonction de l’algorithme de l’équilibreur de charge.
- L’appliance virtuelle réseau traite le trafic et le transfère à la destination d’origine sur le sous-réseau backend.
- Le chemin de retour peut emprunter le même itinéraire si un itinéraire défini par l’utilisateur correspondant est configuré sur le sous-réseau backend. 

![Exemple de déploiement de ports haute disponibilité](./media/load-balancer-configure-ha-ports/haports.png)


## <a name="preview-sign-up"></a>S’inscrire à la préversion

Pour découvrir la préversion des ports haute disponibilité dans Standard Azure Load Balancer, inscrivez votre abonnement et bénéficiez d’un accès par le biais d’Azure CLI 2.0 ou de PowerShell. Inscrivez votre abonnement à la [préversion de Standard Load Balancer](https://aka.ms/lbpreview#preview-sign-up).

>[!NOTE]
>L’inscription à la préversion de Standard Load Balancer peut prendre jusqu’à une heure.

## <a name="configure-high-availability-ports"></a>Configurer les ports haute disponibilité

Pour configurer les ports haute disponibilité, définissez un équilibreur de charge interne avec les appliances virtuelles réseau dans le pool backend. Définissez une configuration de sonde d’intégrité d’équilibreur de charge correspondante pour détecter l’intégrité des appliances virtuelles réseau et la règle d’équilibreur de charge avec les ports haute disponibilité. La configuration générale de l’équilibreur de charge est abordée dans [Bien démarrer](load-balancer-get-started-ilb-arm-portal.md). Cet article explique la configuration des ports haute disponibilité.

La configuration consiste essentiellement à définir le port frontend et le port backend avec la valeur **0**. Affectez **Tout** à la valeur de protocole. Cet article explique comment configurer des ports haute disponibilité à l’aide du portail Azure, de PowerShell et d’Azure CLI 2.0.

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-the-azure-portal"></a>Configurer une règle d’équilibreur de charge des ports haute disponibilité avec le portail Azure

Pour configurer des ports haute disponibilité à l’aide du portail Azure, cochez la case **Ports HA**. Lorsqu’elle est cochée, la configuration correspondante du protocole et des ports est automatiquement remplie. 

![Configuration des ports haute disponibilité par le biais du portail Azure](./media/load-balancer-configure-ha-ports/haports-portal.png)


### <a name="configure-a-high-availability-ports-load-balancing-rule-via-the-resource-manager-template"></a>Configurer une règle d’équilibrage de charge des ports haute disponibilité par le biais du modèle Resource Manager

Vous pouvez configurer des ports haute disponibilité à l’aide de la version d’API 2017-08-01 pour Microsoft.Network/loadBalancers dans la ressource Load Balancer. L’extrait de code JSON suivant illustre les modifications apportées à la configuration de l’équilibreur de charge pour les ports haute disponibilité par le biais de l’API REST :

```json
    {
        "apiVersion": "2017-08-01",
        "type": "Microsoft.Network/loadBalancers",
        ...
        "sku":
        {
            "name": "Standard"
        },
        ...
        "properties": {
            "frontendIpConfigurations": [...],
            "backendAddressPools": [...],
            "probes": [...],
            "loadBalancingRules": [
             {
                "properties": {
                    ...
                    "protocol": "All",
                    "frontendPort": 0,
                    "backendPort": 0
                }
             }
            ],
       ...
       }
    }
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-powershell"></a>Configurer une règle d’équilibreur de charge des ports haute disponibilité avec PowerShell

Utilisez la commande suivante pour créer la règle d’équilibreur de charge des ports haute disponibilité au moment de la création de l’équilibreur de charge interne avec PowerShell :

```powershell
lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HAPortsRule" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "All" -FrontendPort 0 -BackendPort 0
```

### <a name="configure-a-high-availability-ports-load-balancer-rule-with-azure-cli-20"></a>Configurer une règle d’équilibreur de charge des ports haute disponibilité avec Azure CLI 2.0

À l’étape 4 de la section [Créer un jeu d’équilibreurs de charge internes](load-balancer-get-started-ilb-arm-cli.md), utilisez la commande suivante pour créer la règle d’équilibreur de charge des ports haute disponibilité :

```azurecli
azure network lb rule create --resource-group contoso-rg --lb-name contoso-ilb --name haportsrule --protocol all --frontend-port 0 --backend-port 0 --frontend-ip-name feilb --backend-address-pool-name beilb
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus en détail les [ports haute disponibilité](load-balancer-ha-ports-overview.md).
