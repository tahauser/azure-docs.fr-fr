---
title: "Types de nœuds Azure Service Fabric et groupes de machines virtuelles identiques | Microsoft Docs"
description: "Découvrez la relation entre les types de nœuds Azure Service Fabric et les groupes de machines virtuelles identiques, ainsi que la méthode permettant de se connecter à distance à une instance de groupe identique ou à un nœud de cluster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/5/2018
ms.author: chackdan
ms.openlocfilehash: 720bb83c9d8540549852ce78ee1709f8c8717348
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-service-fabric-node-types-and-virtual-machine-scale-sets"></a>Types de nœuds Azure Service Fabric et groupes de machines virtuelles identiques
Les groupes de machines virtuelles identiques représentent une ressource de calcul Azure. Vous pouvez utiliser des groupes identiques pour déployer et gérer une collection de machines virtuelles comme un groupe. Configurez un groupe identique distinct pour chaque type de nœud que vous définissez dans un cluster Azure Service Fabric. Vous pouvez faire monter ou descendre en puissance chaque type de nœud de manière indépendante, avoir différents ensembles de ports ouverts et utiliser différentes métriques de capacité.

La figure suivante illustre un cluster constitué de deux types de nœuds nommés FrontEnd et BackEnd. Chaque type de nœud compte cinq nœuds.

![Cluster constitué de deux types de nœuds][NodeTypes]

## <a name="map-virtual-machine-scale-set-instances-to-nodes"></a>Mapper des instances de groupe de machines virtuelles identiques à des nœuds
Comme l’illustre la figure précédente, les instances de groupe identique commencent à l’instance 0 et augmentent de 1. Les noms de nœuds reflètent la numérotation. Par exemple, le nœud BackEnd_0 représente l’instance 0 du groupe identique BackEnd. Ce groupe identique spécifique compte cinq instances nommées BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 et BackEnd_4.

Quand vous faites monter en puissance un groupe identique, une nouvelle instance est créée. En règle générale, le nom de la nouvelle instance du groupe identique est le nom du groupe identique suivi du numéro d’instance suivant. Dans notre exemple, il s’agit de BackEnd_5.

## <a name="map-scale-set-load-balancers-to-node-types-and-scale-sets"></a>Mapper des équilibreurs de charge de groupe identique à des types de nœuds et des groupes identiques
Si vous avez déployé votre cluster dans le portail Azure ou si vous avez utilisé l’exemple de modèle Azure Resource Manager, toutes les ressources d’un groupe de ressources sont répertoriées. Vous pouvez voir les équilibreurs de charge de chaque groupe identique ou type de nœud. Le nom d’un équilibreur de charge utilise le format suivant : **LB-&lt;nom du type de nœud&gt;**. La figure suivante en propose un exemple (LB-sfcluster4doc-0) :

![Ressources][Resources]
## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Connexion distante à une instance de groupe de machines virtuelles identiques ou à un nœud de cluster
Configurez un groupe identique distinct pour chaque type de nœud que vous avez défini dans un cluster. Vous pouvez faire monter ou descendre en puissance les types de nœud de manière indépendante. Vous pouvez aussi utiliser différentes références (SKU) de machines virtuelles. Contrairement aux machines virtuelles à une seule instance, les instances de groupe identique ne possèdent pas leurs propres adresses IP virtuelles. Cela peut poser des problèmes quand il s’agit de rechercher une adresse IP et un port permettant de se connecter à distance à une instance spécifique.

Pour rechercher une adresse IP et un port permettant de se connecter à distance à une instance spécifique, effectuez les étapes suivantes.

**Étape 1** : recherchez l’adresse IP virtuelle du type de nœud en obtenant les règles NAT de trafic entrant pour le protocole RDP (Remote Desktop Protocol).

Dans un premier temps, obtenez les valeurs des règles NAT de trafic entrant qui ont été définies pendant la définition des ressources de `Microsoft.Network/loadBalancers`.

Dans le portail Azure, dans la page de l’équilibreur de charge, sélectionnez **Paramètres** > **Règles NAT de trafic entrant**. Cela vous donne l’adresse IP et le port qui vous permettent de vous connecter à distance à la première instance de groupe identique. 

![Équilibrage de charge][LBBlade]

Dans la figure suivante, l’adresse IP et le port sont **104.42.106.156** et **3389**.

![Règles NAT][NATRules]

**Étape 2** : recherchez le port vous permettant de vous connecter à distance à un nœud ou à une instance de groupe identique spécifique.

Les instances de groupe identique se mappent aux nœuds. Utilisez les informations de groupe identique pour déterminer exactement quel port utiliser.

Les ports sont alloués dans un ordre croissant qui correspond à l’instance de groupe identique. Pour l’exemple précédent du type de nœud FrontEnd, le tableau suivant répertorie les ports de chacune des cinq instances de nœud. Appliquez le même mappage à votre instance de groupe identique.

