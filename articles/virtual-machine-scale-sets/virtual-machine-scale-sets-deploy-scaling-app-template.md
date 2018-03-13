---
title: "Créer un groupe de machines virtuelles identiques à l’aide d’un modèle Azure | Microsoft Docs"
description: "Apprendre à créer rapidement une machine virtuelle avec un modèle Azure Resource Manager"
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
ms.date: 11/16/2017
ms.author: iainfou
ms.openlocfilehash: 201b752c2a79362f2e049d2e0f0b953d77aaedfe
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Créer un groupe de machines virtuelles identiques avec Azure CLI 2.0
Un groupe de machines virtuelles identiques vous permet de déployer et de gérer un ensemble de machines virtuelles identiques prenant en charge la mise à l’échelle automatique. Vous pouvez mettre à l’échelle manuellement le nombre de machines virtuelles du groupe identique ou définir des règles pour mettre à l’échelle automatiquement en fonction de l’utilisation des ressources (processeur, demande de mémoire ou trafic réseau). Dans cet article de prise en main, vous créez un groupe de machines virtuelles identiques avec un modèle Azure Resource Manager. Vous pouvez également créer un groupe identique avec [Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md), [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) ou le [portail Azure](virtual-machine-scale-sets-create-portal.md).


## <a name="overview-of-templates"></a>Présentation des modèles
Les modèles Azure Resource Manager vous permettent de déployer des groupes de ressources liées. Les modèles sont écrits en JavaScript Object Notation (JSON) et définissent l’ensemble de l’environnement d’infrastructure Azure pour votre application. Dans un modèle unique, vous pouvez créer le groupe de machines virtuelles identiques, installer des applications et configurer des règles de mise à l’échelle automatique. Avec l’utilisation de variables et de paramètres, ce modèle peut être réutilisé pour mettre à jour des groupes identiques existants ou en créer d’autres. Vous pouvez déployer des modèles via le portail Azure, Azure CLI 2.0 ou Azure PowerShell, ou également les appeler à partir de pipelines d’intégration continue/de livraison continue.

Pour plus d’informations sur les modèles, consultez [Présentation d’Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#template-deployment).


## <a name="define-a-scale-set"></a>Définir un groupe identique
Un modèle définit la configuration de chaque type de ressource. Un type de ressource de groupe de machines virtuelles identiques est similaire à une machine virtuelle individuelle. Les parties essentielles du type de ressource de groupe de machines virtuelles identiques sont :

| Propriété                     | Description de la propriété                                  | Exemple de valeur de modèle                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| Type                         | Type de ressource Azure à créer                            | Microsoft.Compute/virtualMachineScaleSets |
| Nom                         | Nom du groupe identique                                       | myScaleSet                                |
| location                     | Emplacement de création du groupe identique                     | Est des États-Unis                                   |
| sku.name                     | Taille de machine virtuelle pour chaque instance de groupe identique                  | Standard_A1                               |
| sku.capacity                 | Nombre d’instances de machines virtuelles à créer initialement           | 2                                         |
| upgradePolicy.mode           | Mode de mise à niveau d’instance de machine virtuelle lorsque des modifications se produisent              | Automatique                                 |
| imageReference               | Plateforme ou image personnalisée à utiliser pour les instances de machine virtuelle | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | Préfixe du nom de chaque instance de machine virtuelle                     | myvmss                                    |
| osProfile.adminUsername      | Nom d’utilisateur de chaque instance de machine virtuelle                        | azureuser                                 |
| osProfile.adminPassword      | Mot de passe de chaque instance de machine virtuelle                        | P@ssw0rd!                                 |

 L’extrait de code suivant montre la définition de ressource de groupe identique essentielle dans un modèle. Pour que l’exemple reste court, la configuration de carte d’interface réseau virtuelle n’est pas affichée. Pour personnaliser un modèle de groupe identique, vous pouvez modifier la taille de machine virtuelle ou la capacité initiale, ou utiliser une autre plateforme ou une image personnalisée.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US",
  "apiVersion": "2016-04-30-preview",
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
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
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


## <a name="install-an-application"></a>Installer une application
Lorsque vous déployez un groupe identique, les extensions de machine virtuelle peuvent fournir des tâches d’automatisation et de configuration après le déploiement, telles que l’installation d’une application. Des scripts peuvent être téléchargés à partir de Stockage Azure ou de GitHub, ou fournis dans le portail Azure lors de l’exécution de l’extension. Pour appliquer une extension à votre groupe identique, vous ajoutez la section *extensionProfile* à l’exemple de ressource précédent. En règle générale, le profil d’extension définit les propriétés suivantes :

- Type d’extension
- Éditeur d’extension
- Version d’extension
- Emplacement des scripts de configuration ou d’installation
- Commandes à exécuter sur les instances de machine virtuelle

Nous allons étudier deux façons d’installer une application avec des extensions : avec l’extension de script personnalisé pour installer une application Python sur Linux ou avec l’extension DSC PowerShell pour installer une application ASP.NET sur Windows.

### <a name="python-http-server-on-linux"></a>Serveur HTTP Python sur Linux
Le [serveur HTTP Python sur Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) utilise l’extension de script personnalisé pour installer [Bottle](http://bottlepy.org/docs/dev/), une infrastructure web Python, et un serveur HTTP simple. 

Deux scripts sont définis dans *fileUris* - *installserver.sh* et *workserver.py*. Ces fichiers sont téléchargés à partir de GitHub, puis *commandToExecute* définit `bash installserver.sh` pour installer et configurer l’application :

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
            "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
          ],
          "commandToExecute": "bash installserver.sh"
        }
      }
    }
  ]
}
```

### <a name="aspnet-application-on-windows"></a>Application ASP.NET sur Windows
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
Le moyen le plus simple pour déployer le modèle de [serveur HTTP Python sur Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) ou [d’application ASP.NET MVC sur Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-webapp-dsc-autoscale) consiste à utiliser le bouton **Déployer sur Azure** qui se trouve dans les fichiers Lisez-moi dans GitHub.  Vous pouvez également utiliser PowerShell ou l’interface Azure CLI pour déployer les exemples de modèles.

### <a name="azure-cli-20"></a>Azure CLI 2.0
Vous pouvez utiliser Azure CLI 2.0 pour installer le serveur HTTP Python sur Linux, comme suit :

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/azuredeploy.json
```

Pour voir votre application en action, obtenez l’adresse IP publique de l’équilibreur de charge avec [az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_show) comme suit :

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

Entrez l’adresse IP publique de l’équilibreur de charge dans un navigateur web au format *http://<publicIpAddress>:9000/do_work*. L’équilibreur de charge répartit le trafic vers l’une de vos instances de machine virtuelle, comme illustré dans l’exemple suivant :

![Page web par défaut dans NGINX](media/virtual-machine-scale-sets-create-template/running-python-app.png)


### <a name="azure-powershell"></a>Azure PowerShell
Vous pouvez utiliser Azure PowerShell pour installer l’application ASP.NET sur Windows, comme suit :

```azurepowershell-interactive
# Create a resource group
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzureRmResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateFile https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json
```

Pour voir fonctionner votre application, obtenez l’adresse IP publique de votre équilibreur de charge avec [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress), comme suit :

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Entrez l’adresse IP publique de l’équilibreur de charge dans un navigateur web au format *http://<publicIpAddress>/MyApp*. L’équilibreur de charge répartit le trafic vers l’une de vos instances de machine virtuelle, comme illustré dans l’exemple suivant :

![Site IIS en cours d’exécution](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)


## <a name="clean-up-resources"></a>Supprimer des ressources
Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées, comme suit :

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Étapes suivantes
