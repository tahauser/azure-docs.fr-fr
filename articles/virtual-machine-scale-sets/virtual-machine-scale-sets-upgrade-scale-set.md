---
title: Modifier un groupe de machines virtuelles identiques Azure | Microsoft Docs
description: Modifier un groupe de machines virtuelles identiques Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: fcca912a8120a51d2f0a454ef0a6341cd5882015
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modifier un groupe de machines virtuelles identiques
Cet article explique comment modifier un groupe existant de machines virtuelles identiques. Il décrit les tâches à effectuer, notamment la configuration du groupe identique, la modification de la configuration des applications exécutées dans le groupe identique, la gestion de la disponibilité.

## <a name="fundamental-concepts"></a>Concepts fondamentaux

### <a name="scale-set-model"></a>Modèle du groupe identique

Un groupe identique a un modèle qui capture l’état *souhaité* du groupe identique dans son ensemble. Pour interroger le modèle d’un groupe identique, vous pouvez utiliser :

* API REST : 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
   
  Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get).

* PowerShell :

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}`
   
  Pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Interface de ligne de commande Azure : 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Pour plus d’informations, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Vous pouvez également utiliser [Azure Resource Explorer (préversion)](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour interroger le modèle d’un groupe identique.

La sortie exacte obtenue varie selon les options que vous ajoutez à la commande. Voici un exemple de sortie retournée par Azure CLI :

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Comme vous pouvez le voir, ces propriétés s’appliquent à l’ensemble du groupe identique.



### <a name="scale-set-instance-view"></a>Vue d’instance d’un groupe identique

Un groupe identique a également une vue d’instance qui capture l’état actuel de *l’exécution* du groupe identique dans son ensemble. Pour interroger la vue d’instance d’un groupe identique, vous pouvez utiliser :

* API REST : 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` 
   
  Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview).

* PowerShell : 

  `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` 
  
  Pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss).

* Interface de ligne de commande Azure : 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` 
   
  Pour plus d’informations, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Vous pouvez également utiliser [Azure Resource Explorer (préversion)](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour interroger la vue d’instance d’un groupe identique.

La sortie exacte obtenue varie selon les options que vous ajoutez à la commande. Voici un exemple de sortie retournée par Azure CLI :

```
$ az vmss get-instance-view -g {resourceGroupName} -n {virtualMachineScaleSetName}
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
  .
  .
  .
}
```

Comme vous pouvez le voir, ces propriétés fournissent un résumé de l’état d’exécution actuel des machines virtuelles du groupe identique. Le résumé inclut l’état des extensions appliquées au groupe identique (omis par souci de concision).



### <a name="scale-set-vm-model-view"></a>Vue de modèle d’une machine virtuelle d’un groupe identique

Comme le groupe identique, chaque machine virtuelle a sa propre vue de modèle. Pour interroger la vue de modèle d’un groupe identique, vous pouvez utiliser :

* API REST : 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` 
  
  Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get).