| **Instance de groupe de machines virtuelles identiques** | **Port** |
| --- | --- |
| FrontEnd_0 |3389 |
| FrontEnd_1 |3390 |
| FrontEnd_2 |3391 |
| FrontEnd_3 |3392 |
| FrontEnd_4 |3393 |
| FrontEnd_5 |3394 |

**Étape 3** : connectez-vous à distance à l’instance de groupe identique spécifique.

Dans la figure suivante, le service Connexion Bureau à distance est utilisé pour se connecter à l’instance de groupe identique FrontEnd_1 :

![Connexion Bureau à distance][RDP]

## <a name="change-the-rdp-port-range-values"></a>Modifier les valeurs de la plage de ports RDP

### <a name="before-cluster-deployment"></a>Avant le déploiement du cluster
Quand vous configurez le cluster à l’aide d’un modèle Resource Manager, spécifiez la plage dans `inboundNatPools`.

Accédez à la définition des ressources de `Microsoft.Network/loadBalancers`. Recherchez la description de `inboundNatPools`.  Remplacez les valeurs de `frontendPortRangeStart` et `frontendPortRangeEnd`.

![Valeurs de inboundNatPools][InboundNatPools]

### <a name="after-cluster-deployment"></a>Après le déploiement du cluster
Modifier les valeurs de la plage de ports RDP après avoir déployé le cluster s’avère plus complexe. Pour éviter de recycler les machines virtuelles, définissez de nouvelles valeurs à l’aide d’Azure PowerShell. 

> [!NOTE]
> Vérifiez qu’Azure PowerShell version 1.0 ou ultérieure est installé sur votre ordinateur. Si vous ne disposez pas d’Azure Powershell version 1.0 ou ultérieure, nous vous recommandons de suivre les étapes décrites dans [Installer et configurer Azure PowerShell](/powershell/azure/overview).

1. Connectez-vous à votre compte Azure. En cas d’échec de la commande PowerShell suivante, vérifiez que vous avez installé PowerShell correctement.

    ```
    Login-AzureRmAccount
    ```

2. Pour obtenir des détails sur votre équilibreur de charge et voir les valeurs qui décrivent `inboundNatPools`, exécutez le code suivant :

    ```
    Get-AzureRmResource -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
    ```

3. Attribuez à `frontendPortRangeEnd` et `frontendPortRangeStart` les valeurs de votre choix.

    ```
    $PropertiesObject = @{
        #Property = value;
    }
    Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <resource group name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name> -ApiVersion <use the API version that is returned> -Force
    ```

## <a name="change-the-rdp-user-name-and-password-for-nodes"></a>Changer le nom d’utilisateur et le mot de passe RDP des nœuds

Pour changer le mot de passe de tous les nœuds d’un type de nœud donné, effectuez les étapes suivantes. Ces modifications s’appliquent à tous les nœuds actuels et futurs du groupe identique.

1. Ouvrez PowerShell ISE en tant qu’administrateur. 
2. Pour vous connecter et sélectionner votre abonnement pour la session, exécutez les commandes suivantes. Remplacez le paramètre `SUBSCRIPTIONID` par votre ID d’abonnement. 

    ```powershell
    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionId 'SUBSCRIPTIONID' | Select-AzureRmSubscription
    ```

3. Exécutez le script suivant. Utilisez les valeurs de `NODETYPENAME`, `RESOURCEGROUP`, `USERNAME` et `PASSWORD` appropriées. Les valeurs de `USERNAME` et `PASSWORD` sont les nouvelles informations d’identification que vous utiliserez dans les futures sessions RDP. 

    ```powershell
    $nodeTypeName = 'NODETYPENAME'
    $resourceGroup = 'RESOURCEGROUP'
    $publicConfig = @{'UserName' = 'USERNAME'}
    $privateConfig = @{'Password' = 'PASSWORD'}
    $extName = 'VMAccessAgent'
    $publisher = 'Microsoft.Compute'
    $node = Get-AzureRmVmss -ResourceGroupName $resourceGroup -VMScaleSetName $nodeTypeName
    $node = Add-AzureRmVmssExtension -VirtualMachineScaleSet $node -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion '2.0' -AutoUpgradeMinorVersion $true

    Update-AzureRmVmss -ResourceGroupName $resourceGroup -Name $nodeTypeName -VirtualMachineScaleSet $node
    ```

## <a name="next-steps"></a>Étapes suivantes
* Consultez [Vue d’ensemble de la fonction « Déployer n’importe où » et comparaison avec les clusters gérés par Azure](service-fabric-deploy-anywhere.md).
* Découvrez plus en détail la [sécurité des clusters](service-fabric-cluster-security.md).
* En savoir plus sur le [Kit de développement logiciel (SDK) de Service Fabric et la mise en route](service-fabric-get-started.md).

<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
