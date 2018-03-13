---
title: "Mises à niveau automatiques du système d’exploitation dans des groupes de machines virtuelles identiques Azure | Microsoft Docs"
description: "Découvrez comment mettre automatiquement à niveau le système d’exploitation sur des instances de machine virtuelle dans un groupe identique"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: negat
ms.openlocfilehash: 59dad832977c4afc39db3773edf9789cd1a704e7
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-virtual-machine-scale-set-automatic-os-upgrades"></a>Mises à niveau automatiques du système d’exploitation dans des groupes de machines virtuelles identiques Azure

La mise à niveau automatique de l’image du système d’exploitation est une fonctionnalité en préversion pour les groupes de machines virtuelles identiques Azure qui met automatiquement à niveau toutes les instances de machine virtuelle avec la dernière image du système d’exploitation.

La fonctionnalité de mise à niveau automatique du système d’exploitation présente les caractéristiques suivantes :

- Une fois configurée, la dernière image du système d’exploitation publiée par les éditeurs de l’image est automatiquement appliquée au groupe identique, sans aucune intervention de l’utilisateur.
- Elle effectue une mise à niveau propagée de lots d’instances chaque fois qu’une nouvelle image de plateforme est publiée par l’éditeur.
- Elle peut s’utiliser avec une sonde d’intégrité d’application (cela est facultatif, mais fortement recommandé pour la sécurité).
- Elle fonctionne pour toutes les tailles de machine virtuelle.
- Elle prend en charge les images de plateforme Windows et Linux.
- Vous pouvez désactiver les mises à niveau automatiques à tout moment (les mises à niveau du système d’exploitation peuvent également être démarrées manuellement).
- Le disque du système d’exploitation d’une machine virtuelle est remplacé par le nouveau disque de système d’exploitation créé avec la dernière version de l’image. Les extensions configurées et les scripts de données personnalisés sont exécutés. Les disques de données persistantes sont conservés.


## <a name="preview-notes"></a>Notes de préversion 
Dans la préversion, les limitations et restrictions suivantes s’appliquent :

