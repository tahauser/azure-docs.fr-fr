---
title: Extension de machine virtuelle Agent Azure Network Watcher pour Windows | Microsoft Docs
description: "Déployez l’Agent Network Watcher sur une machine virtuelle Windows avec une extension de machine virtuelle."
services: virtual-machines-windows
documentationcenter: 
author: dennisg
manager: amku
editor: 
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 68855e0070916dc672914fbc8ca3587a5d3c25f6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/20/2017
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Extension de machine virtuelle Agent Network Watcher pour Windows

## <a name="overview"></a>Vue d’ensemble

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) est un service d’analyse, de diagnostic et d’analytique des performances réseau permettant de surveiller les réseaux Azure. L’extension de machine virtuelle Agent Network Watcher est obligatoire pour capturer le trafic réseau à la demande et pour assurer la prise en charge d’autres fonctionnalités avancées sur les machines virtuelles Azure.


Ce document présente les plateformes et options de déploiement prises en charge pour l’extension de machine virtuelle Agent Network Watcher pour Windows.

## <a name="prerequisites"></a>Prérequis

### <a name="operating-system"></a>Système d’exploitation

L’extension Agent Network Watcher pour Windows peut être exécutée sur Windows Server 2008 R2, 2012, 2012 R2 et 2016. Nano Server n’est pas pris en charge.

### <a name="internet-connectivity"></a>Connectivité Internet

Certaines fonctionnalités de l’Agent Network Watcher requièrent que la machine virtuelle cible soit connectée à Internet. Sans la capacité d’établir des connexions sortantes, l’Agent Network Watcher ne sera pas en mesure de charger des captures de paquets sur votre compte de stockage. Pour plus d’informations, consultez la [documentation de Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md).

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma de l’extension Agent Network Watcher. L’extension ne requiert ni ne prend en charge aucun paramètre fourni par l’utilisateur et s’appuie sur sa configuration par défaut.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Valeurs de propriétés

| NOM | Valeur/Exemple |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| Type | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Déploiement de modèle

Vous pouvez déployer les extensions de machines virtuelles Azure avec des modèles Azure Resource Manager. Vous pouvez utiliser le schéma JSON détaillé dans la section précédente dans un modèle Azure Resource Manager pour exécuter l’extension Agent Network Watcher pendant un déploiement de modèle Azure Resource Manager.

## <a name="powershell-deployment"></a>Déploiement PowerShell

Utilisez la commande `Set-AzureRmVMExtension` pour déployer l’extension de machine virtuelle Agent Network Watcher sur une machine virtuelle existante :

```powershell
Set-AzureRmVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Résolution des problèmes et support

### <a name="troubleshooting"></a>Résolution de problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide de PowerShell. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide du module Azure PowerShell :

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

La sortie de l’exécution de l’extension est enregistrée dans les fichiers qui que se trouvent dans le répertoire suivant :

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez consulter la documentation Guide d’utilisation de Network Watcher ou contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/en-us/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).
