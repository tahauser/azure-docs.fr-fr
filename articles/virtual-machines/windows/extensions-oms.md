---
title: Extension de machine virtuelle OMS Azure pour Windows | Microsoft Docs
description: Déployez l’Agent OMS sur une machine virtuelle Windows avec une extension de machine virtuelle.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: feae6176-2373-4034-b5d9-a32c6b4e1f10
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danis
ms.openlocfilehash: 9b090029ade1cab1d0be17f7622ecf622521fc14
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2018
---
# <a name="oms-virtual-machine-extension-for-windows"></a>Extension de machine virtuelle OMS pour Windows

Operations Management Suite (OMS) fournit des fonctionnalités de surveillance, d’alerte et de correction des alertes sur le ressources cloud et locales. L’extension de machine virtuelle de l’agent OMS pour Windows est publiée et prise en charge par Microsoft. L’extension installe l’agent OMS sur les machines virtuelles Azure et inscrit les machines virtuelles dans un espace de travail OMS existant. Ce document présente les plateformes, configurations et options de déploiement prises en charge pour l’extension de machine virtuelle OMS pour Windows.

## <a name="prerequisites"></a>Prérequis


### <a name="operating-system"></a>Système d’exploitation

L’extension de l’agent OMS pour Windows peut être exécutée sur Windows Server 2008 R2, 2012, 2012 R2 et 2016.

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center configure automatiquement l’agent OMS et le connecte à l’espace de travail Log Analytics par défaut de l’abonnement Azure. Si vous utilisez Azure Security Center, ne suivez pas la procédure de ce document. Si vous le faites, vous écrasez l’espace de travail configuré et interrompez la connexion à Azure Security Center.

### <a name="internet-connectivity"></a>Connectivité Internet
L’extension de l’agent OMS pour Windows nécessite que la machine virtuelle cible soit connectée à Internet. 

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma pour l’extension d’agent OMS. L’extension nécessite l’ID et la clé de l’espace de travail OMS cible, qui sont disponibles dans le portail OMS. La clé de l’espace de travail devant être traitée comme une donnée sensible, elle est stockée dans une configuration protégée. Les données du paramètre de protection de l’extension de machine virtuelle Azure sont chiffrées et ne sont déchiffrées que sur la machine virtuelle cible. Notez que **workspaceId** et **workspaceKey** respectent la casse.

```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```
### <a name="property-values"></a>Valeurs de propriétés

| NOM | Valeur/Exemple |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.EnterpriseCloud.Monitoring |
| Type | MicrosoftMonitoringAgent |
| typeHandlerVersion | 1.0 |
| workspaceId (par exemple) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (par exemple) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI+rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ== |

## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Le schéma JSON détaillé dans la section précédente peut être utilisé dans un modèle Azure Resource Manager pour exécuter l’extension d’agent OMS pendant un déploiement de modèle Azure Resource Manager. Vous trouverez un exemple de modèle qui inclut l’extension de machine virtuelle d’agent OMS dans la [galerie de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-windows-vm). 

Le code JSON pour une extension de machine virtuelle peut être imbriqué à l’intérieur de la ressource de machine virtuelle ou placé à la racine ou au niveau supérieur d’un modèle de Resource Manager JSON. Le positionnement du JSON affecte la valeur du nom de la ressource et son type. Pour plus d’informations, consultez [Définition du nom et du type des ressources enfants](../../azure-resource-manager/resource-manager-templates-resources.md#child-resources). 

L’exemple suivant suppose que l’extension OMS est imbriquée dans la ressource de machine virtuelle. Lors de l’imbrication de la ressource d’extension, le JSON est placé dans l’objet `"resources": []` de la machine virtuelle.


```json
{
    "type": "extensions",
    "name": "OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

Lorsque vous placez l’extension JSON à la racine du modèle, le nom de ressource inclut une référence à la machine virtuelle parente, et le type reflète la configuration imbriquée. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/OMSExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "workspaceId": "myWorkSpaceId"
        },
        "protectedSettings": {
            "workspaceKey": "myWorkspaceKey"
        }
    }
}
```

## <a name="powershell-deployment"></a>Déploiement PowerShell

Vous pouvez utiliser la commande `Set-AzureRmVMExtension` pour déployer l’extension de machine virtuelle d’agent OMS sur une machine virtuelle existante. Avant d’exécuter la commande, les configurations publique et privée doivent être stockées dans une table de hachage PowerShell. 

```powershell
$PublicSettings = @{"workspaceId" = "myWorkspaceId"}
$ProtectedSettings = @{"workspaceKey" = "myWorkspaceKey"}

Set-AzureRmVMExtension -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
    -ExtensionType "MicrosoftMonitoringAgent" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS 
```

## <a name="troubleshoot-and-support"></a>Dépannage et support technique

### <a name="troubleshoot"></a>Résolution des problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide du module Azure PowerShell. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide du module PowerShell.

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

La sortie de l’exécution de l’extension est enregistrée dans les fichiers qui que se trouvent dans le répertoire suivant :

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent\
```

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/en-us/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).
