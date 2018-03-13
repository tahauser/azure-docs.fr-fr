---
title: "Déployez une application sur un groupe de machines virtuelles identiques Azure | Microsoft Docs"
description: "Découvrez comment déployer des applications sur des instances de machine virtuelle Linux et Windows d’un groupe identique"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 288bcdf6628f60d0b08fe151e630784d665db56f
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>Déployer votre application sur des groupes de machines virtuelles identiques
Pour exécuter des applications sur des instances de machine virtuelle d’un groupe identique, vous devez d’abord installer les composants d’application et les fichiers requis. Cet article présente des méthodes pour créer une image de machine virtuelle personnalisée d’un groupe identique, ou pour exécuter automatiquement des scripts d’installation sur des instances de machine virtuelle existantes. Vous apprendrez également à gérer des applications ou des mises à jour du système d’exploitation sur un groupe identique.


## <a name="build-a-custom-vm-image"></a>Créer une image de machine virtuelle personnalisée
Lorsque vous utilisez une des images de plateforme Azure pour créer les instances de votre groupe identique, aucun logiciel supplémentaire n’est installé ou configuré. Vous pouvez automatiser l’installation de ces composants, mais cela augmente le temps nécessaire pour configurer des instances de machine virtuelle sur vos groupes identiques. Si vous appliquez plusieurs modifications de configuration aux instances de machine virtuelle, vous surchargez la gestion de ces scripts de configuration et tâches.

Pour simplifier la gestion de la configuration et accélérer la configuration d’une machine virtuelle, vous pouvez créer une image de machine virtuelle personnalisée prête à exécuter votre application dès qu’une instance est configurée dans le groupe identique. Voici le processus global permettant de créer une instance de machine virtuelle personnalisée pour des groupes identiques :

