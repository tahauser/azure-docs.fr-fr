---
title: "Démarrage rapide - Créer un registre Docker privé dans Azure avec PowerShell"
description: "Apprenez rapidement à créer un registre de conteneurs Docker privé avec PowerShell."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 80b5055dee35cd6efe62ee949c05aef386a3ba14
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Créer un registre Azure Container Registry à l’aide de PowerShell

Azure Container Registry est un service de registre de conteneurs Docker géré utilisé pour stocker des images de conteneurs Docker privés. Ce guide décrit en détail la création d’une instance Azure Container Registry à l’aide de PowerShell.

Ce démarrage rapide requiert le module Azure PowerShell version 3.6 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Docker doit également être installé en local. Docker fournit des packages qui le configurent facilement sur n’importe quel système [Mac][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container-registry"></a>Créer un registre de conteneur

Créez une instance ACR à l’aide de la commande [New-AzureRMContainerRegistry](/powershell/module/containerregistry/New-AzureRMContainerRegistry).

Le nom du registre doit être unique dans Azure et contenir entre 5 et 50 caractères alphanumériques. Dans l’exemple suivant, nous utilisons le nom *myContainerRegistry007*. Mettez à jour le nom de façon à utiliser une valeur unique.

```powershell
$registry = New-AzureRMContainerRegistry -ResourceGroupName "myResourceGroup" -Name "myContainerRegistry007" -EnableAdminUser -Sku Basic
```

## <a name="log-in-to-acr"></a>Se connecter à l’ACR

Avant d’extraire et de transmettre des images conteneur, vous devez vous connecter à l’instance ACR. Utilisez tout d’abord la commande [Get-AzureRmContainerRegistryCredential](/powershell/module/containerregistry/get-azurermcontainerregistrycredential) pour obtenir les informations d’identification d’administrateur pour l’instance ACR.

```powershell
$creds = Get-AzureRmContainerRegistryCredential -Registry $registry
```

Utilisez ensuite la commande [docker login][docker-login] pour vous connecter à l’instance ACR.

```bash
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Une fois l’opération terminée, la commande renvoie `Login Succeeded`. Vous pouvez également voir un avertissement de sécurité vous recommandant l’utilisation du paramètre `--password-stdin`. Même si son utilisation n’est pas le sujet de cet article, nous vous recommandons de suivre cette meilleure pratique. Pour plus d’informations, consultez la référence de la commande [docker login][docker-login].

## <a name="push-image-to-acr"></a>Envoyer une image dans l’ACR

Pour envoyer une image dans un registre Azure Container Registry, vous devez tout d’abord disposer d’une image. Si nécessaire, exécutez la commande suivante pour extraire une image créée préalablement du hub Docker.

```bash
docker pull microsoft/aci-helloworld
```

L’image doit être étiquetée avec le nom du serveur de connexion ACR. Exécutez la commande [Get-AzureRmContainerRegistry](/powershell/module/containerregistry/Get-AzureRmContainerRegistry) pour retourner le nom du serveur de connexion de l’instance ACR.

```powershell
Get-AzureRmContainerRegistry | Select Loginserver
```

Marquez l’image en utilisant la commande [docker tag][docker-tag]. Remplacez la valeur *acrLoginServer* par le nom du serveur de connexion de votre instance ACR.

```bash
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Enfin, utilisez la commande [docker push][docker-push] pour envoyer les images à l’instance ACR. Remplacez la valeur *acrLoginServer* par le nom du serveur de connexion de votre instance ACR.

```bash
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, l’instance ACR et toutes les images de conteneur.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé un registre Azure Container Registry avec Azure CLI. Si vous souhaitez utiliser Azure Container Registry avec des instances Azure Container Instances, passez au didacticiel Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/