* PowerShell : 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
  
  Pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Interface de ligne de commande Azure : 

  `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Pour plus d’informations, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show).

Vous pouvez également utiliser [Azure Resource Explorer (préversion)](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour interroger le modèle d’une machine virtuelle d’un groupe identique.

La sortie exacte obtenue varie selon les options que vous ajoutez à la commande. Voici un exemple de sortie retournée par Azure CLI :

```
$ az vmss show -g {resourceGroupName} -n {vmScaleSetName}
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
  .
  .
  .
}
```

Comme vous pouvez le voir, ces propriétés décrivent la configuration de la machine virtuelle, et non celle du groupe identique dans son ensemble. Par exemple, le modèle du groupe identique a la propriété `overprovision`, ce qui n’est pas le cas pour le modèle d’une machine virtuelle. Ceci s’explique par le fait que cette propriété de surprovisionnement s’applique au groupe identique dans son ensemble, et pas aux machines virtuelles individuelles dans le groupe identique. (Pour plus d’informations sur le surprovisionnement, consultez [Considérations relatives à la conception des groupes de machines virtuelles identiques](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning).)



### <a name="scale-set-vm-instance-view"></a>Vue d’instance d’une machine virtuelle de groupe identique

Comme le groupe identique, chaque machine virtuelle a sa propre vue d’instance. Pour interroger la vue d’instance d’un groupe identique, vous pouvez utiliser :

* API REST : 

  `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` 
 
  Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview).

* PowerShell : 

  `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` 
  
  Pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm).

* Interface de ligne de commande Azure : 

  `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` 
  
  Pour plus d’informations, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view).

Vous pouvez également utiliser [Azure Resource Explorer (préversion)](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour interroger la vue d’instance d’une machine virtuelle d’un groupe identique.

La sortie exacte obtenue varie selon les options que vous ajoutez à la commande. Voici un exemple de sortie retournée par Azure CLI :

```
$ az vmss get-instance-view -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
  .
  .
  .
}
```

Comme vous pouvez le voir, ces propriétés fournissent l’état d’exécution actuel de la machine virtuelle. L’état inclut les extensions appliquées au groupe identique (omis par souci de concision).




## <a name="techniques-for-updating-global-scale-set-properties"></a>Techniques de mise à jour des propriétés globales d’un groupe identique

Pour mettre à jour une propriété globale d’un groupe identique, vous devez mettre à jour cette propriété dans le modèle du groupe identique. Vous pouvez effectuer cette modification via :

* API REST : 

  `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` 
  
  Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate).

  Vous pouvez également déployer un modèle Azure Resource Manager à l’aide des propriétés de l’API REST pour modifier les propriétés globales du groupe identique.

* PowerShell : 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` 
  
  Pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss).

* Interface de ligne de commande Azure :

  * Pour modifier une propriété : `az vmss update --set {propertyPath}={value}` 
  
  * Pour ajouter un objet à la propriété de liste d’un groupe identique : `az vmss update --add {propertyPath} {JSONObjectToAdd}` 
  
  * Pour supprimer un objet de la propriété de liste d’un groupe identique : `az vmss update --remove {propertyPath} {indexToRemove}` 
  
  Pour plus d’informations, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update). 
  
  Si vous avez précédemment déployé le groupe identique à l’aide de la commande `az vmss create`, vous pouvez réexécuter la commande `az vmss create` pour modifier le groupe identique. Pour ce faire, vérifiez que toutes les propriétés définies dans la commande `az vmss create` sont les mêmes qu’avant, à l’exception de celles que vous souhaitez modifier.



