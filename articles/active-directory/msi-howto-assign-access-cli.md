---
title: "Comment attribuer à une identité du service administré un accès à une ressource Azure, à l’aide d’Azure CLI"
description: "Instructions détaillées pour attribuer une identité du service administré à une ressource, et un accès à une autre ressource, à l’aide d’Azure CLI."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: be80065f83e8c80e7c1d6ab383cea04e0d2679a0
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Attribuer à une identité de service administré (MSI) un accès à une ressource à l’aide d’Azure CLI

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Une fois que vous avez configuré une ressource Azure avec une identité du service administré, vous pouvez accorder à cette dernière un accès à une autre ressource, tout comme n’importe quel principal de sécurité. Cet exemple montre comment accorder à l’identité du service administré d’une machine virtuelle Azure l’accès à un compte de stockage Azure, à l’aide d’Azure CLI.

## <a name="prerequisites"></a>configuration requise

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :

- Utilisez [Azure Cloud Shell](../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
- Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
- [Installez la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou version ultérieure) si vous préférez utiliser une console CLI locale. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Utiliser le contrôle d’accès en fonction du rôle (RBAC) pour attribuer à l’identité du service administré un accès à une autre ressource

Après avoir activé l’identité du service administré sur une ressource Azure, [telle qu’une machine virtuelle Azure](msi-qs-configure-cli-windows-vm.md) : 

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/#az_login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer la machine virtuelle :

   ```azurecli-interactive
   az login
   ```

2. Dans cet exemple, nous accordons à une machine virtuelle Azure l’accès à un compte de stockage. Tout d’abord, nous utilisons [az resource list](/cli/azure/resource/#az_resource_list) pour obtenir le principal de service pour la machine virtuelle nommée « myVM », qui a été créé lorsque nous avons activé l’identité du service administré sur la machine virtuelle :

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```

3. Une fois l’ID de principal du service obtenue, nous utilisons [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) pour accorder au « Lecteur » de la machine virtuelle l’accès à un compte de stockage appelé « myStorageAcct » :

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Résolution de problèmes

Si l’identité du service administré pour la ressource n’apparaît pas dans la liste des identités disponibles, vérifiez si elle a été activée correctement. Dans notre cas, nous pouvons revenir à la machine virtuelle Azure dans le [portail Azure](https://portal.azure.com) et :

- Examinez la page « Configuration » et s’assurer que le paramètre d’identité du service administré est activé.
- Examinez la page « Extensions » et assurez-vous que l’extension de l’identité du service administré est déployée avec succès.

En cas d’inexactitude, vous devez redéployer l’identité du service administré sur votre ressource, ou résoudre le problème de déploiement.

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).
- Pour activer l’identité du service administré sur une machine virtuelle Azure, consultez [Configurer l’Identité du service administré (MSI) d’une machine virtuelle Azure à l’aide d’Azure CLI](msi-qs-configure-cli-windows-vm.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

