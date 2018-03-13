---
title: "Guide de démarrage rapide : créer votre premier conteneur Azure Container Instances avec PowerShell"
description: "Commencez à utiliser Azure Container Instances en créant une instance de conteneur Windows avec PowerShell."
services: container-instances
author: mmacy
manager: timlt
ms.service: container-instances
ms.topic: quickstart
ms.date: 01/02/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 18a342fed7e99e82082764d6f5a3429a3ce794b7
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2018
---
# <a name="create-your-first-container-in-azure-container-instances"></a>Créer son premier conteneur dans Azure Container Instances

Azure Container Instances facilite la création et la gestion de conteneurs Docker dans Azure, sans avoir à approvisionner les machines virtuelles ou à adopter un service de niveau supérieur.

Dans ce guide de démarrage rapide, vous créez un conteneur Windows dans Azure et l’exposez sur internet avec une adresse IP publique. Cette opération s’effectue en une seule commande. Après quelques instants, vous pouvez voir l’application s’exécuter dans votre navigateur :

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][qs-powershell-01]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Si vous choisissez d’installer et d’utiliser PowerShell en local, vous devez exécuter le module Azure PowerShell version 3.6 ou version ultérieure pour les besoins de ce didacticiel. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez effectuer une mise à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps). Si vous exécutez PowerShell en local, vous devez également lancer `Login-AzureRmAccount` pour créer une connexion avec Azure.

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup][New-AzureRmResourceGroup]. Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

 ```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Créez un conteneur.

Vous pouvez créer un conteneur en attribuant un nom, une image Docker, ainsi qu’un groupe de ressources Azure à l’applet de commande [New-AzureRmContainerGroup][New-AzureRmContainerGroup]. Si vous le souhaitez, vous pouvez exposer le conteneur sur internet avec une adresse IP publique. Dans ce cas, nous allons utiliser un conteneur Nano Server exécutant Internet Information Services (IIS).

 ```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

Après quelques secondes, vous obtenez une réponse à votre requête. Initialement, le conteneur est défini sur l’état **En cours de création**, mais il doit démarrer après une ou deux minutes. Vous pouvez vérifier l’état à l’aide de l’applet de commande [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] :

 ```azurepowershell-interactive
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

L’état d’approvisionnement et l’adresse IP du conteneur apparaissent dans la sortie de l’applet de commande :

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

Lorsque la valeur **ProvisioningState** du conteneur passe à `Succeeded`, vous pouvez atteindre le conteneur dans le navigateur en utilisant l’adresse IP fournie.

![IIS déployé à l’aide d’Azure Container Instances affiché dans un navigateur][qs-powershell-01]

## <a name="delete-the-container"></a>Supprimer un conteneur

Lorsque vous avez fini d’utiliser le conteneur, vous pouvez le supprimer à l’aide de l’applet de commande [Remove-AzureRmContainerGroup][Remove-AzureRmContainerGroup] :

 ```azurepowershell-interactive
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce démarrage rapide, vous avez démarré un conteneur Windows prêt à l’emploi dans Azure Container Instances. Si vous voulez essayer de créer un conteneur par vous-même et de le déployer dans Azure Container Instances à l’aide d’Azure Container Registry, passez au didacticiel relatif à Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzureRmResourceGroup]: /powershell/module/azurerm.resources/new-azurermresourcegroup
[New-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/remove-azurermcontainergroup
