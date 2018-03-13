---
title: "Mettre à l’échelle un cluster Azure Service Fabric | Microsoft Docs"
description: "Dans ce didacticiel, vous découvrez comment effectuer une mise à l’échelle rapide d’un cluster Service Fabric."
services: service-fabric
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/06/2018
ms.author: adegeo
ms.custom: mvc
ms.openlocfilehash: 21ef6d1981464d5943338c20fb06a218fc0e447d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="tutorial-scale-a-service-fabric-cluster"></a>Didacticiel : mettre à l’échelle un cluster Service Fabric

Ce didacticiel constitue la deuxième partie d’une série et montre comment diminuer ou augmenter la taille de votre cluster existant. À la fin de ce didacticiel, vous saurez comment mettre à l’échelle votre cluster et comment nettoyer les ressources restantes.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Lire le nombre de nœuds de cluster
> * Ajouter des nœuds de cluster (scale out)
> * Supprimer des nœuds de cluster (scale in)

Cette série de didacticiels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * créer un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) sécurisé sur Azure à l’aide d’un modèle ;
> * Mettre à l’échelle un cluster
> * [Mettre à niveau le runtime d’un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [déployer la Gestion des API avec Service Fabric](service-fabric-tutorial-deploy-api-management.md).

## <a name="prerequisites"></a>Prérequis
Avant de commencer ce didacticiel :
- Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Installez le [module Azure PowerShell version 4.1 ou ultérieure](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [Azure CLI 2.0](/cli/azure/install-azure-cli).
- Créer un [cluster Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou un [cluster Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) sécurisé sur Azure
- Si vous déployez un cluster Windows, configurez un environnement de développement Windows. Installez les charges de travail [Visual Studio 2017](http://www.visualstudio.com) et le **développement Azure**, **ASP.NET et le développement web**, ainsi que **Développement multiplateforme .NET Core**.  Ensuite, configurez un [environnement de développement .NET](service-fabric-get-started.md).
- Si vous déployez un cluster Linux, configurez un environnement de développement Java sur [Linux](service-fabric-get-started-linux.md) ou [MacOS](service-fabric-get-started-mac.md).  Installez l’[interface de ligne de commande (CLI) de Service Fabric](service-fabric-cli.md). 

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous à votre compte Azure, sélectionnez votre abonnement avant d’exécuter des commandes Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="connect-to-the-cluster"></a>Connexion au cluster

Pour effectuer cette partie du didacticiel, vous devez vous connecter à la fois au cluster Service Fabric et au groupe de machines virtuelles identiques (qui héberge le cluster). Le groupe de machines virtuelles identiques est la ressource Azure qui héberge Service Fabric dans Azure.

Lorsque vous vous connectez à un cluster, vous pouvez l’interroger pour obtenir des informations. Vous pouvez utiliser le cluster pour savoir quels nœuds sont connus du cluster. Dans le code suivant, la connexion au cluster utilise le certificat qui a été créé dans la première partie de cette série. N’oubliez pas de définir les variables `$endpoint` et `$thumbprint` avec vos valeurs.

```powershell
$endpoint = "<mycluster>.southcentralus.cloudapp.azure.com:19000"
$thumbprint = "63EB5BA4BC2A3BADC42CA6F93D6F45E5AD98A1E4"

Connect-ServiceFabricCluster -ConnectionEndpoint $endpoint `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint $thumbprint `
          -FindType FindByThumbprint -FindValue $thumbprint `
          -StoreLocation CurrentUser -StoreName My

Get-ServiceFabricClusterHealth
```

```azurecli
sfctl cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 \
--pem ./aztestcluster201709151446.pem --no-verify
```

Maintenant que vous êtes connecté, vous pouvez utiliser une commande pour obtenir l’état de chaque nœud du cluster. Pour PowerShell, utilisez la commande `Get-ServiceFabricClusterHealth` tandis que pour **sfctl**, utilisez la commande `sfctl cluster select`.

## <a name="scale-out"></a>Montée en charge

Lorsque vous augmentez le nombre d’instances (scale out), vous ajoutez des instances de machines virtuelles au groupe identique. Ces instances deviennent les nœuds que Service Fabric utilise. Service Fabric sait quand des instances sont ajoutées au groupe identique et réagit automatiquement. Le code suivant obtient un groupe identique par son nom et augmente de 1 sa **capacité**.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzureRmVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Ce code définit la capacité avec la valeur 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="scale-in"></a>Diminuer le nombre d’instances

Pour diminuer le nombre d’instances (scale in), il suffit de diminuer la valeur de la **capacité**. Lorsque vous diminuez le nombre d’instances (scale in) du groupe identique, vous supprimez des instances de machines virtuelles du groupe. Normalement, Service Fabric ne sait pas ce qui s’est produit et pense qu’un nœud est manquant. Service Fabric signale donc que l’état du cluster n’est pas sain. Pour éviter cela, vous devez informer Service Fabric que l’absence du nœud est normale.

### <a name="remove-the-service-fabric-node"></a>Supprimer le nœud Service Fabric

> [!NOTE]
> Cette partie s’adresse uniquement au niveau de durabilité *Bronze*. Pour plus d’informations sur la durabilité, consultez [Planification de la capacité des clusters Service Fabric][durability].

Lorsque vous diminuez le nombre d’instances d’un groupe de machines virtuelles identiques, celui-ci supprime (dans la plupart des cas) l’instance de machine virtuelle qui a été créée en dernier. Par conséquent, vous devez rechercher le nœud Service Fabric correspondant qui a été créé en dernier. Pour le trouver, il vous suffit de regarder la valeur de propriété `NodeInstanceId` la plus élevée parmi les nœuds Service Fabric. Les exemples de code ci-dessous trient les résultats par instance de nœud et retournent les informations sur l’instance dont la valeur ID est la plus élevée. 

```powershell
Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1
```

```azurecli
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Le cluster Service Fabric doit être informé de la suppression de ce nœud. Pour cela, trois étapes sont nécessaires :

1. Désactivez le nœud afin qu’il ne soit plus utilisé pour la réplication de données.  
PowerShell : `Disable-ServiceFabricNode`  
sfcli : `sfctl node disable`

2. Arrêtez le nœud afin que le runtime Service Fabric soit arrêté correctement et que votre application reçoive une demande de fin d’exécution.  
PowerShell : `Start-ServiceFabricNodeTransition -Stop`  
sfcli : `sfctl node transition --node-transition-type Stop`

2. Supprimez le nœud du cluster.  
PowerShell : `Remove-ServiceFabricNodeState`  
sfcli : `sfctl node remove-state`

Une fois ces trois étapes effectuées, vous pouvez supprimer le nœud du groupe identique. Si vous utilisez un niveau de durabilité autre que le niveau [Bronze][durability], ces étapes sont effectuées automatiquement lorsque vous supprimez une instance de groupe identique.

Le bloc de code suivant obtient le dernier nœud créé, le désactive, l’arrête et le supprime du cluster.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300
    
    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }
    
    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

Dans le code **sfctl** ci-dessous, la commande suivante permet d’obtenir le nom du dernier nœud créé (**node-name**) :`sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```azurecli
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Utilisez les requêtes **sfctl** suivantes pour vérifier l’état de chaque étape.
>
> **Vérifier l’état de la désactivation**  
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Vérifier l’état de l’arrêt**  
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>


### <a name="scale-in-the-scale-set"></a>Diminuer le nombre d’instances du groupe identique

Maintenant que le nœud Service Fabric a été supprimé du cluster, vous pouvez diminuer le nombre d’instances du groupe de machines virtuelles identiques. Dans l’exemple ci-dessous, la capacité du groupe identique est diminuée de 1.

```powershell
$scaleset = Get-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzureRmVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Ce code définit la capacité sur la valeur 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```


## <a name="next-steps"></a>étapes suivantes

Dans ce didacticiel, vous avez appris à :

> [!div class="checklist"]
> * Lire le nombre de nœuds de cluster
> * Ajouter des nœuds de cluster (scale out)
> * Supprimer des nœuds de cluster (scale in)


Maintenant, passez au didacticiel suivant pour savoir comment mettre à niveau le runtime d’un cluster.
> [!div class="nextstepaction"]
> [Mettre à niveau le runtime d’un cluster](service-fabric-tutorial-upgrade-cluster.md)

[durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
