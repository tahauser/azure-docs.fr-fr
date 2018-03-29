---
title: Extension de machine virtuelle Agent Azure Network Watcher pour Linux | Microsoft Docs
description: Déployez l’Agent Network Watcher sur une machine virtuelle Linux avec une extension de machine virtuelle.
services: virtual-machines-linux
documentationcenter: ''
author: dennisg
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: a51566148eec88f707b524346afc59606196e4da
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Extension de machine virtuelle Agent Network Watcher pour Linux

## <a name="overview"></a>Vue d'ensemble

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/) est un service d’analyse, de diagnostic et d’analytique des performances réseau permettant de surveiller les réseaux Azure. L’extension de machine virtuelle Agent Network Watcher est requise pour certaines fonctionnalités de Network Watcher sur les machines virtuelles Azure, notamment la capture du trafic réseau à la demande et d’autres fonctionnalités avancées.

Ce document présente les plateformes et options de déploiement prises en charge pour l’extension de machine virtuelle Agent Network Watcher pour Linux.

## <a name="prerequisites"></a>Prérequis


### <a name="operating-system"></a>Système d’exploitation

L’extension Agent Network Watcher peut être exécutée sur ces distributions Linux :

| Distribution | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS et 12.04 LTS |
| Debian | 7 et 8 |
| Red Hat | 6.x et 7.x |
| Oracle Linux | 7x |
| SUSE | 11 et 12 |
| openSUSE | 7.0 |
| CentOS | 7.0 |

Remarque : CoreOS n’est pas pris en charge pour le moment.

### <a name="internet-connectivity"></a>Connectivité Internet

Certaines fonctionnalités de l’Agent Network Watcher requièrent que la machine virtuelle cible soit connectée à Internet. Sans la possibilité d’établir des connexions sortantes, certaines fonctionnalités de l’Agent Network Watcher risquent de mal fonctionner ou de devenir indisponibles. Pour plus d’informations, consultez la [documentation de Network Watcher](https://review.docs.microsoft.com/azure/network-watcher/).

## <a name="extension-schema"></a>Schéma d’extensions

Le JSON suivant illustre le schéma de l’extension Agent Network Watcher. L’extension ne requiert ni ne prend en charge aucun paramètre fourni par l’utilisateur pour l’instant et s’appuie sur sa configuration par défaut.

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
        "type": "NetworkWatcherAgentLinux",
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
| Type | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>Déploiement de modèle

Les extensions de machines virtuelles Azure peuvent être déployées avec des modèles Azure Resource Manager. Le schéma JSON détaillé dans la section précédente peut être utilisé dans un modèle Azure Resource Manager pour exécuter l’extension Agent Network Watcher pendant un déploiement de modèle Azure Resource Manager.

## <a name="azure-cli-deployment"></a>Déploiement de l’interface de ligne de commande Azure

Vous pouvez utiliser l’interface de ligne de commande Azure pour déployer l’extension de machine virtuelle Agent Network Watcher sur une machine virtuelle existante.

```azurecli
azure vm extension set myResourceGroup1 myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="troubleshooting-and-support"></a>Résolution des problèmes et support

### <a name="troubleshooting"></a>Résolution de problèmes

Vous pouvez récupérer les données sur l’état des déploiements d’extension à partir du portail Azure et à l’aide de l’interface de ligne de commande Azure. Pour afficher l’état du déploiement des extensions pour une machine virtuelle donnée, exécutez la commande suivante à l’aide de l’interface de ligne de commande Azure.

```azurecli
azure vm extension get myResourceGroup1 myVM1
```

La sortie de l’exécution de l’extension est enregistrée dans les fichiers qui que se trouvent dans le répertoire suivant :

`
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
`

### <a name="support"></a>Support

Si vous avez besoin d’une aide supplémentaire à quelque étape que ce soit dans cet article, vous pouvez consulter la documentation de Network Watcher ou contacter les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/en-us/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/en-us/support/options/) , puis cliquez sur Obtenir un support. Pour plus d’informations sur l’utilisation du support Azure, lisez le [FAQ du support Microsoft Azure](https://azure.microsoft.com/en-us/support/faq/).
