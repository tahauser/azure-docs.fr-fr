---
title: "Extensions et fonctionnalités de machine virtuelle pour Linux | Microsoft Docs"
description: "Découvrez les extensions disponibles pour les machines virtuelles, regroupées par ce qu’ils fournissent ou améliorent."
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 30e606154830b867382ea1ea439b97749370a1f6
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2018
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Extensions et fonctionnalités de machine virtuelle pour Linux

Les extensions de machine virtuelle Azure sont de petites applications permettant d’exécuter des tâches de configuration et d’automatisation post-déploiement sur des machines virtuelles Azure. Par exemple, si une machine virtuelle requiert l’installation d’un logiciel, une protection antivirus ou une configuration de Docker, il est possible d’effectuer ces tâches à l’aide d’une extension de machine virtuelle. Les extensions de machine virtuelle Azure peuvent être exécutées à l’aide de l’interface de ligne de commande Azure, de PowerShell, de modèles Azure Resource Manager et du portail Azure. Les extensions peuvent être associées à un nouveau déploiement de machine virtuelle ou s’exécuter sur tout système existant.

Ce document offre une vue d’ensemble des extensions de machine virtuelle et des composants requis pour utiliser les extensions de machine virtuelle Azure. Il explique également comment détecter, gérer et supprimer les extensions de machine virtuelle. Ce document fournit des informations générales, car de nombreuses extensions de machine virtuelle sont disponibles, chacune présentant une configuration potentiellement unique. Vous trouverez des informations détaillées sur une extension spécifique dans la documentation consacrée à celle-ci.

## <a name="use-cases-and-samples"></a>Cas d’utilisation et exemples

Plusieurs extensions de machine virtuelle Azure sont disponibles, chacune impliquant un cas d’utilisation spécifique. Voici quelques exemples :