Vous pouvez également utiliser [Azure Resource Explorer (préversion)](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour mettre à jour le modèle du groupe identique.

Une fois que vous avez mis à jour le modèle du groupe identique, la nouvelle configuration est appliquée à toutes les nouvelles machines virtuelles qui sont ajoutées au groupe identique. Toutefois, les modèles des machines virtuelles existantes dans le groupe identique doivent toujours être mis à jour avec le dernier modèle global du groupe identique. Dans le modèle de chaque machine virtuelle, une propriété booléenne appelée `latestModelApplied` indique si la machine virtuelle est à jour par rapport au dernier modèle global du groupe identique. (Une valeur de `true` signifie que la machine virtuelle est à jour.)




## <a name="techniques-for-bringing-vms-up-to-date-with-the-latest-scale-set-model"></a>Techniques de mise à jour des machines virtuelles avec le dernier modèle du groupe identique

Les groupes identiques ont une *stratégie de mise à niveau* qui détermine la façon dont les machines virtuelles sont mises à jour avec le dernier modèle du groupe identique. Les trois modes de la stratégie de mise à niveau sont les suivants :

- **Automatique** : avec ce mode, le groupe identique ne garantit pas l’ordre dans lequel les machines virtuelles sont arrêtées. Le groupe identique peut arrêter toutes les machines virtuelles en même temps. 
- **Continue** : avec ce mode, le groupe identique déploie la mise à jour par lots, en marquant éventuellement une pause entre chaque lot.
- **Manuelle** : avec ce mode, quand vous mettez à jour le modèle du groupe identique, les machines virtuelles existantes ne sont pas mises à jour. Chaque machine virtuelle existante doit être mise à niveau manuellement de façon individuelle. Vous pouvez effectuer cette mise à niveau manuelle via :

  - API REST : 
  
    `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` 
    
    Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances).

  - PowerShell : 
  
    `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` 
    
    Pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance).

  - Interface de ligne de commande Azure : 
  
    `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` 
    
    Pour plus d’informations, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances).

  Vous pouvez également utiliser les [SDK Azure](https://azure.microsoft.com/downloads/) pour effectuer manuellement la mise à niveau d’une machine virtuelle dans un groupe identique.

>[!NOTE]
> Les clusters Azure Service Fabric prennent en charge uniquement le mode Automatique, et la mise à jour est gérée différemment. Pour plus d’informations sur les mises à jour Service Fabric, consultez la [documentation Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

Notez que la stratégie de mise à niveau ne s’applique pas pour un type de modification des propriétés globales du groupe, à savoir les modifications du profil de système d’exploitation du groupe identique. (C’est le cas, par exemple, pour le nom d’utilisateur administrateur et le mot de passe.) Ces propriétés peuvent être modifiées uniquement dans l’API 2017-12-01 ou les versions ultérieures. Ces modifications s’appliquent uniquement aux machines virtuelles qui ont été créées après la modification du modèle du groupe identique. Pour mettre à jour les machines virtuelles existantes, vous devez réimager chacune d’elles. Pour cela, vous avez le choix entre trois méthodes :

* API REST : 

  `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` 
  
  Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage).

* PowerShell : 

  `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` 
  
  Pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm).

* Interface de ligne de commande Azure : 

  `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` 
  
  Pour plus d’informations, consultez la [documentation relative à l’interface Azure CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage).

