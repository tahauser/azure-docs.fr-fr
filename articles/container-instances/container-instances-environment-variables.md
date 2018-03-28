---
title: Définir des variables d’environnement dans Azure Container Instances
description: Découvrir comment définir des variables d’environnement dans Azure Container Instances
services: container-instances
author: david-stanford
manager: timlt
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: f845e96a3e05be3f9109446d0d9e88934c4794cc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="set-environment-variables"></a>Définition des variables d'environnement

Définir des variables d’environnement dans vos instances de conteneur vous permet de fournir une configuration dynamique de l’application ou du script exécuté par le conteneur.

Vous pouvez actuellement définir des variables d’environnement à partir de l’interface de ligne de commande (CLI) et de PowerShell. Dans les deux cas, vous utilisez un indicateur sur les commandes pour définir les variables d’environnement. Définir des variables d’environnement dans ACI est similaire à l’argument de ligne de commande `--env` pour `docker run`. Par exemple, si vous utilisez l’image conteneur microsoft/aci-wordcount, vous pouvez modifier le comportement en spécifiant les variables d’environnement suivantes :

*NumWords* : nombre de mots envoyés à STDOUT.

*MinLength* : nombre minimal de caractères dans un mot pour que celui-ci soit comptabilisé. Cela vous permet d’ignorer les mots communs tels que « de » et « le ».

## <a name="azure-cli-example"></a>Exemple Azure CLI

Pour afficher la sortie par défaut du conteneur, exécutez la commande suivante :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Si vous spécifiez `NumWords=5` et `MinLength=8` pour les variables d’environnement du conteneur, les journaux du conteneur doivent afficher une sortie différente.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Une fois que l’état du conteneur est *Terminé* (utilisez [az container show][az-container-show] pour vérifier son état), affichez ses journaux pour voir la sortie.  Pour afficher la sortie du conteneur sans variables d’environnement, affectez à --name la valeur mycontainer1 au lieu de mycontainer2.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Exemple Azure PowerShell

Pour afficher la sortie par défaut du conteneur, exécutez la commande suivante :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Si vous spécifiez `NumWords=5` et `MinLength=8` pour les variables d’environnement du conteneur, les journaux du conteneur doivent afficher une sortie différente.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Une fois que l’état du conteneur est *Terminé* (utilisez [Get-AzureRmContainerInstanceLog][azure-instance-log] pour vérifier son état), affichez ses journaux pour voir la sortie. Pour afficher les journaux du conteneur sans variables d’environnement, affectez à ContainerGroupName la valeur mycontainer1 au lieu de mycontainer2.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>Exemple de sortie sans variables d’environnement

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

## <a name="example-output-with-environment-variables"></a>Exemple de sortie avec des variables d’environnement

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment personnaliser l’entrée de votre conteneur, découvrez comment conserver la sortie des conteneurs qui s’exécutent jusqu’à la fin.
> [!div class="nextstepaction"]
> [Montage d’un partage de fichiers Azure avec Azure Container Instances](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show