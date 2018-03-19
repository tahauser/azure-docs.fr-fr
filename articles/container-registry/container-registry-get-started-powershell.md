---
title: "Démarrage rapide - Créer un registre Docker privé dans Azure avec PowerShell"
description: "Apprenez rapidement à créer un registre de conteneurs Docker privé avec PowerShell."
services: container-registry
author: neilpeterson
manager: timlt
ms.service: container-registry
ms.topic: quickstart
ms.date: 03/03/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 2bae45955cf3c2b157acce2544b1f35fbddd0170
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="create-an-azure-container-registry-using-powershell"></a>Créer un registre Azure Container Registry à l’aide de PowerShell

Azure Container Registry est un service de registre de conteneurs Docker géré utilisé pour stocker des images de conteneurs Docker privés. Ce guide détaille comment créer un registre de conteneurs Azure Container Registry avec PowerShell, envoyer une image de conteneur dans le registre et enfin déployer le conteneur dans Azure Container Instances (ACI) à partir de votre registre.

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

```powershell
docker login $registry.LoginServer -u $creds.Username -p $creds.Password
```

Une fois l’opération terminée, la commande renvoie `Login Succeeded`. Vous pouvez également voir un avertissement de sécurité vous recommandant l’utilisation du paramètre `--password-stdin`. Même si son utilisation n’est pas le sujet de cet article, nous vous recommandons de suivre cette meilleure pratique. Pour plus d’informations, consultez la référence de la commande [docker login][docker-login].

## <a name="push-image-to-acr"></a>Envoyer une image dans l’ACR

Pour envoyer une image dans un registre Azure Container Registry, vous devez tout d’abord disposer d’une image. Si nécessaire, exécutez la commande suivante pour extraire une image créée préalablement du hub Docker.

```powershell
docker pull microsoft/aci-helloworld
```

L’image doit être étiquetée avec le nom du serveur de connexion ACR. Utilisez la commande [docker tag][docker-tag] pour réaliser cette action. 

```powershell
$image = $registry.LoginServer + "/aci-helloworld:v1"
docker tag microsoft/aci-helloworld $image
```

Enfin, utilisez la commande [docker push][docker-push] pour envoyer l’image vers ACR.

```powershell
docker push $image
```

## <a name="deploy-image-to-aci"></a>Déployer l’image dans ACI
Pour déployer l’image en tant qu’instance de conteneur dans Azure Container Instances (ACI), convertissez d’abord les informations d’identification du registre en PSCredential.

```powershell
$secpasswd = ConvertTo-SecureString $creds.Password -AsPlainText -Force
$pscred = New-Object System.Management.Automation.PSCredential($creds.Username, $secpasswd)
```

Pour déployer votre image conteneur à partir du registre de conteneurs avec 1 noyau de processeur et 1 Go de mémoire, exécutez la commande qui suit :

```powershell
New-AzureRmContainerGroup -ResourceGroup myResourceGroup -Name mycontainer -Image $image -Cpu 1 -MemoryInGB 1 -IpAddressType public -Port 80 -RegistryCredential $pscred
```

Vous devez obtenir une réponse initiale d’Azure Resource Manager avec des détails concernant votre conteneur. Pour surveiller le statut de votre conteneur, et voir quand il s’exécute, répétez la commande [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup]. L’opération doit prendre moins d’une minute.

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).ProvisioningState
```

Exemple de sortie : `Succeeded`

## <a name="view-the-application"></a>Afficher l’application
Une fois le déploiement dans ACI réussi, récupérez l’adresse IP publique du conteneur avec la commande [Get-AzureRmContainerGroup][Get-AzureRmContainerGroup] :

```powershell
(Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer).IpAddress
```

Exemple de sortie : `"13.72.74.222"`

Pour voir l’application en cours d’exécution, accédez à l’adresse IP publique dans votre navigateur favori. Le résultat suivant doit s’afficher :

![Application Hello world dans le navigateur][qs-portal-15]

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez utiliser la commande [Remove-AzureRmResourceGroup][Remove-AzureRmResourceGroup] pour supprimer le groupe de ressources, Azure Container Registry et toutes les instances Azure Container Instances.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez créé un registre de conteneur Azure Container Registry avec Azure CLI, et lancé une instance dans Azure Container Instances. Poursuivez avec le didacticiel sur Azure Container Instances pour approfondir vos connaissances sur ACI.

> [!div class="nextstepaction"]
> [Didacticiel Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- Links - internal -->
[Get-AzureRmContainerGroup]: /powershell/module/azurerm.containerinstance/get-azurermcontainergroup
[Remove-AzureRmResourceGroup]: /powershell/module/azurerm.resources/remove-azurermresourcegroup

<!-- IMAGES> -->
[qs-portal-15]: ./media/container-registry-get-started-portal/qs-portal-15.png
