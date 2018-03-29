---
title: Créer un groupe identique Azure qui utilise les zones de disponibilité (préversion) | Microsoft Docs
description: Découvrir comment créer des groupes identiques de machines virtuelles Azure qui utilisent les zones de disponibilité pour augmenter la redondance contre les pannes
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: iainfou
ms.openlocfilehash: 8b497af8bc7e3060e184dd6a029b23ccb2d2bbfb
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-virtual-machine-scale-set-that-uses-availability-zones-preview"></a>Créer un groupe identique de machines virtuelles qui utilise les zones de disponibilité (préversion)
Pour protéger vos groupes de machines virtuelles identiques contre les défaillances au niveau du centre de données, vous pouvez créer un groupe identique à travers les zones de disponibilité. Les régions Azure qui prennent en charge les zones de disponibilité comportent au minimum trois zones distinctes, chacune avec leurs propres source d’alimentation, réseau et système de refroidissement. Pour plus d'informations, consultez [Vue d’ensemble des zones de disponibilité](../availability-zones/az-overview.md).

[!INCLUDE [availability-zones-preview-statement.md](../../includes/availability-zones-preview-statement.md)]


## <a name="single-zone-and-zone-redundant-scale-sets"></a>Groupes identiques dans une zone unique et redondants dans une zone
Quand vous déployez un groupe de machines virtuelles identiques, vous pouvez utiliser une seule zone de disponibilité dans une région, ou bien plusieurs zones.

Quand vous créez un groupe identique dans une zone unique, vous contrôlez la zone dans laquelle toutes ces instances de machine virtuelle s’exécutent, et le groupe identique est géré et automatiquement mis à l’échelle dans cette zone uniquement. Un groupe identique redondant dans une zone permet de créer un groupe identique unique qui couvre plusieurs zones. Au fur et à mesure que les instances de machine virtuelle sont créées, elles sont, par défaut, uniformément réparties sur les différentes zones. En cas d’interruption dans l’une de ces zones, le groupe identique ne se met pas automatiquement à l’échelle pour augmenter la capacité. Une meilleure pratique serait de configurer des règles de mise à l’échelle automatique en fonction de l’utilisation du processeur ou de la mémoire. Les règles de mise à l’échelle automatique permettraient au groupe identique de réagir en cas de perte d’instances de machine virtuelle dans cette zone en augmentant la taille des instances dans les zones opérationnelles restantes.

