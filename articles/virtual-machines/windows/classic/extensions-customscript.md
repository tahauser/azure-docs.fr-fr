---
title: "Extension de script personnalisé sur une machine virtuelle Windows | Microsoft Docs"
description: "Automatisation des tâches de configuration de machine virtuelle Azure à l’aide de l’extension de script personnalisé pour exécuter des scripts PowerShell sur une machine virtuelle Windows distante"
services: virtual-machines-windows
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: danis
ms.openlocfilehash: 5de2949561ed5f64582898006de7818e81f79f5b
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2017
---
# <a name="custom-script-extension-for-windows-using-the-classic-deployment-model"></a>Extension de script personnalisé pour Windows à l’aide du modèle de déploiement classique

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [le déploiement Resource Manager et le déploiement classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](../extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

L’extension de script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension. L’extension de script personnalisé s’intègre aux modèles Azure Resource Manager et peut être exécutée à l’aide de l’interface de ligne de commande Azure, de PowerShell, du portail Azure ou de l’API REST de machine virtuelle Azure.

Ce document explique en détail l’utilisation de l’extension de script personnalisé à l’aide du module Azure PowerShell, des modèles Azure Resource Manager, et détaille également les étapes de résolution de problèmes sur les systèmes Windows.

## <a name="prerequisites"></a>Composants requis

### <a name="operating-system"></a>Système d’exploitation

L’extension de script personnalisé pour Windows peut être exécutée sur Windows Server 2008 R2, 2012, 2012 R2 et 2016.

### <a name="script-location"></a>Emplacement du script

Le script doit être stocké dans le stockage Azure ou tout autre emplacement accessible via une URL valide.

### <a name="internet-connectivity"></a>Connectivité Internet

L’extension de script personnalisé pour Windows nécessite que la machine virtuelle cible soit connectée à Internet. 

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma pour l’extension de script personnalisé. L’extension nécessite un emplacement de script (stockage Azure ou autre emplacement disposant d’une URL valide) et une commande à exécuter. Si vous utilisez le stockage Azure comme source de script, une clé de compte et un nom de compte de stockage Azure sont requis. Ces éléments doivent être traités comme des données sensibles et spécifiés dans la configuration du paramètre de protection des extensions. Les données du paramètre de protection de l’extension de machine virtuelle Azure sont chiffrées et ne sont déchiffrées que sur la machine virtuelle cible.

```json
{
    "name": "config-app",
    "type": "Microsoft.ClassicCompute/virtualMachines/extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-01",
    "properties": {
        "publisher": "Microsoft.Compute",
        "extension": "CustomScriptExtension",
        "version": "1.8",
        "parameters": {
            "public": {
                "fileUris": "[myScriptLocation]"
            },
            "private": {
                "commandToExecute": "[myExecutionString]"
            }
        }
    }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| Nom | Valeur/Exemple |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Compute |
| extension | CustomScriptExtension |
| typeHandlerVersion | 1.8 |
| fileUris (p. ex.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (p. ex.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 |

## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Le schéma JSON détaillé dans la section précédente peut être utilisé dans un modèle Azure Resource Manager pour exécuter l’extension de script personnalisé pendant un déploiement de modèle Azure Resource Manager. Un exemple de modèle qui inclut l’extension de script personnalisé est disponible ici, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Déploiement PowerShell

Vous pouvez utiliser la commande `Set-AzureVMCustomScriptExtension` pour ajouter l’extension de script personnalisé sur une machine virtuelle existante. Pour plus d’informations, consultez [Set-AzureRmVMCustomScriptExtension](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.1.0/set-azurermvmcustomscriptextension).

```powershell
# create vm object
$vm = Get-AzureVM -Name 2016clas -ServiceName 2016clas1313

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri myFileUri -Run 'create-file.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide du module Azure PowerShell. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante.

```powershell
Get-AzureVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

La sortie de l’exécution de l’extension est enregistrée dans les fichiers qui se trouvent dans le répertoire suivant sur la machine virtuelle.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Le script proprement dit est téléchargé dans le répertoire suivant sur la machine virtuelle cible.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/en-us/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).