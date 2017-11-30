---
title: "Exemple de script Azure PowerShell - Supprimer des conteneurs par préfixe | Microsoft Docs"
description: "Supprimez des conteneurs d’objets blob Stockage Azure selon un préfixe de nom de conteneur."
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 629189b9dbe2327763d364abc95f49539a312c53
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2017
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Supprimer des conteneurs selon un préfixe de nom de conteneur

Ce script supprime des conteneurs dans le stockage d’objets blob Azure selon un préfixe contenu dans le nom du conteneur.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, les conteneurs restants, ainsi que toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes pour supprimer des conteneurs selon un préfixe de nom de conteneur. Chaque élément du tableau renvoie à une documentation spécifique de la commande.

| Commande | Remarques |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Obtient un compte de stockage spécifié ou tous les comptes de stockage dans un groupe de ressources ou l’abonnement. |
| [Get-AzureStorageContainer](/powershell/module/azure.storage/get-azurestoragecontainer) | Répertorie les conteneurs de stockage associés à un compte de stockage. |
| [Remove-AzureStorageContainer](/powershell/module/azure.storage/remove-azurestoragecontainer) | Supprime le conteneur de stockage spécifié. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples de scripts PowerShell de stockage supplémentaires dans les [exemples PowerShell pour le stockage d’objets blob Azure](../blobs/storage-samples-blobs-powershell.md).
