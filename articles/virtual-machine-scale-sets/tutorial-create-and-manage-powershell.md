---
title: 'Didacticiel : Créer et gérer un groupe de machines virtuelles identiques Azure | Microsoft Docs'
description: Découvrez comment utiliser Azure PowerShell pour créer un groupe de machines virtuelles identiques, ainsi que certaines tâches de gestion courantes comme le démarrage et l’arrêt d’une instance, ou la modification de la capacité du groupe identique.
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 1f1b987d00fad4931f9ad39b39101cc474c2a1e3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-create-and-manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Didacticiel : Créer et gérer un groupe de machines virtuelles identiques avec Azure PowerShell
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Tout au long du cycle de vie du groupe de machines virtuelles identiques, vous devrez peut-être exécuter une ou plusieurs tâches de gestion. Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer et connecter un groupe de machines virtuelles identiques
> * Sélectionner et utiliser des images de machine virtuelle
> * Afficher et utiliser des tailles d’instance de VM spécifiques
> * Mettre manuellement à l’échelle un groupe identique
> * Effectuer des tâches courantes de gestion de groupe identique

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 5.6.0 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure. 


## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. Un groupe de ressources doit être créé avant le groupe de machines virtuelles identiques. Créez un groupe de ressources avec la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Dans cet exemple, un groupe de ressources nommé *myResourceGroup* est créé dans la région *EastUS*. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroup" -Location "EastUS"
```

Le nom du groupe de ressources est spécifié lorsque vous créez ou modifiez un groupe identique dans ce didacticiel.


## <a name="create-a-scale-set"></a>Créer un groupe identique
Tout d’abord, définissez un nom d’utilisateur administrateur et un mot de passe pour les instances de machine virtuelle avec [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) :

```azurepowershell-interactive
$cred = Get-Credential
```

À présent, créez un groupe de machines virtuelles identiques avec [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Pour distribuer le trafic aux différentes instances de machine virtuelle, un équilibreur de charge est également créé. L’équilibreur de charge inclut des règles pour distribuer le trafic sur le port TCP 80, ainsi que pour autoriser le trafic Bureau à distance sur le port TCP 3389 et le trafic Accès distant PowerShell sur le port TCP 5985 :

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myScaleSet" `
  -Location "EastUS" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

La création et la configuration de l’ensemble des ressources et des instances de machine virtuelle du groupe identique prennent quelques minutes.


## <a name="view-the-vm-instances-in-a-scale-set"></a>Afficher les instances de machine virtuelle dans un groupe identique
Pour afficher une liste d’instances de machine virtuelle dans un groupe identique, utilisez [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) comme suit :

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

L’exemple suivant illustre deux instances de machine virtuelle dans le groupe identique :

```powershell
ResourceGroupName         Name Location          Sku InstanceID ProvisioningState
-----------------         ---- --------          --- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_0   eastus Standard_DS2          0         Succeeded
MYRESOURCEGROUP   myScaleSet_1   eastus Standard_DS2          1         Succeeded
```

Pour afficher des informations supplémentaires sur une instance spécifique de la machine virtuelle, ajoutez le paramètre `-InstanceId` à [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). L’exemple suivant présente des informations sur l’instance de machine virtuelle *1* :

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="list-connection-information"></a>Répertorier les informations de connexion
Une adresse IP publique est assignée à l’équilibreur de charge qui achemine le trafic vers les instances de machine virtuelle individuelles. Par défaut, des règles de traduction d’adresses réseau (NAT) sont ajoutées à l’équilibreur de charge Azure qui transfère le trafic de connexion distant à chaque machine virtuelle sur un port donné. Pour vous connecter aux instances de machine virtuelle d’un groupe identique, vous créez une connexion distante à une adresse IP publique et à un numéro de port assignés.

