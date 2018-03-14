---
title: Modifier un groupe de machines virtuelles identiques Azure | Microsoft Docs
description: Modifier un groupe de machines virtuelles identiques Azure
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: negat
ms.openlocfilehash: cdd1015f63e80b7ec51565c18f3440ce1828fb03
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2018
---
# <a name="modify-a-virtual-machine-scale-set"></a>Modifier un groupe de machines virtuelles identiques
Cet article explique comment modifier un groupe identique existant. Cela comprend notamment la configuration du groupe identique, la modification de la configuration des applications exécutées dans le groupe identique, la gestion de la disponibilité.

## <a name="fundamental-concepts"></a>Concepts fondamentaux

### <a name="the-scale-set-model"></a>Modèle du groupe identique

Un groupe identique est associé à un modèle qui capture l’état *souhaité* du groupe identique dans son ensemble. Pour interroger le modèle d’un groupe identique, vous pouvez utiliser :

Une API REST : `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/get))

PowerShell : `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

L’interface CLI : `az vmss show -g {resourceGroupName} -n {vmSaleSetName}` (pour plus d’informations, consultez la [documentation relative à l’interface CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Vous pouvez également utiliser [resources.azure.com](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour interroger le modèle d’un groupe identique.

La sortie exacte varie selon les options que vous fournissez à la commande. Voici toutefois un exemple de sortie pour l’interface CLI :

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



### <a name="the-scale-set-instance-view"></a>Vue d’instance du groupe identique

Un groupe identique est également associé à une vue d’instance de groupe identique. Cette vue capture l’état actuel de *l’exécution* du groupe identique dans son ensemble. Pour interroger la vue d’instance d’un groupe identique, vous pouvez utiliser :

Une API REST : `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/instanceView?api-version={apiVersion}` (pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/getinstanceview))

PowerShell : `Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceView` (pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss))

L’interface CLI : `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName}` (pour plus d’informations, consultez la [documentation relative à l’interface CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Vous pouvez également utiliser [resources.azure.com](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour interroger la vue d’instance d’un groupe identique.

La sortie exacte varie selon les options que vous fournissez à la commande. Voici toutefois un exemple de sortie pour l’interface CLI :

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

Comme vous pouvez le voir, ces propriétés fournissent un récapitulatif de l’état actuel de l’exécution des machines virtuelles du groupe identique, notamment l’état des extensions appliquées au groupe identique (omis par souci de concision).



### <a name="the-scale-set-vm-model-view"></a>Vue de modèle des machines virtuelles du groupe identique

Comme le groupe identique, chaque machine virtuelle a sa propre vue de modèle. Pour interroger la vue de modèle d’un groupe identique, vous pouvez utiliser :

Une API REST : `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}?api-version={apiVersion}` (pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/get))

PowerShell : `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

L’interface CLI : `az vmss show -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (pour plus d’informations, consultez la [documentation relative à l’interface CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_show))

Vous pouvez également utiliser [resources.azure.com](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour interroger le modèle d’une machine virtuelle d’un groupe identique.

La sortie exacte varie selon les options que vous fournissez à la commande. Voici toutefois un exemple de sortie pour l’interface CLI :

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

Comme vous pouvez le voir, ces propriétés décrivent la configuration de la machine virtuelle, et non celle du groupe identique dans son ensemble. Par exemple, le modèle du groupe identique a la propriété `overprovision`, contrairement au modèle d’une machine virtuelle. En effet, le surprovisionnement est une propriété qui s’applique aux groupes identiques, et pas aux machines virtuelles qui les constituent (pour plus d’informations sur le surprovisionnement, consultez [cette documentation](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview#overprovisioning)).



### <a name="the-scale-set-vm-instance-view"></a>Vue d’instance des machines virtuelles d’un groupe identique

Comme le groupe identique, chaque machine virtuelle a sa propre vue d’instance. Pour interroger la vue d’instance d’un groupe identique, vous pouvez utiliser :

Une API REST : `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/instanceView?api-version={apiVersion}` (pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/getinstanceview))

PowerShell : `Get-AzureRmVmssVm -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -InstanceView` (pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmssvm))

L’interface CLI : `az vmss get-instance-view -g {resourceGroupName} -n {vmSaleSetName} --instance-id {instanceId}` (pour plus d’informations, consultez la [documentation relative à l’interface CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_get_instance_view))

Vous pouvez également utiliser [resources.azure.com](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour interroger la vue d’instance d’une machine virtuelle d’un groupe identique.

La sortie exacte varie selon les options que vous fournissez à la commande. Voici toutefois un exemple de sortie pour l’interface CLI :

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

Comme vous pouvez le voir, ces propriétés décrivent l’état actuel de l’exécution de la machine virtuelle, y compris celui des extensions appliquées au groupe identique (omis par souci de concision).




## <a name="how-to-update-global-scale-set-properties"></a>Comment mettre à jour les propriétés globales d’un groupe identique

Pour mettre à jour une propriété globale d’un groupe identique, vous devez mettre à jour cette propriété dans le modèle du groupe identique. Vous pouvez effectuer cette modification via :

Une API REST : `PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}?api-version={apiVersion}` (pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate))

Des modèles Resource Manager : vous pouvez également déployer un modèle Resource Manager à l’aide des propriétés de l’API REST pour modifier les propriétés globales du groupe identique.

PowerShell : `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -VirtualMachineScaleSet {scaleSetConfigPowershellObject}` (pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss))

L’interface CLI Pour modifier une propriété : `az vmss update --set {propertyPath}={value}`. Pour ajouter un objet à la propriété de liste d’un groupe identique : `az vmss update --add {propertyPath} {JSONObjectToAdd}`. Pour supprimer un objet de la propriété de liste d’un groupe identique : `az vmss update --remove {propertyPath} {indexToRemove}`. (pour plus d’informations, consultez la [documentation relative à l’interface CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update)) Si vous avez précédemment déployé le groupe identique à l’aide de la commande `az vmss create`, vous pouvez réexécuter la commande `az vmss create` pour modifier le groupe identique. Pour ce faire, vous devez vérifier que toutes les propriétés de la commande `az vmss create` sont les mêmes qu’avant, à l’exception de celles que vous souhaitez modifier.



Vous pouvez également utiliser [resources.azure.com](https://resources.azure.com) ou les [SDK Azure](https://azure.microsoft.com/downloads/) pour modifier le modèle d’un groupe identique.

Une fois que vous avez modifié le modèle du groupe identique, la nouvelle configuration est appliquée à toutes les nouvelles machines virtuelles qui sont créées dans le groupe identique. Toutefois, les modèles des machines virtuelles existantes appartenant au groupe identique doivent toujours être mis à jour à l’aide du dernier modèle global du groupe identique. Le modèle de chaque machine virtuelle contient une propriété booléenne appelée `latestModelApplied`, qui indique si la machine virtuelle est à jour, par rapport au dernier modèle global du groupe identique (`true` signifie que la machine virtuelle est à jour par rapport au dernier modèle).




## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>Comment mettre à jour les machines virtuelles avec le dernier modèle du groupe identique

Les groupes identiques ont une « stratégie de mise à niveau » qui détermine la façon dont les machines virtuelles sont mises à jour à l’aide du dernier modèle du groupe identique. Les trois modes de la stratégie de mise à niveau sont les suivants :

- Automatique : avec ce mode, le groupe identique ne garantit pas l’ordre dans lequel les machines virtuelles sont arrêtées. Le groupe identique peut arrêter toutes les machines virtuelles en même temps. 
- Continue : avec ce mode, le groupe identique déploie la mise à jour par lots, en marquant une pause entre chaque lot (facultatif).
- Manuelle : avec ce mode, lorsque vous mettez à jour le modèle du groupe identique, rien ne se produit pour les machines virtuelles existantes. Pour mettre à jour les machines virtuelles existantes, vous devez effectuer une « mise à niveau manuelle » de chaque machine virtuelle existante. Vous pouvez effectuer cette mise à niveau manuelle via :

Une API REST : `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/manualupgrade?api-version={apiVersion}` (pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/updateinstances))

PowerShell : `Update-AzureRmVmssInstance -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId}` (pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance))

L’interface CLI : `az vmss update-instances -g {resourceGroupName} -n {vmScaleSetName} --instance-ids {instanceIds}` (pour plus d’informations, consultez la [documentation relative à l’interface CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_update_instances))

Vous pouvez également utiliser les [SDK Azure](https://azure.microsoft.com/downloads/) pour effectuer une mise à niveau manuelle sur une machine virtuelle d’un groupe identique.

>[!NOTE]
> Les clusters Service Fabric peuvent uniquement utiliser le mode Automatique, et la mise à jour est gérée différemment. Pour plus d’informations sur les mises à jour Service Fabric, consultez la [documentation Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade).

>[!NOTE]
> Il existe une méthode de modification des propriétés globales de groupe identique qui ne respecte pas la stratégie de mise à niveau. Il s’agit des modifications apportées au profil de système d’exploitation du groupe identique (par exemple, le nom d’utilisateur ou le mot de passe de l’administrateur). Ces propriétés ne peuvent être modifiées que dans l’API 2017-12-01 ou versions ultérieures. Ces modifications s’appliquent uniquement aux machines virtuelles qui ont été créées après la modification du modèle du groupe identique. Pour mettre à jour les machines virtuelles existantes, vous devez effectuer un réimageage pour chacune d’elles. Vous pouvez effectuer ce réimageage via :

Une API REST : `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell : `Set-AzureRmVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (pour plus d’informations, consultez la [documentation PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmssvm))

L’interface CLI : `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (pour plus d’informations, consultez la [documentation relative à l’interface CLI](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest#az_vmss_reimage))

Vous pouvez également utiliser les [SDK Azure](https://azure.microsoft.com/downloads/) pour effectuer le réimageage d’une machine virtuelle d’un groupe identique.




## <a name="properties-with-restrictions-on-modification"></a>Propriétés avec restrictions au niveau de la modification

### <a name="create-time-properties"></a>Propriétés définies au moment de la création

Certaines propriétés peuvent être définies uniquement lors de la création du groupe identique. Ces propriétés sont les suivantes :

- zones
- image reference publisher
- image reference offer

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Propriétés qui peuvent être modifiées uniquement en fonction de la valeur actuelle

La capacité à modifier certaines propriétés peut dépendre de la valeur actuelle. Ces propriétés sont les suivantes :

- singlePlacementGroup : si singlePlacementGroup a la valeur true, celle-ci peut être remplacée par false. Toutefois, si singlePlacementGroup a la valeur false, elle **ne peut pas** avoir la valeur true.
- subnet : le sous-réseau d’un groupe identique peut être modifié, tant que le sous-réseau d’origine et le nouveau sous-réseau se trouvent dans le même réseau virtuel.

### <a name="properties-that-require-deallocation-to-change"></a>Propriétés qui nécessitent une désallocation pour être modifiées

La valeur de certaines propriétés peut uniquement être modifiée si les machines virtuelles du groupe identique sont libérées. Ces propriétés sont les suivantes :

- sku name : si la nouvelle référence SKU de machine virtuelle n’est pas prise en charge par l’appareil sur lequel se trouve le groupe identique, vous devez libérer les machines virtuelles du groupe identique avant de modifier le nom de la référence SKU. Pour plus d’informations sur le redimensionnement des machines virtuelles, consultez [ce billet de blog Azure](https://azure.microsoft.com/blog/resize-virtual-machines/).


## <a name="vm-specific-updates"></a>Mises à jour relatives à certaines machines virtuelles

Certaines modifications peuvent être appliquées uniquement à certaines machines virtuelles, et non aux propriétés globales du groupe identique. Pour le moment, la seule mise à jour applicable à certaines machines virtuelles qui est prise en charge est celle qui permet de joindre et de détacher des disques de données au niveau des machines virtuelles du groupe identique. Cette fonctionnalité est en préversion. Pour plus d’informations, consultez la [documentation relative à la préversion](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).

## <a name="scenarios-application-updates-os-updates-etc"></a>Scénarios : Mises à jour d’application, mises à jour du système d’exploitation, etc.

### <a name="application-updates"></a>Mises à jour d’application

Si une application est déployée dans un groupe identique via des extensions, la mise à jour de la configuration de l’extension va entraîner la mise à jour de l’application conformément à la stratégie de mise à niveau. Par exemple, si vous avez une nouvelle version d’un script qui doit s’exécuter dans une extension de script personnalisé, vous pouvez mettre à jour la propriété fileUris pour qu’elle pointe vers le nouveau script. Dans certains cas, toutefois, il est nécessaire de forcer une mise à jour, même si cela ne modifie pas la configuration de l’extension (par exemple, si vous avez mis à jour le script sans modifier son URI). Dans ce cas, vous pouvez modifier le forceUpdateTag pour qu’il force la mise à jour. La plateforme Azure n’interprète pas cette propriété. La modification de sa valeur n’a donc aucun effet sur la façon dont l’extension s’exécute. Sa modification ne fait que forcer la réexécution de l’extension. Pour plus d’informations sur forceUpdateTag, consultez la [documentation API REST sur les extensions](https://docs.microsoft.com/rest/api/compute/virtualmachineextensions/createorupdate).

Il est également courant que les applications soient déployées via une image personnalisée. Ce scénario est abordé dans la section « Mises à jour du système d’exploitation ».

### <a name="os-updates"></a>Mises à jour de système d’exploitation

Si vous utilisez des images de plateforme, vous pouvez les mettre à jour en modifiant imageReference (pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

>[!NOTE]
> Avec les images de plateforme, il est courant de spécifier « latest » (dernière) pour la version de référence d’image. Cela signifie que lors de la création, de l’augmentation de la taille des instances et du réimageage du groupe identique, les machines virtuelles sont créées avec la dernière version disponible. Toutefois, cela **ne signifie pas** que l’image du système d’exploitation sera automatiquement mise à jour à chaque nouvelle version d’image. Il s’agit là d’une autre fonctionnalité, actuellement en préversion. Pour plus d’informations, consultez la [documentation relative aux mises à niveau automatiques du système d’exploitation](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

Si vous utilisez des images personnalisées, vous pouvez les mettre à jour en modifiant l’ID d’imageReference (pour plus d’informations, consultez la [documentation de l’API REST](https://docs.microsoft.com/en-us/rest/api/compute/virtualmachinescalesets/createorupdate)).

## <a name="examples"></a>Exemples

### <a name="updating-the-os-image-for-your-scale-set"></a>Mise à jour de l’image du système d’exploitation pour votre groupe identique

Supposons que vous disposiez d’un groupe identique qui s’exécute sur une ancienne version d’Ubuntu LTS 16.04, et que vous souhaitiez effectuer une mise à jour vers une version plus récente d’Ubuntu LTS 16.04 (par exemple, la version 16.04.201801090). La propriété de version de la référence d’image ne fait pas partie d’une liste. Vous pouvez donc la modifier directement avec les commandes suivantes :

PowerShell : `Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -ImageReferenceVersion 16.04.201801090`

Interface CLI : `az vmss update -g {resourceGroupName} -n {vmScaleSetName} --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090`


### <a name="updating-the-load-balancer-for-your-scale-set"></a>Mise à jour de l’équilibreur de charge pour votre groupe identique

Supposons que vous utilisiez un groupe identique avec Azure Load Balancer, et que vous souhaitiez remplacer Azure Load Balancer par Azure Application Gateway. Les propriétés de l’équilibreur de charge et de la passerelle d’application d’un groupe identique font partie d’une liste. Vous pouvez donc utiliser les commandes de suppression et d’ajout d’éléments de liste, au lieu de modifier directement les propriétés :

PowerShell :
```
# get the current model of the scale set and store it in a local powershell object named $vmss
> $vmss=Get-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName}

# create a local powershell object for the new desired IP configuration, which includes the referencerence to the application gateway
> $ipconf = New-AzureRmVmssIPConfig myNic -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name

# replace the existing IP configuration in the local powershell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
> $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf

# Update the model of the scale set with the new configuration in the local powershell object
> Update-AzureRmVmss -ResourceGroupName {resourceGroupName} -Name {vmScaleSetName} -virtualMachineScaleSet $vmss

```

Interface CLI :
```
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0 # remove the load balancer backend pool from the scale set model
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0 # remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
az vmss update -g {resourceGroupName} -n {vmScaleSetName} --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}' # add the application gateway backend pool to the scale set model
```

>[!NOTE]
> Ces commandes supposent qu’il n’existe qu’une seule configuration IP et qu’un seul équilibreur de charge dans le groupe identique. S’il en existe plusieurs, vous devrez peut-être utiliser un index de liste différent de 0.