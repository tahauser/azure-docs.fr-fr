---
title: "Azure Hybrid Benefit pour Windows Server | Microsoft Docs"
description: "Découvrez comment optimiser les avantages de votre contrat Software Assurance pour Windows pour mettre des licences locales sur Azure"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss
ms.openlocfilehash: 245bffbc208ce67d990a63e744c42dc671686b4b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit pour Windows Server
Pour les clients avec Software Assurance, Azure Hybrid Benefit pour Windows Server permet d’utiliser les licences Windows Server locales et d’exécuter des machines virtuelles Windows sur Azure à moindre coût. Vous pouvez utiliser Azure Hybrid Benefit pour Windows Server pour déployer de nouvelles machines virtuelles à partir de n’importe quelle image Windows Server ou image personnalisée Windows de plateforme Azure prise en charge. Cet article récapitule les étapes du déploiement de nouvelles machines virtuelles avec Azure Hybrid Benefit pour Windows Server. Il explique également comment mettre à jour des machines virtuelles existantes en cours d’exécution. Pour plus d’informations sur les licences et les réductions de coût relatives à Azure Hybrid Benefit pour Windows Server, consultez la [page de gestion des licences Azure Hybrid Benefit pour Windows Server](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

> [!IMPORTANT]
> Les images Windows Server '[HUB]' héritées qui étaient publiées pour les clients avec un Contrat Entreprise sur la Place de marché Azure ont été retirées depuis le 11/09/2017. Utilisez Windows Server standard avec l’option « Save Money » sur le portail pour Azure Hybrid Benefit pour Windows Server. Pour plus d’informations, reportez-vous à cet [article](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions).
>

> [!NOTE]
> L’utilisation d’Azure Hybrid Benefit pour Windows Server avec des machines virtuelles faisant l’objet d’une facturation pour des logiciels supplémentaires, comme SQL Server ou l’une des images de place de marché tierce, est en cours de déploiement. Si vous obtenez une erreur 409 (par exemple, Vous n’êtes pas autorisé à changer la propriété « LicenseType »), cela indique que vous essayez de convertir ou de déployer une nouvelle machine virtuelle Windows Server impliquant des coûts logiciels supplémentaires, situation qui n’est peut-être pas prise en charge dans la région concernée. Il en va de même si vous tentez de rechercher l’option de configuration du portail pour effectuer la conversion et que cette option ne s’affiche pas pour cette machine virtuelle.
>


> [!NOTE]
> Pour les machines virtuelles classiques, seul le déploiement d’une nouvelle machine virtuelle à partir des images personnalisées locales est pris en charge. Pour tirer parti des fonctionnalités prises en charge dans cet article, vous devez d’abord migrer des machines virtuelles classiques vers le modèle Resource Manager.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Façons d’utiliser Azure Hybrid Benefit pour Windows Server
Il existe plusieurs façons d’utiliser des machines virtuelles Windows avec Azure Hybrid Benefit :

1. Vous pouvez déployer des machines virtuelles à partir de l’une des [images Windows Server fournies sur la Place de marché Microsoft Azure](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)
2. Vous pouvez [charger une machine virtuelle personnalisée](#upload-a-windows-vhd) et [effectuer un déploiement à l’aide d’un modèle Resource Manager](#deploy-a-vm-via-resource-manager) ou [Azure PowerShell](#detailed-powershell-deployment-walkthrough)
3. Vous pouvez convertir une machine virtuelle existante de sorte qu’elle s’exécute avec Azure Hybrid Benefit ou selon un paiement à l’utilisation pour Windows Server ou basculer entre les deux méthodes.
4. Vous pouvez également déployer un nouveau groupe de machines virtuelles identiques avec Azure Hybrid Benefit pour Windows Server

> [!NOTE]
> La conversion d’un groupe de machines virtuelles identiques pour utiliser Azure Hybrid Benefit pour Windows Server n’est pas prise en charge.
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Déployer une machine virtuelle à partir d’une image de la Place de marché Windows Server
Toutes les images Windows Server disponibles à partir de la Place de marché Microsoft Azure sont activées avec Azure Hybrid Benefit pour Windows Server. Par exemple, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 et Windows Server 2008 SP1 entre autres. Vous pouvez utiliser ces images pour déployer des machines virtuelles directement à partir du portail Azure, de modèles Resource Manager, d’Azure PowerShell ou d’autres SDK.

Vous pouvez déployer ces images directement à partir du portail Azure. Pour une utilisation dans des modèles Resource Manager et avec Azure PowerShell, affichez la liste des images comme suit :

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
Vous pouvez suivre les étapes pour [créer une machine virtuelle Windows avec PowerShell](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) et passer LicenseType = "Windows_Server". Cette option vous permet d’utiliser votre licence Windows Server actuelle sur Azure.

### <a name="portal"></a>Portail
Vous pouvez suivre les étapes pour [créer une machine virtuelle Windows avec le portail Azure](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) et sélectionner l’option permettant d’utiliser votre licence Windows Server existante.

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Convertir une machine virtuelle existante pour utiliser Azure Hybrid Benefit pour Windows Server
Si vous souhaitez convertir une machine virtuelle existante de sorte qu’elle puisse tirer parti d’Azure Hybrid Benefit pour Windows Server, vous pouvez mettre à jour le type de licence de votre machine virtuelle comme suit :

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>Conversion pour utiliser Azure Hybrid Benefit pour Windows Server
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>Retour au paiement à l’utilisation
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>Portail
Dans le panneau de la machine virtuelle du Portail, vous pouvez mettre à jour la machine virtuelle pour utiliser Azure Hybrid Benefit en sélectionnant l’option « Configuration », puis en activant l’option « Azure Hybrid Benefit ».

> [!NOTE]
> Si l’option permettant d’activer ou de désactiver « Azure Hybrid Benefit » ne s’affiche pas sous « Configuration », cela est dû au fait que la conversion n’est pas encore prise en charge pour le type de machine virtuelle sélectionné (par exemple, une machine virtuelle générée à partir d’une image personnalisée ou à partir d’une image dotée de logiciels payants supplémentaires comme SQL Sever ou Place de marché Azure).
>

## <a name="upload-a-windows-server-vhd"></a>Téléchargement d’un disque dur virtuel Windows Server
Pour déployer une machine virtuelle Windows Server dans Azure, vous devez d’abord créer un disque dur virtuel contenant votre build Windows de base. Ce disque dur virtuel doit être correctement préparé par Sysprep avant son téléchargement sur Azure. Découvrez [plus d’information sur la configuration requise du disque dur virtuel et le processus Sysprep](upload-generalized-managed.md) et la [prise en charge de Sysprep pour les rôles serveur](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Sauvegardez la machine virtuelle avant d’exécuter Sysprep. 

Une fois votre disque dur virtuel préparé, chargez-le dans votre compte de stockage Azure comme suit :

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint Server et Dynamics peuvent également utiliser vos licences Software Assurance. Vous devez encore préparer l’image Windows Server en installant vos composants d’application et en fournissant les clés de licence appropriées, puis en chargeant l’image de disque vers Azure. Passez en revue la documentation appropriée pour l’exécution de Sysprep avec votre application, telle que [Considérations relatives à l’installation de SQL Server à l’aide de Sysprep](https://msdn.microsoft.com/library/ee210754.aspx) ou [Build a SharePoint Server 2016 Reference Image (Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx) (Créer une image de référence SharePoint Server 2016 (Sysprep)).
>
>

Pour en savoir plus sur le chargement du disque dur virtuel vers Azure, [cliquez ici](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account).

## <a name="deploy-a-vm-via-resource-manager-template"></a>Déployer une machine virtuelle à l’aide du modèle Resource Manager
Dans vos modèles Resource Manager, vous pouvez spécifier un paramètre supplémentaire `licenseType`. Pour en savoir plus sur la création de modèles Azure Resource Manager, [cliquez ici](../../resource-group-authoring-templates.md). Une fois que votre disque dur virtuel téléchargé dans Azure, modifiez votre modèle Resource Manager pour inclure le type de licence dans le fournisseur de calcul et déployez votre modèle normalement :

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>Déployer une machine virtuelle à l’aide du démarrage rapide de PowerShell
Lors du déploiement de votre machine virtuelle Windows Server par le biais de PowerShell, vous disposez d’un paramètre supplémentaire `-LicenseType`. Une fois votre disque dur virtuel chargé dans Azure, vous créez une machine virtuelle en utilisant `New-AzureRmVM` et spécifiez le type de licence comme suit :

Pour Windows Server :
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

Vous pouvez consulter un guide plus descriptif sur les différentes étapes permettant de [créer une machine virtuelle Windows à l’aide de Resource Manager et PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Vérifiez que votre machine virtuelle utilise l’avantage de licence
Une fois votre machine virtuelle déployée par le biais de PowerShell, d’un modèle Resource Manager ou du portail, vous pouvez vérifier le type de licence avec `Get-AzureRmVM` comme suit :

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Le résultat ressemble à l’exemple suivant pour Windows Server :

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Ce résultat contraste avec la machine virtuelle suivante déployée sans licence Azure Hybrid Benefit pour Windows Server :

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>Répertorier toutes les machines virtuelles Azure Hybrid Benefit pour Windows Server d’un abonnement

Pour afficher et compter toutes les machines virtuelles déployées avec Azure Hybrid Benefit pour Windows Server, vous pouvez exécuter la commande suivante à partir de votre abonnement :

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Déployer un groupe de machines virtuelles identiques avec Azure Hybrid Benefit pour Windows Server
Au sein des modèles Resource Manager de votre groupe de machines virtuelles identiques, vous devez spécifier un paramètre supplémentaire `licenseType`. Pour en savoir plus sur la création de modèles Azure Resource Manager, [cliquez ici](../../resource-group-authoring-templates.md). Modifiez votre modèle Resource Manager afin d’inclure la propriété licenseType dans le cadre du paramètre virtualMachineProfile du groupe identique, puis déployez votre modèle comme d’habitude. Consultez l’exemple d’utilisation d’une image de Windows Server 2016 suivant :


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
Vous pouvez également [créer et déployer un groupe de machines virtuelles identiques](#https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) et définir la propriété LicenseType.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [réaliser des économies avec Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/).

Consultez les [instructions détaillées sur la gestion des licences avec Azure Hybrid Benefit pour Windows Server](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit).

En savoir plus sur [l’utilisation de modèles Resource Manager](../../azure-resource-manager/resource-group-overview.md).

Découvrez comment [Azure Hybrid Benefit pour Windows Server et Azure Site Recovery rendent la migration d’applications vers Azure encore plus rentable](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/).

En savoir plus sur [Windows 10 sur Azure avec des droits d’hébergement multilocataire](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment).

Obtenir plus d’informations dans le [Forum Aux Questions](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/)
