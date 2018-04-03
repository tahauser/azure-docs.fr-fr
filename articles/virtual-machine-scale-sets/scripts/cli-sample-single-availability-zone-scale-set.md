---
title: 'Exemples Azure CLI 2.0 : groupe identique dans une zone unique | Microsoft Docs'
description: Exemples Azure CLI 2.0
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f881d90628f5e4b5c4fc3fe56ecb1b3377d8ba42
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Créer un groupe de machines virtuelles identiques dans une zone unique à l’aide de PowerShell
Ce script permet de créer un groupe de machines virtuelles identiques exécutant Ubuntu dans une zone de disponibilité unique. Une fois que vous avez exécuté le script, vous pouvez accéder à la machine virtuelle via RDP.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script
[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.sh "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement
Exécutez la commande suivante pour supprimer le groupe de ressources, le groupe identique et toutes les ressources associées.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explication du script
Ce script utilise les commandes suivantes pour créer un groupe de ressources, un groupe de machines virtuelles identiques, ainsi que toutes les ressources associées. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [az group create](/cli/azure/ad/group#az_ad_group_create) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [az vmss create](/cli/azure/vmss#az_vmss_create) | Crée le groupe de machines virtuelles identiques et le connecte au réseau virtuel, au sous-réseau et au groupe de sécurité réseau. Un équilibreur de charge est également créé pour distribuer le trafic vers les différentes instances de machine virtuelle. Cette commande spécifie aussi l’image de machine virtuelle à utiliser ainsi que les informations d’identification d’administration.  |
| [az group delete](/cli/azure/ad/group#delete) | Supprime un groupe de ressources, y compris toutes les ressources imbriquées. |

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur Azure CLI 2.0, consultez la [documentation sur Azure CLI 2.0](https://docs.microsoft.com/cli/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure CLI 2.0 de groupes de machines virtuelles identiques dans la [documentation relative aux groupes de machines virtuelles identiques Azure](../cli-samples.md).