---
title: "Gérer le service Protection DDos Standard Azure à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Découvrez comment gérer le service Protection DDos Standard Azure à l’aide d’Azure PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 33ff6cfcacd1632dc49b448e70361e1cb2ce1176
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Gérer service Protection DDos Standard Azure à l’aide d’Azure PowerShell

Découvrez comment activer et désactiver la protection contre les attaques par déni de service distribué (DDoS), et utiliser la télémétrie pour atténuer ce type d’attaque, grâce au service Protection DDos Standard Azure. Ce service protège les ressources Azure, telles que les machines virtuelles, les équilibreurs de charge et les passerelles d’application auxquels une [adresse IP publique](virtual-network-public-ip-address.md) a été affectée. Pour en savoir plus sur le service Protection DDos Standard et ses fonctionnalités, voir [Vue d’ensemble du service Protection DDos Standard Azure](ddos-protection-overview.md). 

>[!IMPORTANT]
>Le service Protection DDos Standard Azure (Protection DDos) est disponible en préversion. Un nombre limité de ressources Azure prend en charge le service Protection DDos et ce, dans un nombre limité de régions. Pour obtenir la liste des régions disponibles, voir [Vue d’ensemble du service Protection DDos Standard](ddos-protection-overview.md). Vous devez [vous inscrire au service](http://aka.ms/ddosprotection) pendant la préversion limitée afin que le service Protection DDos soit activé pour votre abonnement. L’équipe Azure DDoS vous contacte après l’inscription, pour vous guider tout au long du processus d’activation.


## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. Si vous devez installer ou mettre à niveau Azure PowerShell, consultez la section relative à [l’installation du module Azure PowerShell](/powershell/azure/install-azurerm-ps).

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Activer le service Protection DDos Standard : nouveau réseau virtuel

Pour créer un réseau virtuel doté du service Protection DDos, exécutez l’exemple suivant :

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

Cet exemple crée un réseau virtuel avec deux sous-réseaux et deux serveurs DNS. Quand vous spécifiez des serveurs DNS sur le réseau virtuel, les cartes réseau/machines virtuelles qui sont déployées sur ce réseau virtuel héritent de ces serveurs DNS en tant que serveurs par défaut. Le service Protection DDos est activé pour toutes les ressources protégées dans le réseau virtuel.

> [!WARNING]
> Lorsque vous sélectionnez une région, choisissez-en une qui soit prise en charge dans la liste de la section [Vue d’ensemble du service Protection DDos Standard Azure](ddos-protection-overview.md).

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Activer le service Protection DDos sur un réseau virtuel existant

Pour activer le service Protection DDos sur un réseau virtuel existant, exécutez l’exemple suivant :

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> Le réseau virtuel doit exister dans une région prise en charge. Pour obtenir la liste des régions prises en charge, voir [Vue d’ensemble du service Protection DDos Standard Azure](ddos-protection-overview.md).

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Désactiver le service Protection DDos sur un réseau virtuel

Pour désactiver le service Protection DDos sur un réseau virtuel, exécutez l’exemple suivant :

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>Passer en revue l’état de protection DDoS d’un réseau virtuel 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Utiliser les données de télémétrie du service Protection DDos

Les données de télémétrie pour une attaque sont fournies par le biais d’Azure Monitor en temps réel. Les données de télémétrie ne sont disponibles que pendant l’atténuation dont fait l’objet une adresse IP publique. Vous ne les voyez pas avant ou après l’atténuation d’une attaque.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurer des alertes pour les métriques du service Protection DDos

En tirant parti de la configuration des alertes Azure Monitor, vous pouvez sélectionner les métriques du service Protection DDos disponibles de votre choix pour être averti quand il existe une atténuation active pendant une attaque.

#### <a name="configure-email-alert-rules-via-azure"></a>Configurer des règles d’alerte par e-mail via Azure

1. Récupérez la liste des abonnements dont vous disposez. Vérifiez que vous travaillez avec le bon abonnement. Dans le cas contraire, choisissez le bon à l’aide de la sortie de Get-AzureRmSubscription. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. Pour répertorier les règles existantes sur un groupe de ressources, utilisez la commande suivante : 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. Pour créer une règle, vous devez disposer des informations suivantes : 

    - L’ID de la ressource pour laquelle vous souhaitez définir une alerte
    - Les définitions de métriques disponibles pour cette ressource Pour obtenir l’ID de la ressource, vous pouvez utiliser le Portail Azure. À supposer que la ressource soit déjà créée, sélectionnez-la dans le portail Azure. Puis, dans la page suivante, sélectionnez *Propriétés* sous la section *Paramètres*. **L’ID DE RESSOURCE** est un champ de la page suivante. Une autre méthode consiste à utiliser [l’Explorateur de ressources Azure](https://resources.azure.com/). Voici un exemple d’ID de ressource d’une adresse IP publique : `/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    L’exemple suivant crée une alerte pour une adresse IP publique associée à une ressource dans un réseau virtuel. La métrique sur laquelle créer une alerte est **Sous attaque DDoS ou non**. Il s’agit d’une valeur booléenne 1 ou 0. **1** signifie que vous faites l’objet d’une attaque. **0** signifie que vous ne faites pas l’objet d’une attaque. L’alerte est créée au démarrage d’une attaque au cours des 5 dernières minutes.

    Pour créer un webhook ou envoyer un e-mail quand une alerte est créée, commencez par créer l’e-mail et/ou le webhook. Après avoir créé le message ou le webhook, créez immédiatement la règle avec la balise `-Actions`, comme indiqué dans l’exemple suivant. Vous ne pouvez pas associer un webhook ou un e-mail à des règles déjà créées à l’aide de PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. Vérifiez que vos alertes ont été correctement créées en examinant la règle :

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

Vous pouvez également découvrir plus en détail la [configuration de webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) et les [applications logiques](../logic-apps/logic-apps-overview.md) pour créer des alertes.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurer la journalisation des métriques du service Protection DDos

Reportez-vous aux [exemples de démarrage rapide PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pour savoir comment accéder à la journalisation des diagnostics Azure et la configurer via PowerShell.

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les journaux de diagnostic Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analyser les journaux du stockage Azure avec Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Prise en main des hubs d’événements](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)