- Appliquer des configurations d’état souhaité PowerShell à une machine virtuelle à l’aide de l’extension DSC pour Linux. Pour plus d’informations sur l’extension DSC Azure, consultez [cette page](https://github.com/Azure/azure-linux-extensions/tree/master/DSC) (en anglais).
- Configurer l’analyse d’une machine virtuelle avec l’extension de machine virtuelle Microsoft Monitoring Agent. Pour plus d’informations, voir [How to monitor a Linux VM](tutorial-monitoring.md) (Surveillance d’une machine virtuelle Linux).
- Configurer l’analyse de votre infrastructure Azure à l’aide de l’extension Datadog. Pour plus d’informations, consultez le [blog Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).
- Configurer un hôte Docker sur une machine virtuelle Azure avec l’extension de machine virtuelle Docker. Pour plus d’informations sur l’extension de machine virtuelle Docker, consultez [cet article](dockerextension.md).

En plus des extensions propres à des processus, une extension de script personnalisé est disponible pour les machines virtuelles Windows et Linux. L’extension de script personnalisé pour Linux permet d’exécuter n’importe quel script Bash sur une machine virtuelle. Les scripts personnalisés s’avèrent utile pour concevoir des déploiements Azure qui nécessitent une configuration plus avancée que celle fournie par les outils Azure natifs. Pour plus d’informations sur l’extension de script personnalisé pour les machines virtuelles Linux, consultez [cet article](extensions-customscript.md).


## <a name="prerequisites"></a>configuration requise

Chaque extension de machine virtuelle peut présenter son propre ensemble de composants requis. Par exemple, l’extension de machine virtuelle Docker nécessite une distribution Linux compatible. Les composants requis pour une extension spécifique sont présentés en détail dans la documentation consacrée à celle-ci.

### <a name="azure-vm-agent"></a>Agent de machine virtuelle Azure

L’agent de machine virtuelle Azure gère l’interaction entre une machine virtuelle et le contrôleur de structure Azure. L’agent de machine virtuelle est responsable de nombreux aspects fonctionnels liés au déploiement et à la gestion des machines virtuelles Azure, dont les extensions de machine virtuelle en cours d’exécution. L’agent de machine virtuelle Azure est préinstallé sur les images de la Place de marché Azure et peut être installé manuellement sur les systèmes d’exploitation pris en charge.

Pour plus d’informations sur les systèmes d’exploitation pris en charge et sur la procédure d’installation, consultez l’article [Agent de machine virtuelle et extensions Azure](agent-user-guide.md).

## <a name="discover-vm-extensions"></a>Détecter les extensions de machine virtuelle

De nombreuses extensions de machine virtuelle différentes peuvent être utilisées avec les machines virtuelles Azure. Pour en obtenir la liste complète, exécutez la commande suivante avec l’interface de ligne de commande Azure, en remplaçant l’exemple d’emplacement par l’emplacement de votre choix.

```azurecli
az vm extension image list --location westus -o table
```

## <a name="run-vm-extensions"></a>Exécuter les extensions de machine virtuelle

Les extensions de machine virtuelle Azure peuvent être exécutées sur des machines virtuelles existantes. Elles s’avèrent utiles quand vous devez apporter des modifications de configuration ou restaurer la connectivité sur une machine virtuelle déjà déployée. Les extensions de machines virtuelles peuvent également être intégrées dans des déploiements de modèles Azure Resource Manager. L’utilisation d’extensions avec des modèles Resource Manager permet de déployer et de configurer des machines virtuelles Azure sans avoir à intervenir après le déploiement.

Les méthodes suivantes peuvent être utilisées pour exécuter une extension sur une machine virtuelle existante.

### <a name="azure-cli"></a>Azure CLI

Les extensions de machine virtuelle peuvent être exécutées sur une machine virtuelle existante à l’aide de la commande `az vm extension set`. Cet exemple exécute l’extension de script personnalisé sur une machine virtuelle.

```azurecli
az vm extension set `
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Le script génère une sortie semblable au texte suivant :

```azurecli
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portail Azure

Les extensions de machines virtuelles peuvent être appliquées à une machine virtuelle existante via le portail Azure. Pour ce faire, sélectionnez la machine virtuelle, choisissez **Extensions**, puis cliquez sur **Ajouter**. Sélectionnez l’extension souhaitée dans la liste des extensions disponibles, puis suivez les instructions de l’Assistant.

L’image suivante illustre l’installation de l’extension de script personnalisé Linux à partir du portail Azure.

![Installer l’extension de script personnalisé](./media/extensions-features/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Modèles Microsoft Azure Resource Manager

Les extensions de machine virtuelle peuvent être ajoutées à un modèle Azure Resource Manager et exécutées avec le déploiement du modèle. Lorsque vous déployez une extension avec un modèle, vous pouvez créer des déploiements Azure entièrement configurés. Par exemple, le code JSON suivant est tiré partir d’un modèle Resource Manager. Ce modèle déploie un ensemble de machines virtuelles à charge équilibrée et une base de données SQL Azure, puis installe une application .NET Core sur chaque machine virtuelle. L’extension de machine virtuelle se charge de l’installation du logiciel.

Pour plus d’informations, consultez le [modèle Resource Manager complet](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Pour plus d’informations, consultez [Création de modèles Azure Resource Manager](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#extensions).

## <a name="secure-vm-extension-data"></a>Sécuriser les données des extensions de machine virtuelle

Lorsque vous exécutez une extension de machine virtuelle, vous pouvez avoir besoin d’inclure des informations sensibles telles que des informations d’identification, des noms de compte de stockage et des clés d’accès à des comptes de stockage. De nombreuses extensions de machine virtuelle comprennent une configuration protégée qui chiffre les données et les déchiffre uniquement à l’intérieur de la machine virtuelle cible. Chaque extension possède un schéma spécifique de configuration protégée, présenté en détail dans la documentation consacrée à l’extension.

L’exemple suivant illustre une instance de l’extension de script personnalisé pour Linux. Notez que la commande à exécuter inclut un ensemble d’informations d’identification. Dans le cadre de cet exemple, la commande à exécuter ne sera pas chiffrée.


```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Pour sécuriser la chaîne d’exécution, déplacez la propriété **commandToExecute** dans la configuration **protected**.

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

## <a name="troubleshoot-vm-extensions"></a>Résoudre les problèmes liés aux extensions de machine virtuelle

Chaque extension de machine virtuelle peut présenter une procédure de résolution des problèmes spécifique. Par exemple, lorsque vous utilisez l’extension de script personnalisé, les détails de l’exécution du script sont accessibles localement sur la machine virtuelle utilisée pour l’exécution de l’extension. La procédure de résolution des problèmes spécifique d’une extension est présentée en détail dans la documentation de cette dernière.

La procédure de résolution des problèmes ci-dessous s’applique à toutes les extensions de machine virtuelle.

### <a name="view-extension-status"></a>Afficher l’état de l’extension

Après avoir exécuté une extension de machine virtuelle, utilisez la commande d’interface de ligne de commande Azure suivante pour obtenir l’état de l’extension. Remplacez les exemples de noms de paramètre par vos propres valeurs.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

La sortie ressemble au texte suivant :

```azurecli
AutoUpgradeMinorVersion    Location    Name          ProvisioningState    Publisher                   ResourceGroup      TypeHandlerVersion  VirtualMachineExtensionType
-------------------------  ----------  ------------  -------------------  --------------------------  ---------------  --------------------  -----------------------------
True                       westus      customScript  Succeeded            Microsoft.Azure.Extensions  exttest                             2  customScript
```

L’état d’exécution de l’extension est également visible dans le portail Azure. Pour afficher l’état d’une extension, sélectionnez la machine virtuelle, choisissez **Extensions**, puis sélectionnez l’extension souhaitée.

### <a name="rerun-a-vm-extension"></a>Réexécuter une extension de machine virtuelle

Dans certains cas, il se peut que vous deviez réexécuter une extension de machine virtuelle. Pour ce faire, supprimez-la, puis réexécutez-la avec la méthode d’exécution de votre choix. Pour supprimer une extension, exécutez la commande suivante dans l’interface de ligne de commande Azure. Remplacez les exemples de noms de paramètre par vos propres valeurs.

```azurecli
az vm extension delete --name customScript --resource-group myResourceGroup --vm-name myVM
```

Vous pouvez supprimer une extension à partir du portail Azure en procédant comme suit :

1. Sélectionnez une machine virtuelle.
2. Choisissez **Extensions**.
3. Sélectionnez l’extension souhaitée.
4. Choisissez **Désinstaller**.

## <a name="common-vm-extension-reference"></a>Informations de référence sur les extensions de machine virtuelle courantes
| Nom de l’extension | Description | Plus d’informations |
| --- | --- | --- |
| Extension de script personnalisé pour Linux |Exécuter des scripts sur une machine virtuelle Azure |[Extension de script personnalisé pour Linux](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Extension Docker |Installer le démon Docker pour prendre en charge les commandes Docker distantes |[Extension de machine virtuelle Docker](dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| Extension d’accès aux machines virtuelles |Récupérer l’accès à une machine virtuelle Azure |[Extension d’accès aux machines virtuelles](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Extension Diagnostics Azure |Gérer les diagnostics Azure |[Extension Diagnostics Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extension d’accès aux machines virtuelles Azure |Gérer les utilisateurs et les informations d’identification |[Extension d’accès aux machines virtuelles pour Linux](https://azure.microsoft.com/en-us/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |
