---
title: "Créer un groupe de machines virtuelles identiques Windows à l’aide d’un modèle Azure | Microsoft Docs"
description: "Apprendre à créer rapidement un groupe de machines virtuelles identiques Windows avec un modèle Azure Resource Manager qui déploie un exemple d’application et configure des règles de mise à l’échelle automatique"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/19/2017
ms.author: iainfou
ms.openlocfilehash: 1632411b0cfc2f8fa59f323436ee386e763a1ae0
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="create-a-windows-virtual-machine-scale-set-with-an-azure-template"></a>Créer un groupe de machines virtuelles identiques Windows à l’aide d’un modèle Azure
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Vous pouvez mettre à l’échelle manuellement le nombre de machines virtuelles du groupe identique ou définir des règles pour mettre à l’échelle automatiquement en fonction de l’utilisation des ressources (processeur, demande de mémoire ou trafic réseau). Dans cet article de prise en main, vous créez un groupe de machines virtuelles identiques avec un modèle Azure Resource Manager. Vous pouvez également créer un groupe identique avec [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) ou le [portail Azure](virtual-machine-scale-sets-create-portal.md).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 4.4.1 ou version ultérieure pour les besoins de ce didacticiel. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.


## <a name="define-a-scale-set-in-a-template"></a>Définir un groupe identique dans un modèle
Les modèles Azure Resource Manager vous permettent de déployer des groupes de ressources liées. Les modèles sont écrits en JavaScript Object Notation (JSON) et définissent l’ensemble de l’environnement d’infrastructure Azure pour votre application. Dans un modèle unique, vous pouvez créer le groupe de machines virtuelles identiques, installer des applications et configurer des règles de mise à l’échelle automatique. Avec l’utilisation de variables et de paramètres, ce modèle peut être réutilisé pour mettre à jour des groupes identiques existants ou en créer d’autres. Vous pouvez déployer des modèles via le portail Azure, Azure CLI 2.0 ou Azure PowerShell, ou à partir de pipelines d’intégration continue/de livraison continue.

Pour plus d’informations sur les modèles, consultez [Présentation d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).

Un modèle définit la configuration de chaque type de ressource. Un type de ressource de groupe de machines virtuelles identiques est similaire à une machine virtuelle individuelle. Les parties essentielles du type de ressource de groupe de machines virtuelles identiques sont :

| Propriété                     | Description de la propriété                                  | Exemple de valeur de modèle                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| Type                         | Type de ressource Azure à créer                            | Microsoft.Compute/virtualMachineScaleSets |
| Nom                         | Nom du groupe identique                                       | myScaleSet                                |
| location                     | Emplacement de création du groupe identique                     | Est des États-Unis                                   |
| sku.name                     | Taille de machine virtuelle pour chaque instance de groupe identique                  | Standard_A1                               |
| sku.capacity                 | Nombre d’instances de machines virtuelles à créer initialement           | 2                                         |
| upgradePolicy.mode           | Mode de mise à niveau d’instance de machine virtuelle lorsque des modifications se produisent              | Automatique                                 |
| imageReference               | Plateforme ou image personnalisée à utiliser pour les instances de machine virtuelle | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | Préfixe du nom de chaque instance de machine virtuelle                     | myvmss                                    |
| osProfile.adminUsername      | Nom d’utilisateur de chaque instance de machine virtuelle                        | azureuser                                 |
| osProfile.adminPassword      | Mot de passe de chaque instance de machine virtuelle                        | P@ssw0rd!                                 |

 L’exemple suivant montre la définition de ressource de groupe identique essentielle. Pour personnaliser un modèle de groupe identique, vous pouvez modifier la taille de machine virtuelle ou la capacité initiale, ou utiliser une autre plateforme ou une image personnalisée.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2017-12-01",
  "sku": {
    "name": "Standard_A1",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
      "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```

 Pour que l’exemple reste court, la configuration de carte d’interface réseau virtuelle n’est pas affichée. En outre, des composants supplémentaires, tels qu’un équilibreur de charge, ne sont pas affichés. Un modèle de groupe identique complet figure [à la fin de cet article](#deploy-the-template).


## <a name="install-an-application"></a>Installer une application
Lorsque vous déployez un groupe identique, les extensions de machine virtuelle peuvent fournir des tâches d’automatisation et de configuration après le déploiement, telles que l’installation d’une application. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension. Pour appliquer une extension à votre groupe identique, vous ajoutez la section *extensionProfile* à l’exemple de ressource précédent. En règle générale, le profil d’extension définit les propriétés suivantes :

- Type d’extension
- Éditeur d’extension
- Version d’extension
- Emplacement des scripts de configuration ou d’installation
- Commandes à exécuter sur les instances de machine virtuelle

L’exemple de modèle [d’application ASP.NET sur Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) utilise l’extension DSC PowerShell pour installer une application MVC ASP.NET qui s’exécute dans IIS. 

Un script d’installation est téléchargé à partir de GitHub, tel que défini dans *url*. L’extension exécute ensuite *InstallIIS* à partir du script *IISInstall.ps1* comme défini dans *Fonction* et *Script*. L’application ASP.NET proprement dite est fournie en tant que package Web Deploy, qui est également téléchargé à partir de GitHub, tel que défini dans *WebDeployPackagePath* :

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "Microsoft.Powershell.DSC",
      "properties": {
        "publisher": "Microsoft.Powershell",
        "type": "DSC",
        "typeHandlerVersion": "2.9",
        "autoUpgradeMinorVersion": true,
        "forceUpdateTag": "1.0",
        "settings": {
          "configuration": {
            "url": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/DSC/IISInstall.ps1.zip",
            "script": "IISInstall.ps1",
            "function": "InstallIIS"
          },
          "configurationArguments": {
            "nodeName": "localhost",
            "WebDeployPackagePath": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/WebDeploy/DefaultASPWebApp.v1.0.zip"
          }
        }
      }
    }
  ]
}
```

## <a name="deploy-the-template"></a>Déployer le modèle
Vous pouvez déployer le modèle [d’application MVC ASP.NET sur Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) avec le bouton **Déployer sur Azure** suivant. Ce bouton ouvre le portail Azure, charge le modèle complet et vous invite à renseigner quelques paramètres comme le nom du groupe identique, le nombre d’instances et les informations d’identification d’administrateur.

[![Déployer le modèle sur Azure](media/virtual-machine-scale-sets-create-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Vous pouvez également utiliser Azure PowerShell pour installer l’application ASP.NET sur Windows avec [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) comme suit :

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzureRmVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

Renseignez le nom du groupe identique et les informations d’identification d’administrateur pour les instances de machine virtuelle. 10 à 15 minutes peuvent être nécessaires pour créer le groupe identique et appliquer l’extension permettant de configurer l’application.


## <a name="test-your-sample-application"></a>Tester votre exemple d’application
Pour voir fonctionner votre application, obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), comme suit :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Entrez l’adresse IP publique de l’équilibreur de charge dans un navigateur web au format *http://publicIpAddress/MyApp*. L’équilibreur de charge répartit le trafic vers l’une de vos instances de machine virtuelle, comme illustré dans l’exemple suivant :

![Site IIS en cours d’exécution](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Supprimer des ressources
Quand vous n’en avez plus besoin, vous pouvez utiliser [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées comme suit :

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```


## <a name="next-steps"></a>Étapes suivantes
Dans cet article de prise en main, vous avez créé un groupe identique Windows avec un modèle Azure et vous avez utilisé l’extension DSC PowerShell afin d’installer une application ASP.NET de base sur les instances de machine virtuelle. Pour plus d’automatisation et d’évolutivité, développez votre groupe identique à l’aide des articles de procédures suivants :

- [Déployer votre application sur des groupes de machines virtuelles identiques](virtual-machine-scale-sets-deploy-app.md)
- Faites une mise à l’échelle automatique avec [Azure CLI](virtual-machine-scale-sets-autoscale-cli.md), [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) ou le [portail Azure](virtual-machine-scale-sets-autoscale-portal.md)
- [Mises à niveau automatiques du système d’exploitation dans des groupes de machines virtuelles identiques Azure](virtual-machine-scale-sets-automatic-upgrade.md)