Pour répertorier les ports NAT permettant une connexion à des instances de machine virtuelle dans un groupe identique, obtenez tout d’abord l’objet d’équilibrage de charge avec [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Affichez ensuite les règles NAT entrantes avec [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) :

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

L’exemple de sortie suivant affiche le nom de l’instance, l’adresse IP publique de l’équilibreur de charge et le numéro de port vers lequel les règles NAT transfèrent le trafic :

```powershell
Name             Protocol FrontendPort BackendPort
----             -------- ------------ -----------
myScaleSet3389.0 Tcp             50001        3389
myScaleSet5985.0 Tcp             51001        5985
myScaleSet3389.1 Tcp             50002        3389
myScaleSet5985.1 Tcp             51002        5985
```

Le *nom* de la règle s’aligne avec le nom de l’instance de machine virtuelle indiqué dans une commande [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) précédente. Par exemple, pour vous connecter à une instance de machine virtuelle *0*, vous utilisez *myScaleSet3389.0* et vous vous connectez au port *50001*. Pour vous connecter à une instance de machine virtuelle *1*, utilisez la valeur tirée de *myScaleSet3389.1* et connectez-vous au port *50002*. Pour utiliser la communication à distance PowerShell, vous vous connectez à la règle d’instance de machine virtuelle appropriée pour le port *TCP* *5985*.

Affichez l’adresse IP publique de l’équilibreur de charge avec [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name "myPublicIPAddress" | Select IpAddress
```

Exemple de sortie :

```powershell
IpAddress
---------
52.168.121.216
```

Créez une connexion distante vers votre première instance de machine virtuelle. Spécifiez votre adresse IP publique et le numéro de port de l’instance de machine virtuelle, comme indiqué dans les commandes précédentes. Lorsque vous y êtes invité, entrez les informations d’identification utilisées lors de la création du groupe identique (par défaut dans les exemples de commandes, *azureuser* et *P@ssw0rd!*). Si vous utilisez Azure Cloud Shell, effectuez cette étape à partir d’une invite de commandes PowerShell locale ou du client Bureau à distance. L’exemple suivant établit une connexion à l’instance de machine virtuelle *1* :

```powershell
mstsc /v 52.168.121.216:50001
```

Une fois connecté à l’instance de machine virtuelle, vous pouvez effectuer certaines modifications de configuration manuelle, le cas échéant. Pour l’instant, fermez la connexion à distance.


## <a name="understand-vm-instance-images"></a>Comprendre les images d’instance de machine virtuelle
Lorsque vous définissez une configuration d’un groupe identique avec [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) à l’étape précédente, vous avez utilisé une image Windows Server 2016 Datacenter. La Place de marché Azure comprend de nombreuses images qui permettent de créer des instances de machine virtuelle. Pour voir la liste des serveurs de publication disponibles, utilisez la commande [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher).

```azurepowershell-interactive
Get-AzureRmVMImagePublisher -Location "EastUS"
```

Pour afficher une liste d’images pour un serveur de publication donné, utilisez [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). Vous pouvez également utiliser `-PublisherName` ou `–Offer` pour filtrer la liste d’images. Dans l’exemple suivant, la liste est filtrée pour toutes les images avec le nom du serveur de publication de *MicrosoftWindowsServer* et une offre qui correspond à *WindowsServer* :

```azurepowershell-interactive
Get-AzureRmVMImageSku -Location "EastUS" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

L’exemple de sortie suivant illustre toutes les images Windows Server disponibles :

```powershell
Skus                                  Offer         PublisherName          Location
----                                  -----         -------------          --------
2008-R2-SP1                           WindowsServer MicrosoftWindowsServer eastus
2008-R2-SP1-smalldisk                 WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2012-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter                    WindowsServer MicrosoftWindowsServer eastus
2012-R2-Datacenter-smalldisk          WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter                       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core           WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-Server-Core-smalldisk WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-smalldisk             WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-Containers       WindowsServer MicrosoftWindowsServer eastus
2016-Datacenter-with-RDSH             WindowsServer MicrosoftWindowsServer eastus
2016-Nano-Server                      WindowsServer MicrosoftWindowsServer eastus
```

Lorsque vous avez créé un groupe identique au début de ce didacticiel, une image de machine virtuelle par défaut de *Windows Server 2016 DataCenter* a été fournie pour les instances de machine virtuelle. Vous pouvez spécifier une image de machine virtuelle différente en fonction de la sortie de [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku). L’exemple suivant créerait un groupe identique avec le paramètre `-ImageName` pour spécifier une image de machine virtuelle de *MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest*. Comme la création et la configuration de toutes les ressources et les instances de machine virtuelle du groupe identique prennent quelques minutes, vous n’avez pas à déployer le groupe identique suivant :

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup2" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet2" `
  -VirtualNetworkName "myVnet2" `
  -SubnetName "mySubnet2" `
  -PublicIpAddressName "myPublicIPAddress2" `
  -LoadBalancerName "myLoadBalancer2" `
  -UpgradePolicy "Automatic" `
  -ImageName "MicrosoftWindowsServer:WindowsServer:2016-Datacenter-with-Containers:latest" `
  -Credential $cred
```


## <a name="understand-vm-instance-sizes"></a>Comprendre les tailles d’instance de machine virtuelle
Une taille d’instance de machine virtuelle, ou *référence SKU*, détermine la quantité de ressources de calcul comme l’UC, le GPU et la mémoire qui sont mises à la disposition de l’instance de machine virtuelle. Les instances de machine virtuelle doivent être correctement dimensionnées en fonction de la charge de travail attendue.

### <a name="vm-instance-sizes"></a>Taille des instances de machine virtuelle
Le tableau suivant classe les tailles courantes de machine virtuelle en fonction des cas d’usage.

| type                     | Tailles courantes           |    Description       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Usage général](../virtual-machines/windows/sizes-general.md)         |Dsv3, Dv3, DSv2, Dv2, DS, D, Av2, A0-7| Ratio processeur/mémoire équilibré. Idéale pour le développement/test et pour les petites et moyennes applications et solutions de données.  |
| [Optimisé pour le calcul](../virtual-machines/windows/sizes-compute.md)   | Fs, F             | Ratio processeur/mémoire élevé. Convient pour les applications au trafic moyen, les appliances réseau et les processus de traitement par lots.        |
| [Mémoire optimisée](../virtual-machines/windows/sizes-memory.md)    | Esv3, Ev3, M, GS, G, DSv2, DS, Dv2, D   | Ratio mémoire/cœur élevé. Idéale pour les bases de données relationnelles, les caches moyens à grands et l’analytique en mémoire.                 |
| [Optimisé pour le stockage](../virtual-machines/windows/sizes-storage.md)      | Ls                | Débit de disque et E/S élevés. Idéale pour les bases de données NoSQL, SQL et Big Data.                                                         |
| [GPU](../virtual-machines/windows/sizes-gpu.md)          | NV, NC            | Machines virtuelles spécialisées conçues pour les opérations graphiques lourdes et la retouche vidéo.       |
| [Hautes performances](../virtual-machines/windows/sizes-hpc.md) | H, A8-11          | Nos machines virtuelles dotées des processeurs les plus puissants avec interfaces réseau haut débit en option (RDMA). 

### <a name="find-available-vm-instance-sizes"></a>Rechercher des tailles d’instance de machine virtuelle disponibles
Pour afficher la liste des tailles d’instances de machine virtuelle disponibles dans une région particulière, utilisez la commande [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). 

```azurepowershell-interactive
Get-AzureRmVMSize -Location "EastUS"
```

La sortie est semblable à l’exemple condensé suivant, qui présente les ressources assignées à chaque taille de machine virtuelle :

```powershell
Name                   NumberOfCores MemoryInMB MaxDataDiskCount OSDiskSizeInMB ResourceDiskSizeInMB
----                   ------------- ---------- ---------------- -------------- --------------------
Standard_DS1_v2                    1       3584                4        1047552                 7168
Standard_DS2_v2                    2       7168                8        1047552                14336
[...]
Standard_A0                        1        768                1        1047552                20480
Standard_A1                        1       1792                2        1047552                71680
[...]
Standard_F1                        1       2048                4        1047552                16384
Standard_F2                        2       4096                8        1047552                32768
[...]
Standard_NV6                       6      57344               24        1047552               389120
Standard_NV12                     12     114688               48        1047552               696320
```

Lorsque vous avez créé un groupe identique au début de ce didacticiel, la valeur de référence SKU par défaut SKU de la machine virtuelle *Standard_D1_v2* a été fournie pour les instances de machine virtuelle. Vous pouvez spécifier une taille d’instance de machine virtuelle différente en fonction de la sortie de [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize). L’exemple suivant crée un groupe identique avec le paramètre `-VmSize` afin de spécifier une taille d’instance de machine virtuelle *Standard_F1*. Comme la création et la configuration de toutes les ressources et les instances de machine virtuelle du groupe identique prennent quelques minutes, vous n’avez pas à déployer le groupe identique suivant :

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroup3" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet3" `
  -VirtualNetworkName "myVnet3" `
  -SubnetName "mySubnet3" `
  -PublicIpAddressName "myPublicIPAddress3" `
  -LoadBalancerName "myLoadBalancer3" `
  -UpgradePolicy "Automatic" `
  -VmSize "Standard_F1" `
  -Credential $cred
```


## <a name="change-the-capacity-of-a-scale-set"></a>Modifier la capacité d’un groupe identique
Lorsque vous avez créé un groupe identique, vous avez demandé de deux instances de machine virtuelle. Pour augmenter ou diminuer le nombre d’instances dans le groupe identique, vous pouvez modifier manuellement la capacité. Le groupe identique crée ou supprime le nombre requis d’instances de machine virtuelle, puis configure l’équilibreur de charge pour distribuer le trafic.

Commencez par créer un objet de groupe identique avec [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), puis spécifiez une nouvelle valeur pour `sku.capacity`. Pour appliquer la modification de la capacité, utilisez [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). L’exemple suivant fixe le nombre d’instances de machine virtuelle présentes dans votre groupe identique à *3* :

```azurepowershell-interactive
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Quelques minutes sont nécessaires pour mettre à jour la capacité de votre groupe identique. Pour afficher le nombre d’instances désormais présentes dans le groupe identique, utilisez [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) :

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

L’exemple de sortie suivant montre que la capacité du groupe identique est désormais *3* :

```powershell
Sku        :
  Name     : Standard_DS2
  Tier     : Standard
  Capacity : 3
```


## <a name="common-management-tasks"></a>Tâches de gestion courantes
Vous pouvez à présent créer un groupe identique, répertorier les informations de connexion et vous connecter aux instances de machine virtuelle. Vous avez appris à utiliser une autre image de système d’exploitation pour vos instances de machine virtuelle, sélectionner une autre taille de machine virtuelle et mettre à l’échelle manuellement le nombre d’instances. Dans le cadre de la gestion quotidienne, vous devrez peut-être arrêter, démarrer ou redémarrer les instances de machine virtuelle dans votre groupe identique.

### <a name="stop-and-deallocate-vm-instances-in-a-scale-set"></a>Arrêter et libérer des instances de machine virtuelle dans un groupe identique
Pour arrêter une ou plusieurs machines virtuelles dans un groupe identique, utilisez [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). Le paramètre `-InstanceId` vous permet de spécifier une ou plusieurs machines virtuelles à arrêter. Si vous ne spécifiez pas d’ID d’instance, toutes les machines virtuelles dans le groupe identique sont arrêtées. L’exemple suivant arrête l’instance *1* :

```azurepowershell-interactive
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

Par défaut, les machines virtuelles arrêtées sont libérées et n’entraînent pas de frais de calcul. Si vous souhaitez que la machine virtuelle reste dans un état configuré lors de l’arrêt, ajoutez le paramètre `-StayProvisioned` à la commande précédente. Les machines virtuelles arrêtées qui conservent leur configuration peuvent occasionner des frais de calcul standard.

### <a name="start-vm-instances-in-a-scale-set"></a>Démarrer les instances de machine virtuelle dans un groupe identique
Pour démarrer une ou plusieurs machines virtuelles dans un groupe identique, utilisez [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). Le paramètre `-InstanceId` vous permet de spécifier une ou plusieurs machines virtuelles à démarrer. Si vous ne spécifiez pas d’ID d’instance, toutes les machines virtuelles dans le groupe identique sont démarrées. L’exemple suivant démarre l’instance *1* :

```azurepowershell-interactive
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```

### <a name="restart-vm-instances-in-a-scale-set"></a>Redémarrer les instances de machine virtuelle dans un groupe identique
Pour redémarrer une ou plusieurs machines virtuelles dans un groupe identique, utilisez [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). Le paramètre `-InstanceId` vous permet de spécifier une ou plusieurs machines virtuelles à redémarrer. Si vous ne spécifiez pas d’ID d’instance, toutes les machines virtuelles dans le groupe identique sont redémarrées. L’exemple suivant redémarre l’instance *1* :

```azurepowershell-interactive
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous supprimez un groupe de ressources, toutes les ressources qu’il contient, comme les instances de machine virtuelle, le réseau virtuel et les disques, sont également supprimées. Le paramètre `-Force` confirme que vous souhaitez supprimer les ressources sans passer par une invite supplémentaire à cette fin. Le paramètre `-AsJob` retourne le contrôle à l’invite de commandes sans attendre que l’opération se termine.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à effectuer quelques tâches de création et de gestion de base concernant le groupe identique avec Azure PowerShell :

> [!div class="checklist"]
> * Créer et connecter un groupe de machines virtuelles identiques
> * Sélectionner et utiliser des images de machine virtuelle
> * Afficher et utiliser des tailles de machine virtuelle spécifiques
> * Mettre manuellement à l’échelle un groupe identique
> * Effectuer des tâches courantes de gestion de groupe identique

Passez au didacticiel suivant pour en savoir plus sur les disques de groupe identique.

> [!div class="nextstepaction"]
> [Utiliser des disques de données avec des groupes identiques](tutorial-use-disks-powershell.md)