Pour utiliser les zones de disponibilité, votre groupe identique doit être créé dans une [région Azure prise en charge](../availability-zones/az-overview.md#regions-that-support-availability-zones). Vous devez également [vous inscrire à la préversion de Zones de disponibilité](http://aka.ms/azenroll). Vous pouvez créer un groupe identique qui utilise des zones de disponibilité avec l’une des méthodes suivantes :

- [Portail Azure](#use-the-azure-portal)
- [Azure CLI 2.0](#use-the-azure-cli-20)
- [Azure PowerShell](#use-azure-powershell)
- [Modèles Microsoft Azure Resource Manager](#use-azure-resource-manager-templates)


## <a name="use-the-azure-portal"></a>Utilisation du portail Azure
Le processus de création d’un groupe identique qui utilise une zone de disponibilité est identique à celui décrit dans [l’article de prise en main](quick-create-portal.md). Veillez à bien [vous inscrire à la préversion de Zones de disponibilité](http://aka.ms/azenroll). Lorsque vous sélectionnez une région Azure prise en charge, vous pouvez créer un groupe identique dans une des zones disponibles, comme indiqué dans l’exemple suivant :

![Créer un groupe identique dans une zone de disponibilité unique](media/virtual-machine-scale-sets-use-availability-zones/create-portal-single-az.png)

Le groupe identique et les ressources prises en charge, notamment l’équilibreur de charge Azure et l’adresse IP publique, sont créés dans la même zone que vous spécifiez.


## <a name="use-the-azure-cli-20"></a>Utiliser Azure CLI 2.0
Le processus de création d’un groupe identique qui utilise une zone de disponibilité est identique à celui décrit dans [l’article de prise en main](quick-create-cli.md). Pour utiliser Zones de disponibilité, vous devez créer votre groupe identique dans une région Azure prise en charge et vous être [inscrit à la préversion de Zones de disponibilité](http://aka.ms/azenroll).

Ajoutez le paramètre `--zones` à la commande [az vmss create](/cli/azure/vmss#az_vmss_create), puis spécifiez la zone à utiliser (par exemple, zone *1*, *2* ou *3*). L’exemple suivant crée un groupe identique dans une zone unique, nommé *myScaleSet* dans la zone *1* :

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones 1
```
Pour obtenir un exemple complet de groupe identique dans une zone unique et de ressources réseau, consultez [cet exemple de script CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh.).

### <a name="zone-redundant-scale-set"></a>Groupe identique redondant dans une zone
Pour créer un groupe identique redondant dans une zone, vous devez utiliser une adresse IP publique et un équilibreur de charge avec une référence SKU *Standard*. Pour assurer une meilleure redondance, la référence SKU *Standard* crée des ressources réseau redondantes dans une zone. Pour plus d’informations, consultez [Présentation de la référence Standard d’Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md). La première fois que vous créez un groupe identique redondant dans une zone ou un équilibreur de charge, vous devez réaliser les étapes suivantes pour inscrire votre compte avec ces fonctionnalités disponibles en préversion.

1. Inscrivez votre compte pour le groupe identique redondant dans une zone et les fonctionnalités réseau à l’aide la commande [az feature register](/cli/azure/feature#az_feature_register) comme suit :

    ```azurecli
    az feature register --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. L’inscription des fonctionnalités peut prendre quelques minutes. Vous pouvez vérifier l’état de l’opération à l’aide de la commande [az feature show](/cli/azure/feature#az_feature_show) :

    ```azurecli
    az feature show --name MultipleAvailabilityZones --namespace Microsoft.Compute
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```

    L’exemple suivant montre l’état souhaité de la fonctionnalité, à savoir *Registered* (inscrit) :
    
    ```json
    "properties": {
          "state": "Registered"
       },
    ```

3. Quand le groupe identique redondant dans une zone et les ressources réseau apparaissent comme inscrits (*Registered*), réinscrivez les fournisseurs *Compute* et *Network* à l’aide de la commande [az provider register](/cli/azure/provider#az_provider_register) comme suit :

    ```azurecli
    az provider register --namespace Microsoft.Compute
    az provider register --namespace Microsoft.Network
    ```

Pour créer un groupe identique redondant dans une zone, spécifiez des zones multiples à l’aide du paramètre `--zones`. Dans l’exemple suivant, un groupe identique redondant dans une zone, nommé *myScaleSet*, est créé dans les zones *1,2,3* :

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --zones {1,2,3}
```

Quelques minutes sont nécessaires pour créer et configurer l’ensemble des ressources et machines virtuelles du groupe identique dans les zones que vous spécifiez. Pour obtenir un exemple complet de groupe identique redondant dans une zone et de ressources réseau, consultez [cet exemple de script CLI](https://github.com/Azure/azure-docs-cli-python-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.sh).


## <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
Le processus de création d’un groupe identique qui utilise une zone de disponibilité est identique à celui décrit dans [l’article de prise en main](quick-create-powershell.md). Pour utiliser Zones de disponibilité, vous devez créer votre groupe identique dans une région Azure prise en charge et vous être [inscrit à la préversion de Zones de disponibilité](http://aka.ms/azenroll). Ajoutez le paramètre `-Zone` à la commande [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig), puis spécifiez la zone à utiliser (par exemple, zone *1*, *2* ou *3*). 

L’exemple suivant crée une configuration de groupe identique dans une zone unique, nommée *vmssConfig* dans la région *Est des États-Unis 2*, zone *1* :

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1"
```

Pour obtenir un exemple complet de groupe identique dans une zone unique et de ressources réseau, consultez [cet exemple de script PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1).

### <a name="zone-redundant-scale-set"></a>Groupe identique redondant dans une zone
Pour créer un groupe identique redondant dans une zone, vous devez utiliser une adresse IP publique et un équilibreur de charge avec une référence SKU *Standard*. Pour assurer une meilleure redondance, la référence SKU *Standard* crée des ressources réseau redondantes dans une zone. Pour plus d’informations, consultez [Présentation de la référence Standard d’Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md). La première fois que vous créez un groupe identique redondant dans une zone ou un équilibreur de charge, vous devez réaliser les étapes suivantes pour inscrire votre compte avec ces fonctionnalités disponibles en préversion.

1. Inscrivez votre compte pour le groupe identique redondant dans une zone et les fonctionnalités réseau à l’aide la commande [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature) comme suit :

    ```powershell
    Register-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. L’inscription des fonctionnalités peut prendre quelques minutes. Vous pouvez vérifier l’état de l’opération à l’aide de la commande [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature) :

    ```powershell
    Get-AzureRmProviderFeature -FeatureName MultipleAvailabilityZones -ProviderNamespace Microsoft.Compute 
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    L’exemple suivant montre l’état souhaité de la fonctionnalité, à savoir *Registered* (inscrit) :
    
    ```powershell
    RegistrationState
    -----------------
    Registered
    ```

3. Quand le groupe identique redondant dans une zone et les ressources réseau apparaissent comme inscrits (*Registered*), réinscrivez les fournisseurs *Compute* et *Network* à l’aide de la commande [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider) comme suit :

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```

Pour créer un groupe identique redondant dans une zone, spécifiez des zones multiples à l’aide du paramètre `-Zone`. Dans l’exemple suivant, une configuration de groupe identique redondant dans une zone, nommée *myScaleSet*, est créée dans la région *Est des États-Unis 2*, zones *1, 2, 3* :

```powershell
$vmssConfig = New-AzureRmVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Zone "1", "2", "3"
```

Si vous créez une adresse IP publique à l’aide de[New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) ou un équilibreur de charge à l’aide de[New-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/New-AzureRmLoadBalancer), spécifiez la référence *-SKU "Standard"* pour créer des ressources réseau redondantes dans une zone. Vous devez également créer un groupe de sécurité réseau et les règles associées pour autoriser tout le trafic. Pour plus d’informations, consultez [Présentation de la référence Standard d’Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md).

Pour obtenir un exemple complet de groupe identique redondant dans une zone et de ressources réseau, consultez [cet exemple de script PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine-scale-sets/create-zone-redundant-scale-set/create-zone-redundant-scale-set.ps1).


## <a name="use-azure-resource-manager-templates"></a>Utiliser les modèles Azure Resource Manager
Le processus de création d’un groupe identique qui utilise une zone de disponibilité est identique à celui décrit dans l’article de prise en main pour [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Pour utiliser Zones de disponibilité, vous devez créer votre groupe identique dans une région Azure prise en charge et vous être [inscrit à la préversion de Zones de disponibilité](http://aka.ms/azenroll). Ajoutez la propriété `zones` au type de ressource *Microsoft.Compute/virtualMachineScaleSets* dans votre modèle, puis spécifiez la zone à utiliser (par exemple, zone *1*, *2* ou *3*).

L’exemple suivant crée un groupe identique Linux dans une zone unique, nommé *myScaleSet* dans la région *Est des États-Unis 2*, zone *1* :

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": ["1"],
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

Pour obtenir un exemple complet de groupe identique dans une zone unique et de ressources réseau, consultez [cet exemple de modèle Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/singlezone.json).

### <a name="zone-redundant-scale-set"></a>Groupe identique redondant dans une zone
Pour créer un groupe identique redondant dans une zone, spécifiez plusieurs valeurs pour la propriété `zones` du type de ressource *Microsoft.Compute/virtualMachineScaleSets*. Dans l’exemple suivant, un groupe identique redondant dans une zone, nommé *myScaleSet*, est créé dans la région *Est des États-Unis 2*, zones *1,2,3* :

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2017-12-01",
  "zones": [
        "1",
        "2",
        "3"
      ]
}
```

Si vous créez une adresse IP publique ou un équilibreur de charge, spécifiez la propriété *"sku": { "name": "Standard" }"* pour créer des ressources réseau redondantes dans une zone. Vous devez également créer un groupe de sécurité réseau et les règles associées pour autoriser tout le trafic. Pour plus d’informations, consultez [Présentation de la référence Standard d’Azure Load Balancer](../load-balancer/load-balancer-standard-overview.md).

Pour obtenir un exemple complet de groupe identique redondant dans une zone et de ressources réseau, consultez [cet exemple de modèle Resource Manager](https://github.com/Azure/vm-scale-sets/blob/master/preview/zones/multizone.json).


## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez créé un groupe identique dans une zone de disponibilité, vous pouvez apprendre à [déployer des applications sur des groupes identiques de machines virtuelles](virtual-machine-scale-sets-deploy-app.md) ou à [utiliser la mise à l’échelle automatique avec des groupes identiques de machines virtuelles](virtual-machine-scale-sets-autoscale-overview.md).