- Les mises à niveau automatiques du système d’exploitation prennent uniquement en charge [quatre références SKU du système d’exploitation](#supported-os-images). La fonctionnalité est fournie sans contrat de niveau de service (SLA) ni garanties. Nous vous recommandons de ne pas utiliser la préversion des mises à jour automatiques pour des charges de travail critiques dans votre environnement de production.
- Le chiffrement de disque Azure Disk Encryption (actuellement en préversion) n’est **pas** pris en charge avec les mises à niveau automatiques du système d’exploitation dans les groupes de machines virtuelles identiques.


## <a name="register-to-use-automatic-os-upgrade"></a>Inscription pour utiliser la fonctionnalité de mise à niveau automatique du système d’exploitation
Pour utiliser la fonctionnalité de mise à niveau automatique du système d’exploitation, inscrivez le fournisseur de la préversion avec [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature), comme suit :

```powershell
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName AutoOSUpgradePreview
```

Il faut environ 10 minutes pour que l’inscription affiche l’état *Inscrit*. Vous pouvez vérifier l’état actuel de l’inscription avec [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Une fois que le fournisseur est affiché comme inscrit, vérifiez que le fournisseur *Microsoft.Compute* est inscrit à l’aide de la commande [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider), comme suit :

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

> [!NOTE]
> Les clusters Service Fabric ont leurs propres critères de contrôle d’intégrité d’application, mais des groupes identiques sans Service Fabric utilisent la sonde d’intégrité de l’équilibreur de charge pour surveiller l’intégrité de l’application. Pour inscrire la fonctionnalité du fournisseur avec les sondes d’intégrité, utilisez [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature), comme suit :
>
> ```powershell
> Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVmssHealthProbe
> ```
>
> Là aussi, il faut environ 10 minutes pour que l’inscription affiche l’état *Inscrit*. Vous pouvez vérifier l’état actuel de l’inscription avec [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature). Une fois que le fournisseur est affiché comme inscrit, vérifiez que le fournisseur *Microsoft.Network* est inscrit à l’aide de la commande [Register-AzureRmResourceProvider](/powershell/module/AzureRM.Resources/Register-AzureRmResourceProvider), comme suit :
>
> ```powershell
> Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
> ```

## <a name="portal-experience"></a>Utilisation du portail
Lorsque vous suivez la procédure d’inscription ci-dessus, vous pouvez accéder au [portail Azure](https://aka.ms/managed-compute) pour activer les mises à niveau automatiques du système d’exploitation sur vos groupes identiques et voir la progression des mises à niveau :

![](./media/virtual-machine-scale-sets-automatic-upgrade/automatic-upgrade-portal.png)


## <a name="supported-os-images"></a>Images de système d’exploitation prises en charge
Seules certaines images de plateforme de système d’exploitation sont actuellement prises en charge. Vous ne pouvez pas utiliser des images personnalisées que vous avez créées vous-même. La propriété *version* de l’image de plateforme doit être définie sur *latest*.

Les références SKU suivantes sont prises en charge (d’autres le seront ultérieurement) :
    
| Publisher               | Offre         |  Sku               | Version  |
|-------------------------|---------------|--------------------|----------|
| Canonical               | UbuntuServer  | 16.04-LTS          | le plus récent   |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter | le plus récent   |
| MicrosoftWindowsServer  | WindowsServer | 2016-centre-de-données    | le plus récent   |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Smalldisk | le plus récent   |



## <a name="application-health-without-service-fabric"></a>Intégrité de l’application sans Service Fabric
> [!NOTE]
> Cette section s’applique uniquement aux groupes identiques sans Service Fabric. Service Fabric a ses propres critères d’intégrité de l’application. Lors de l’utilisation de mises à niveau automatiques du système d’exploitation avec Service Fabric, la nouvelle image du système d’exploitation est déployée, un domaine de mise à jour après l’autre, pour maintenir la haute disponibilité des services en cours d’exécution dans Service Fabric. Pour plus d’informations sur les caractéristiques de durabilité des clusters Service Fabric, voir [cette documentation](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster).

Durant une mise à niveau du système d’exploitation, les instances de machine virtuelle dans un groupe identique sont mises à niveau par lot. La mise à niveau doit se poursuivre uniquement si l’application du client est intègre sur les instances de machine virtuelle mises à niveau. Nous recommandons que l’application envoie des signaux d’intégrité au moteur de mise à niveau du système d’exploitation pour le groupe identique. Par défaut, pendant les mises à niveau du système d’exploitation, la plateforme vérifie l’état d’alimentation des machines virtuelles et l’état de provisionnement des extensions pour déterminer l’intégrité d’une instance de machine virtuelle après sa mise à niveau. Lors de la mise à niveau du système d’exploitation d’une instance de machine virtuelle, le disque du système d’exploitation sur l’instance de machine virtuelle est remplacé par un nouveau disque basé sur la dernière version de l’image. Après la mise à niveau du système d’exploitation, les extensions configurées sont exécutées sur ces machines virtuelles. L’application est considérée comme intègre uniquement quand toutes les extensions sur une machine virtuelle ont été correctement provisionnées. 

Un groupe identique peut éventuellement être configuré avec des sondes d’intégrité d’application pour fournir à la plateforme des informations précises sur l’état actuel de l’application. Les sondes d’intégrité d’application sont des sondes d’équilibreur de charge personnalisées qui sont utilisées comme signal d’intégrité. L’application exécutée sur une instance de machine virtuelle d’un groupe identique peut répondre à des demandes HTTP ou TCP externes en indiquant si elle est intègre. Pour plus d’informations sur le fonctionnement des sondes d’équilibreur de charge personnalisées, consultez [Comprendre les sondes d’équilibreur de charge](../load-balancer/load-balancer-custom-probe-overview.md). L’utilisation d’une sonde d’intégrité d’application n’est pas obligatoire pour les mises à niveau automatiques du système d’exploitation, mais cela est fortement recommandé.

Si le groupe identique est configuré pour utiliser plusieurs groupes de sélection élective, vous devez utiliser des sondes avec un [équilibreur de charge standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).

### <a name="important-keep-credentials-up-to-date"></a>Important : Tenez à jour toutes les informations d’identification
Si votre groupe identique utilise des informations d’identification pour accéder aux ressources externes (par exemple, quand une extension de machine virtuelle est configurée et utilise un jeton SAP pour le compte de stockage), vous devez vous assurer que les informations d’identification sont à jour. Si les informations d’identification, y compris les certificats et les jetons, ont expiré, la mise à niveau échoue, et le premier groupe de machines virtuelles reste dans un état d’échec.

Pour récupérer les machines virtuelles et réactiver la mise à niveau automatique du système d’exploitation après un échec d’authentification des ressources, effectuez les étapes recommandées suivantes :

* Regénérez le jeton (ou d’autres informations d’identification) qui a été passé à vos extensions.
* Vérifiez que toutes les informations d’identification utilisées pour les communications entre les machines virtuelles et les entités externes sont à jour.
* Mettez à jour chaque extension dans le modèle de groupe identique avec les nouveaux jetons.
* Déployez le groupe identique mis à jour. Cette opération met à jour toutes les instances de machine virtuelle, y compris celles en échec. 

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>Configuration d’une sonde d’équilibreur de charge personnalisée comme sonde d’intégrité d’application dans un groupe identique
La bonne pratique est de créer une sonde d’équilibreur de charge de manière explicite pour déterminer l’intégrité du groupe identique. Vous pouvez utiliser le même point de terminaison pour une sonde HTTP ou TCP existante, mais sachez qu’une sonde d’intégrité peut avoir un comportement différent de celui d’une sonde d’équilibreur de charge standard. Par exemple, une sonde d’équilibreur de charge standard peut signaler un état non intègre si la charge sur l’instance est trop élevée, alors que cela ne détermine pas forcément l’intégrité de l’instance durant une mise à niveau automatique du système d’exploitation. Configurez la sonde pour que le taux de sondage élevé soit inférieur à deux minutes.

La sonde d’équilibreur de charge peut être référencée dans la propriété *networkProfile* du groupe identique et être associée à un équilibreur de charge interne ou public, comme suit :

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
```


## <a name="enforce-an-os-image-upgrade-policy-across-your-subscription"></a>Appliquer une stratégie de mise à niveau de l’image du système d’exploitation dans votre abonnement
Pour sécuriser les mises à niveau, nous vous recommandons d’appliquer une stratégie de mise à niveau. Cette stratégie peut imposer l’utilisation de sondes d’intégrité d’application dans votre abonnement. La stratégie Azure Resource Manager suivante rejette les déploiements qui ne sont pas configurés avec les paramètres de mise à niveau automatique de l’image du système d’exploitation :

1. Obtenez la définition de la stratégie Azure Resource Manager intégrée à l’aide de [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition), comme suit :

    ```powershell
    $policyDefinition = Get-AzureRmPolicyDefinition -Id "/providers/Microsoft.Authorization/policyDefinitions/465f0161-0087-490a-9ad9-ad6217f4f43a"
    ```

2. Assignez la stratégie à un abonnement avec [New-AzureRmPolicyAssignment](/powershell/module/AzureRM.Resources/New-AzureRmPolicyAssignment), comme suit :

    ```powershell
    New-AzureRmPolicyAssignment `
        -Name "Enforce automatic OS upgrades with app health checks" `
        -Scope "/subscriptions/<SubscriptionId>" `
        -PolicyDefinition $policyDefinition
    ```


## <a name="configure-auto-updates"></a>Configurer les mises à niveau automatiques
Pour configurer les mises à niveau automatiques, vérifiez que la propriété *automaticOSUpgrade* est définie sur *true* dans la définition du modèle de groupe identique. Vous pouvez configurer cette propriété avec Azure PowerShell ou Azure CLI 2.0.

L’exemple suivant utilise Azure PowerShell (4.4.1 ou version ultérieure) pour configurer les mises à niveau automatiques pour le groupe identique nommé *myVMSS* dans le groupe de ressources nommé *myResourceGroup* :

```powershell
$rgname = myResourceGroup
$vmssname = myVMSS
$vmss = Get-AzureRmVMss -ResourceGroupName $rgname -VmScaleSetName $vmssname
$vmss.UpgradePolicy.AutomaticOSUpgrade = $true
Update-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname -VirtualMachineScaleSet $vmss
```


L’exemple suivant utilise Azure CLI (2.0.20 ou version ultérieure) pour configurer les mises à niveau automatiques pour le groupe identique nommé *myVMSS* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli
rgname="myResourceGroup"
vmssname="myVMSS"
az vmss update --name $vmssname --resource-group $rgname --set upgradePolicy.AutomaticOSUpgrade=true
```


## <a name="check-the-status-of-an-automatic-os-upgrade"></a>Vérifier l’état d’une mise à niveau automatique du système d’exploitation
Vous pouvez vérifier l’état de la dernière mise à niveau du système d’exploitation effectuée dans un groupe identique à l’aide d’Azure PowerShell, d’Azure CLI 2.0 ou des API REST.

### <a name="azure-powershell"></a>Azure PowerShell
L’exemple suivant utilise Azure PowerShell (4.4.1 ou version ultérieure) pour vérifier l’état de la mise à niveau pour le groupe identique nommé *myVMSS* dans le groupe de ressources nommé *myResourceGroup* :

```powershell
Get-AzureRmVmssRollingUpgrade -ResourceGroupName myResourceGroup -VMScaleSetName myVMSS
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
L’exemple suivant utilise Azure CLI (2.0.20 ou version ultérieure) pour vérifier l’état de la mise à niveau pour le groupe identique nommé *myVMSS* dans le groupe de ressources nommé *myResourceGroup* :

```azurecli
az vmss rolling-upgrade get-latest --resource-group myResourceGroup --name myVMSS
```

### <a name="rest-api"></a>de l’API REST
L’exemple suivant utilise l’API REST pour vérifier l’état de la mise à niveau pour le groupe identique nommé *myVMSS* dans le groupe de ressources nommé *myResourceGroup* :

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/rollingUpgrades/latest?api-version=2017-03-30`
```

L’appel de Get retourne des propriétés similaires à l’exemple de sortie suivant :

```json
{
  "properties": {
    "policy": {
      "maxBatchInstancePercent": 20,
      "maxUnhealthyInstancePercent": 5,
      "maxUnhealthyUpgradedInstancePercent": 5,
      "pauseTimeBetweenBatches": "PT0S"
    },
    "runningStatus": {
      "code": "Completed",
      "startTime": "2017-06-16T03:40:14.0924763+00:00",
      "lastAction": "Start",
      "lastActionTime": "2017-06-22T08:45:43.1838042+00:00"
    },
    "progress": {
      "successfulInstanceCount": 3,
      "failedInstanceCount": 0,
      "inprogressInstanceCount": 0,
      "pendingInstanceCount": 0
    }
  },
  "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
  "location": "southcentralus"
}
```


## <a name="automatic-os-upgrade-execution"></a>Exécution des mises à niveau automatiques du système d’exploitation
Dans le contexte de l’utilisation de sondes d’intégrité d’application, les mises à niveau du système d’exploitation pour le groupe identique fonctionnent de la manière suivante :

1. Si plus de 20 % des instances sont considérées comme non intègres, elles arrêtent la mise à niveau. Sinon, elles continuent.
2. Elles identifient le lot suivant d’instances de machine virtuelle à mettre à niveau, chaque lot représentant 20 % au maximum du nombre total d’instances.
3. Elles mettent à niveau le système d’exploitation pour le lot suivant d’instances de machine virtuelle.
4. Si plus de 20 % des instances mises à niveau sont considérées comme non intègres, elles arrêtent la mise à niveau. Sinon, elles continuent.
5. Si le client a configuré des sondes d’intégrité d’application, la mise à niveau attend jusqu’à cinq minutes que les sondes signalent l’intégrité, puis elle continue immédiatement sur le lot suivant. Sinon, la mise à niveau attend 30 minutes avant de continuer sur le lot suivant.
6. S’il reste des instances à mettre à niveau, la mise à niveau revient à l’étape 1 pour le lot suivant. Sinon, la mise à niveau est terminée.

Le moteur de mise à niveau du système d’exploitation pour le groupe identique vérifie l’intégrité de toutes les instances de machine virtuelle avant de mettre à niveau chaque lot. Pendant la mise à niveau d’un lot, il peut arriver que d’autres opérations de maintenance planifiées ou non planifiées aient lieu en même temps dans les centres de données Azure et impactent la disponibilité de vos machines virtuelles. Vous pouvez donc avoir plus de 20 % des instances qui sont temporairement arrêtées. Dans ce cas, à la fin du lot en cours, la mise à niveau du groupe identique s’arrête.


## <a name="deploy-with-a-template"></a>Déployer les mises à niveau avec un modèle

Vous pouvez utiliser le modèle suivant pour déployer un groupe identique qui utilise les mises à niveau automatiques. Consultez <a href='https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json'>Mises à niveau propagées automatiques (Ubuntu 16.04-LTS)</a>

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/>
</a>


## <a name="next-steps"></a>Étapes suivantes
Pour obtenir d’autres exemples d’utilisation des mises à niveau automatiques du système d’exploitation avec des groupes identiques, consultez le [dépôt GitHub des fonctionnalités en préversion](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade).
