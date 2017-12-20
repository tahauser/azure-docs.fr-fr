---
title: "Créer un équilibrage de charge interne à l’aide de PowerShell classique | Microsoft Docs"
description: "Découvrez comment créer un équilibreur de charge interne à l’aide de PowerShell dans le modèle de déploiement classique"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 3be93168-3787-45a5-a194-9124fe386493
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: 85fd757bdd6dd9906a23977b760f01bb1f85bf77
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Prise en main de la création d’un équilibreur de charge interne (classique) à l’aide de PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [interface de ligne de commande Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Services cloud](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../azure-resource-manager/resource-manager-deployment-model.md).  Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Créer un jeu d’équilibrage de charge interne pour les machines virtuelles

Pour créer un jeu d’équilibrage de charge interne et les serveurs qui y enverront leur trafic, vous devez procéder comme suit :

1. Créez une instance d’équilibrage de charge qui sera le point de terminaison du trafic entrant qui devra être équilibré entre les serveurs d’un jeu d’équilibrage de charge.
2. Ajoutez des points de terminaison correspondants aux machines virtuelles qui recevront le trafic entrant.
3. Configurez les serveurs qui enverront le trafic avec une charge équilibrée pour envoyer leur trafic à l’adresse IP virtuelle (VIP) de l’instance d’équilibrage de charge interne.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>Étape 1 : Créer une instance d’équilibrage de charge interne

Pour un service cloud existant ou un service cloud déployé dans un réseau virtuel régional, vous pouvez créer une instance d’équilibrage de charge interne avec les commandes Windows PowerShell suivantes :

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Notez que l’utilisation de cette cmdlet [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell utilise le jeu de paramètres DefaultProbe. Pour plus d'informations sur les jeux de paramètres supplémentaires, consultez [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Étape 2 : Ajouter des points de terminaison à l’instance d’équilibrage de charge interne

Voici un exemple :

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Étape 3 : Configurer vos serveurs pour envoyer leur trafic vers le nouveau point de terminaison d’équilibrage de charge interne

Vous devez configurer les serveurs dont la charge du trafic sera équilibrée pour utiliser la nouvelle adresse IP virtuelle de l’instance d’équilibrage de charge interne. Il s’agit de l’adresse sur laquelle l’instance d’équilibrage de charge interne est en train d’écouter. Dans la plupart des cas, il vous suffit d’ajouter ou de modifier un enregistrement DNS pour l’adresse IP virtuelle de l’instance d’équilibrage de charge interne.

Si vous avez spécifié l’adresse IP lors de la création de l’instance d’équilibrage de charge interne, vous avez déjà l’adresse IP virtuelle. Autrement, vous pouvez afficher l'adresse IP virtuelle à partir des commandes suivantes :

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Pour utiliser ces commandes, renseignez les valeurs et supprimez < et >. Voici un exemple :

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

À partir de l'affichage de la commande Get-AzureInternalLoadBalancer, notez l'adresse IP et apportez les modifications nécessaires à vos serveurs ou à vos enregistrements DNS pour vous assurer que le trafic est envoyé à l'adresse IP virtuelle.

> [!NOTE]
> La plateforme Microsoft Azure utilise une adresse IPv4 statique routable publiquement pour divers scénarios d’administration. L’adresse IP est 168.63.129.16. Cette adresse IP ne doit pas être bloquée par les pare-feu, car cela peut entraîner un comportement inattendu.
> En ce qui concerne l’équilibrage de charge Azure, cette adresse IP est utilisée par les sondes de l’équilibreur de charge, pour déterminer l’état de santé pour les machines virtuelles dans un jeu d’équilibrage de charge interne. Si un groupe de sécurité réseau est utilisé pour limiter le trafic vers les machines virtuelles Azure dans un jeu d’équilibrage de charge interne, ou est appliqué à un sous-réseau de réseau virtuel, vérifiez qu’une règle de sécurité de réseau est ajoutée pour autoriser le trafic à partir de 168.63.129.16.

## <a name="example-of-internal-load-balancing"></a>Exemple d’équilibrage de charge interne

Pour vous guider dans le processus de bout en bout de la création d'un jeu d'équilibrage de charge pour deux exemples de configuration, consultez les sections suivantes.

### <a name="an-internet-facing-multi-tier-application"></a>Une application multi-niveau sur Internet

Vous souhaitez fournir un service de base de données à charge équilibrée pour un ensemble de serveurs web sur Internet. Les deux ensembles de serveurs sont hébergés dans un seul service cloud Azure. Le trafic du serveur web vers le port TCP 1433 doit être réparti entre deux machines virtuelles au niveau de la base de données. La Figure 1 montre la configuration.

![Jeu d'équilibrage de charge interne pour le niveau base de données](./media/load-balancer-internal-getstarted/IC736321.png)

La configuration se compose des éléments suivants :

* Le service cloud existant qui héberge les machines virtuelles s’appelle mytestcloud.
* Les deux serveurs de base de données existants sont nommés DB1 et DB2.
* Les serveurs web au niveau du web se connectent aux serveurs de base de données au niveau de la base de données à l’aide d’une adresse IP privée. Une autre option consiste à utiliser votre propre serveur DNS pour le réseau virtuel et à inscrire manuellement un enregistrement A pour le jeu d’équilibrage de charge interne.

Les commandes suivantes configurent une nouvelle instance d’équilibrage de charge interne appelée **ILBset** et ajoutent des points de terminaison aux machines virtuelles correspondant aux deux serveurs de base de données :

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Supprimer une configuration d’équilibrage de charge interne

Pour supprimer une machine virtuelle en tant que point de terminaison d’une instance d’équilibreur de charge interne, utilisez les commandes suivantes :

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Pour utiliser ces commandes, renseignez les valeurs et supprimez < et >.

Voici un exemple :

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Pour supprimer une instance d’équilibreur de charge interne depuis un service cloud, utilisez les commandes suivantes :

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

Pour utiliser ces commandes, renseignez les valeurs et supprimez < et >.

Voici un exemple :

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Informations supplémentaires sur les applets de commande de l’équilibreur de charge interne

Pour obtenir plus d’informations sur les applets de commande d’équilibrage de charge interne, exécutez les commandes suivantes à l’invite Azure Windows PowerShell :

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Étapes suivantes

[Configurer le mode de distribution d’équilibreur de charge à l’aide de l’affinité d’IP source](load-balancer-distribution-mode.md)

[Configuration des paramètres de délai d’expiration TCP inactif pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)