1. Pour créer une image de machine virtuelle personnalisée pour vos instances de groupe identique, vous créez et vous connectez à une machine virtuelle, puis installez et configurez l’application. Vous pouvez utiliser Packer pour définir et créer une image de machine virtuelle [Linux](../virtual-machines/linux/build-image-with-packer.md) ou [Windows](../virtual-machines/windows/build-image-with-packer.md). Ou vous pouvez manuellement créer et configurer la machine virtuelle :

    - Créez une machine virtuelle Linux avec [Azure CLI 2.0](../virtual-machines/linux/quick-create-cli.md), [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md), ou le [portail](../virtual-machines/linux/quick-create-portal.md).
    - Créez une machine virtuelle Windows avec [Azure PowerShell](../virtual-machines/windows/quick-create-powershell.md), [Azure CLI 2.0](../virtual-machines/windows/quick-create-cli.md), ou le [portail](../virtual-machines/windows/quick-create-portal.md).
    - Connectez-vous à une machine virtuelle [Linux](../virtual-machines/linux/mac-create-ssh-keys.md#use-the-ssh-key-pair) ou [Windows](../virtual-machines/windows/connect-logon.md).
    - Installez et configurez les applications et les outils nécessaires. Si vous avez besoin de versions spécifiques d’une bibliothèque ou d’un runtime, une image de machine virtuelle personnalisée vous permet de définir une version 

2. Capturez votre machine virtuelle avec [Azure CLI 2.0](../virtual-machines/linux/capture-image.md) ou [Azure PowerShell](../virtual-machines/windows/capture-image.md). Cette étape crée l’image de machine virtuelle personnalisée qui sert ensuite à déployer des instances sur un groupe identique.

3. [Créez un groupe identique](virtual-machine-scale-sets-create.md) et spécifiez l’image de machine virtuelle personnalisée créée dans les étapes précédentes.


## <a name="already-provisioned"></a>Installer une application avec l’extension de script personnalisé
L’extension de script personnalisé télécharge et exécute des scripts sur des machines virtuelles Azure. Cette extension est utile pour la configuration post-déploiement, l’installation de logiciels ou toute autre tâche de configuration ou de gestion. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension.

L’extension de script personnalisé s’intègre aux modèles Azure Resource Manager et peut être exécutée à l’aide de l’interface de ligne de commande Azure, de PowerShell, du portail Azure ou de l’API REST de machine virtuelle Azure. 

Pour plus d’informations, consultez [Vue d’ensemble de l’extension de script personnalisé](../virtual-machines/windows/extensions-customscript.md).


### <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell
PowerShell utilise une table de hachage pour stocker le fichier à télécharger et la commande à exécuter. L’exemple suivant permet :

- Demande aux instances de machine virtuelle pour télécharger un script à partir de GitHub - *https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1*
- Définit l’extension pour exécuter un script d’installation - `powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1`
- Obtient des informations sur un groupe identique avec [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Applique l’extension aux instances de machine virtuelle avec [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

L’extension de script personnalisé est appliquée aux instances de machine virtuelle *myScaleSet* dans le groupe de ressources nommé *myResourceGroup*. Entrez vos propres noms, comme suit :

```powershell
# Define the script for your Custom Script Extension to run
$customConfig = @{
    "fileUris" = (,"https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Custom Script Extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $customConfig

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```

Si la stratégie de mise à niveau de votre groupe identique est *manuelle*, mettez à jour vos instances de machine virtuelle avec [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Cette applet de commande applique la configuration du groupe identique mis à jour aux instances de machine virtuelle, puis installe votre application.


### <a name="use-azure-cli-20"></a>Utiliser Azure CLI 2.0
Pour utiliser l’extension de script personnalisé avec l’interface CLI Azure, vous créez un fichier JSON qui définit les fichiers à obtenir et les commandes à exécuter. Ces définitions JSON peuvent être réutilisées sur plusieurs déploiements de groupe identique pour garantir des installations d’applications cohérentes.

Dans l’interpréteur de commandes actuel, créez un fichier nommé *customConfig.json* et collez la configuration suivante. Par exemple, créez le fichier dans l’interpréteur de commandes Cloud et non sur votre ordinateur local. Vous pouvez utiliser l’éditeur de votre choix. Entrez `sensible-editor cloudConfig.json` pour créer le fichier et afficher la liste des éditeurs disponibles.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Appliquez la configuration de l’extension de script personnalisé aux instances de machine virtuelle dans votre groupe identique avec [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set). L’exemple suivant applique la configuration *customConfig.json* aux instances de machine virtuelle *myScaleSet* dans le groupe de ressources nommé *myResourceGroup*. Entrez vos propres noms, comme suit :

```azurecli
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfig.json
```

Si la stratégie de mise à niveau de votre groupe identique est *manuelle*, mettez à jour vos instances de machine virtuelle avec [az vmss update-instances](/cli/azure/vmss#update-instances). Cette applet de commande applique la configuration du groupe identique mis à jour aux instances de machine virtuelle, puis installe votre application.


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>Installer une application sur une machine virtuelle Windows avec PowerShell DSC
La [Configuration d’état souhaité (DSC) PowerShell](https://msdn.microsoft.com/en-us/powershell/dsc/overview) est une plateforme de gestion qui définit la configuration des machines cibles. Les configurations d’état souhaité définissent les éléments à installer sur une machine et la procédure à suivre pour configurer l’hôte. Un moteur du Gestionnaire de configuration local (LCM) s’exécute sur chaque nœud cible qui traite les actions demandées en fonction des configurations envoyées.

L’extension PowerShell DSC vous permet de personnaliser les instances de machine virtuelle d’un groupe identique avec PowerShell. L’exemple suivant permet :

- Demande aux instances de machine virtuelle de télécharger un package DSC à partir de GitHub - *https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip*
- Définit l’extension pour exécuter un script d’installation - `configure-http.ps1`
- Obtient des informations sur un groupe identique avec [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss)
- Applique l’extension aux instances de machine virtuelle avec [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)

L’extension DSC est appliquée aux instances de machine virtuelle *myScaleSet* dans le groupe de ressources nommé *myResourceGroup*. Saisissez vos propres noms, comme suit :

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/iainfoulds/azure-samples/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzureRmVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

Si la stratégie de mise à niveau de votre groupe identique est *manuelle*, mettez à jour vos instances de machine virtuelle avec [Update-AzureRmVmssInstance](/powershell/module/azurerm.compute/update-azurermvmssinstance). Cette applet de commande applique la configuration du groupe identique mis à jour aux instances de machine virtuelle, puis installe votre application.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>Installer une application sur une machine virtuelle Linux avec cloud-init
[Cloud-init](https://cloudinit.readthedocs.io/latest/) est une méthode largement utilisée pour personnaliser une machine virtuelle Linux lors de son premier démarrage. Vous pouvez utiliser cloud-init pour installer des packages et écrire des fichiers, ou encore pour configurer des utilisateurs ou des paramètres de sécurité. Comme cloud-init s’exécute pendant le processus de démarrage initial, aucune autre étape ni aucun agent ne sont nécessaires pour appliquer votre configuration.

Cloud-init fonctionne aussi sur les différentes distributions. Par exemple, vous n’utilisez pas **apt-get install** ou **yum install** pour installer un package. Au lieu de cela, vous pouvez définir une liste des packages à installer, après quoi cloud-init se charge d’utiliser automatiquement l’outil de gestion de package natif correspondant à la distribution que vous sélectionnez.

Pour plus d’informations, y compris un exemple de fichier *cloud-init.txt*, consultez [Utiliser cloud-init pour personnaliser des machines virtuelles Azure](../virtual-machines/linux/using-cloud-init.md).

Pour créer un groupe identique et utiliser un fichier cloud-init, ajoutez le paramètre `--custom-data` à la commande [az vmss create](/cli/azure/vmss#az_vmss_create) et spécifiez le nom d’un fichier cloud-init. L’exemple suivant crée un groupe identique nommé *myScaleSet* dans *myResourceGroup* et configure des instances de machine virtuelle avec un fichier nommé *cloud-init.txt*. Entrez vos propres noms, comme suit :

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="install-applications-as-a-set-scales-out"></a>Installer des applications lorsqu’un groupe identique monte en charge
Les groupes identiques vous permettent d’augmenter le nombre d’instances de machine virtuelle qui exécutent votre application. Ce processus de montée en charge peut être démarré manuellement ou automatiquement en fonction de métriques telles que l’utilisation du processeur ou de la mémoire.

Si vous avez appliqué une extension de script personnalisé au groupe identique, l’application est installée sur chaque nouvelle instance de machine virtuelle. Si le groupe identique est basé sur une image personnalisée avec l’application préinstallée, chaque nouvelle instance de machine virtuelle est déployée dans un état utilisable. 

Si les instances de machine virtuelle du groupe identique sont des hôtes de conteneur, vous pouvez utiliser l’extension de script personnalisé pour extraire et exécuter des images de conteneur requises. L’extension de script personnalisé peut également enregistrer la nouvelle instance de machine virtuelle avec un orchestrateur, par exemple Azure Container Service.


## <a name="deploy-application-updates"></a>Déployer des mises à jour de l’application
Si vous mettez à jour le code de votre application, des bibliothèques ou des packages, vous pouvez transmettre le dernier état de l’application aux instances de machine virtuelle d’un groupe identique. Si vous utilisez l’extension de script personnalisé, les mises à jour de votre application ne sont pas automatiquement déployées. Modifiez la configuration du script personnalisé afin de pointer vers un script d’installation avec un nom de version mis à jour. Dans un exemple précédent, l’extension de script personnalisé utilise un script nommé *automate_nginx.sh* comme suit :

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

Toutes les mises à jour que vous apportez à votre application ne sont pas exposées dans l’extension de script personnalisé, sauf en cas de modification de ce script d’installation. Une approche consiste à inclure un numéro de version qui s’incrémente à chaque nouvelle version de votre application. L’extension de script personnalisé peut désormais référencer *automate_nginx_v2.sh* comme suit :

```json
{
  "fileUris": ["https://raw.githubusercontent.com/iainfoulds/azure-samples/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

L’extension de script personnalisé s’exécute désormais sur les instances de machine virtuelle pour appliquer les dernières mises à jour de l’application.


### <a name="install-applications-with-os-updates"></a>Installer des applications avec des mises jour du système d’exploitation
Lorsque de nouvelles versions du système d’exploitation sont disponibles, vous pouvez utiliser ou créer une nouvelle image personnalisée et [déployer des mises à niveau du système d’exploitation](virtual-machine-scale-sets-upgrade-scale-set.md) sur un groupe identique. Chaque instance de machine virtuelle est mise à niveau avec la dernière image que vous spécifiez. Vous pouvez utiliser une image personnalisée avec l’application préinstallée, l’extension de script personnalisé ou DSC PowerShell, pour que votre application soit automatiquement disponible lorsque vous effectuez la mise à niveau. Vous devrez peut-être planifier la maintenance de l’application lorsque vous effectuez cette procédure pour éviter tout problème de compatibilité de version.

Si vous utilisez une image de machine virtuelle personnalisée avec l’application préinstallée, vous pouvez intégrer les mises à jour de l’application dans un pipeline de déploiement pour créer les nouvelles images et déployer des mises à niveau du système d’exploitation sur l’ensemble du groupe identique. Avec cette approche, le pipeline peut récupérer les dernières versions de l’application, créer et valider une image de machine virtuelle, puis mettre à niveau les instances de machine virtuelle dans le groupe identique. Pour exécuter un pipeline de déploiement qui crée et déploie des mises à jour de l’application sur des images de machine virtuelle personnalisées, vous pouvez utiliser [Visual Studio Team Services](https://www.visualstudio.com/team-services/), [Spinnaker](https://www.spinnaker.io/) ou [Jenkins](https://jenkins.io/).


## <a name="next-steps"></a>Étapes suivantes
Lorsque vous générez et déployez des applications sur vos groupes identiques, vous pouvez consulter [Vue d’ensemble de la conception de groupes identiques](virtual-machine-scale-sets-design-overview.md). Pour plus d’informations sur la façon de gérer votre groupe identique, consultez [Utiliser PowerShell pour gérer votre groupe identique](virtual-machine-scale-sets-windows-manage.md).