Vous pouvez également utiliser les [SDK Azure](https://azure.microsoft.com/downloads/) pour effectuer le réimageage d’une machine virtuelle d’un groupe identique.




## <a name="properties-with-restrictions-on-modification"></a>Propriétés avec restrictions au niveau de la modification

### <a name="create-time-properties"></a>Propriétés définies au moment de la création

Certaines propriétés peuvent être définies uniquement durant la création initiale du groupe identique. Ces propriétés sont les suivantes :

- Zones
- Image reference publisher
- Image reference offer

### <a name="properties-that-can-be-changed-based-on-the-current-value-only"></a>Propriétés modifiables en fonction de la valeur actuelle uniquement

Certaines propriétés peuvent être modifiées, avec des exceptions, en fonction de la valeur actuelle. Ces propriétés sont les suivantes :

- `singlePlacementGroup` : si `singlePlacementGroup` a la valeur true, elle peut être changée en false. En revanche, si `singlePlacementGroup` a la valeur false, elle *ne peut pas* être changée en true.
- `subnet` : le sous-réseau d’un groupe identique peut être modifié si le sous-réseau d’origine et le nouveau sous-réseau se trouvent dans le même réseau virtuel.

### <a name="properties-that-require-deallocation-to-change"></a>Propriétés qui nécessitent une désallocation pour être modifiées

La valeur de certaines propriétés peut être modifiée uniquement si les machines virtuelles du groupe identique sont libérées. Ces propriétés sont les suivantes :

- `sku name` : si la nouvelle référence SKU de machine virtuelle n’est pas prise en charge par l’appareil sur lequel se trouve le groupe identique, vous devez libérer les machines virtuelles du groupe identique avant de modifier `sku name`. Pour plus d’informations sur le redimensionnement des machines virtuelles, consultez [ce billet de blog Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>Mises à jour relatives à certaines machines virtuelles

Certaines modifications peuvent être appliquées uniquement à des machines virtuelles spécifiques, et non aux propriétés globales du groupe identique. Pour le moment, la seule mise à jour applicable à certaines machines virtuelles qui est prise en charge est celle qui permet de joindre et de détacher des disques de données au niveau des machines virtuelles du groupe identique. Cette fonctionnalité est en préversion. Pour plus d’informations, consultez la [documentation relative à la préversion](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios"></a>Scénarios

### <a name="application-updates"></a>Mises à jour d’application

Si une application est déployée dans un groupe identique par le biais d’extensions, la mise à jour de la configuration des extensions entraîne la mise à jour de l’application conformément à la stratégie de mise à niveau. Par exemple, si vous avez une nouvelle version d’un script qui doit s’exécuter dans une extension de script personnalisé, vous pouvez mettre à jour la propriété `fileUris` pour qu’elle pointe vers le nouveau script. 

Dans certains cas, vous pouvez avoir besoin de forcer une mise à jour, même si la configuration des extensions est inchangée. (Par exemple, vous avez mis à jour le script en gardant le même URI de script.) Dans ce cas, vous pouvez modifier `forceUpdateTag` pour forcer la mise à jour. La plateforme Azure n’interprète pas cette propriété. La modification de sa valeur n’a donc aucun effet sur la façon dont l’extension s’exécute. Sa modification ne fait que forcer la réexécution de l’extension. 

Pour plus d’informations sur `forceUpdateTag`, consultez la [documentation API REST sur les extensions](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Il est également courant de déployer des applications à l’aide d’une image personnalisée. Ce scénario est décrit dans la section suivante.

### <a name="os-updates"></a>Mises à jour de système d’exploitation

Si vous utilisez des images de plateforme, vous pouvez mettre à jour les images en modifiant `imageReference`. Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

>[!NOTE]
> Avec les images de plateforme, « latest » est souvent spécifié comme version de référence de l’image. Ainsi, quand des groupes identiques sont créés, mis à l’échelle et réimagés, les machines virtuelles sont créées avec la dernière version d’image disponible. Toutefois, cela *ne signifie pas* que l’image du système d’exploitation sera automatiquement mise à jour à chaque nouvelle version d’image. Il s’agit là d’une autre fonctionnalité, actuellement en préversion. Pour plus d’informations, consultez [Mises à niveau automatiques du système d’exploitation](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Si vous utilisez des images personnalisées, vous pouvez mettre à jour les images en mettant à jour l’ID `imageReference`. Pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate).

## <a name="examples"></a>Exemples

### <a name="update-the-os-image-for-your-scale-set"></a>Mettre à jour l’image du système d’exploitation pour un groupe identique

Supposons que vous utilisez un groupe identique qui exécute une ancienne version d’Ubuntu LTS 16.04. Vous souhaitez mettre à jour cette version avec une version plus récente d’Ubuntu LTS 16.04 (par exemple, la version 16.04.201801090). La propriété de la version de référence de l’image ne fait pas partie d’une liste. Vous pouvez donc la modifier directement à l’aide des commandes suivantes :

* PowerShell : 

  `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

* Interface de ligne de commande Azure : 

  `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="update-the-load-balancer-for-your-scale-set"></a>Mettre à jour l’équilibreur de charge pour un groupe identique

Supposons que vous utilisez un groupe identique avec un équilibreur de charge Azure et que vous souhaitez remplacer cet équilibreur de charge par une passerelle d’application Azure. Les propriétés de l’équilibreur de charge et de la passerelle d’application pour un groupe identique font partie d’une liste. Vous pouvez donc utiliser les commandes de suppression et d’ajout d’éléments de liste au lieu de modifier les propriétés directement.

PowerShell :
```
# Get the current model of the scale set and store it in a local PowerShell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure load balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local PowerShell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Interface de ligne de commande Azure :
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # Remove the load balancer back-end pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # Remove the load balancer back-end pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # Add the application gateway back-end pool to the scale set model
```

>[!NOTE]
> Ces commandes supposent qu’il n’existe qu’une seule configuration IP et qu’un seul équilibreur de charge dans le groupe identique. S’il en existe plusieurs, vous devrez peut-être utiliser un index de liste différent de 0.