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
ms.openlocfilehash: 90a7ec3059b6905e4aa660f538c299f3e8dedaae
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Attribuer à une identité de service administré (MSI) un accès à une ressource à l’aide d’Azure CLI

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Une fois que vous avez configuré une ressource Azure avec une identité du service administré, vous pouvez accorder à cette dernière un accès à une autre ressource, tout comme n’importe quel principal de sécurité. Cet exemple montre comment accorder à l’identité de service administré (MSI) d’une machine virtuelle Azure ou du groupe de machines virtuelles identique l’accès à un compte de stockage Azure, à l’aide d’Azure CLI.

## <a name="prerequisites"></a>configuration requise

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :

- Utilisez [Azure Cloud Shell](../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
- Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
- [Installez la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou version ultérieure) si vous préférez utiliser une console CLI locale. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Utiliser le contrôle d’accès en fonction du rôle (RBAC) pour attribuer à l’identité du service administré un accès à une autre ressource

Après avoir activé l’identité de service administré sur une ressource Azure, comme une [machine virtuelle Azure](msi-qs-configure-cli-windows-vm.md) ou un [groupe de machines virtuelles identiques Azure](msi-qs-configure-cli-windows-vmss.md) : 

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/#az_login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer la machine virtuelle ou un groupe de machines virtuelles identiques :

   ```azurecli-interactive
   az login
   ```

2. Dans cet exemple, nous accordons à une machine virtuelle Azure l’accès à un compte de stockage. Tout d’abord, nous utilisons [az resource list](/cli/azure/resource/#az_resource_list) pour obtenir le principal du service pour la machine virtuelle nommée « myVM » :

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Pour un groupe de machines virtuelles identiques, la commande est la même à l’exception que, ici, vous obtenez le principal du service pour le groupe de machines virtuelles identiques nommé « DevTestVMSS » :
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Une fois l’ID de principal du service obtenu, utilisez [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) pour accorder au « Lecteur » de la machine virtuelle ou du groupe de machines virtuelles identiques l’accès à un compte de stockage appelé « myStorageAcct » :

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Résolution de problèmes

Si l’identité du service administré pour la ressource n’apparaît pas dans la liste des identités disponibles, vérifiez si elle a été activée correctement. Dans notre cas, nous pouvons revenir à la machine virtuelle Azure ou au groupe de machines virtuelles identiques dans le [portail Azure](https://portal.azure.com) et :

- Examinez la page « Configuration » et s’assurer que le paramètre d’identité du service administré est activé.
- Examinez la page « Extensions » et vérifiez que l’extension de l’identité de service administré a été correctement déployée (la page **Extensions** n’est pas disponible pour un groupe de machines virtuelles identiques Azure).

En cas d’inexactitude, vous devez redéployer l’identité du service administré sur votre ressource, ou résoudre le problème de déploiement.

## <a name="related-content"></a>Contenu connexe

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).
- Pour activer l’identité de service administré sur une machine virtuelle Azure, consultez [Configurer l’identité de service administré (MSI) d’une machine virtuelle Azure à l’aide d’Azure CLI](msi-qs-configure-cli-windows-vm.md).
- Pour activer l’identité de service administré sur un groupe de machines virtuelles identiques Azure, consultez [Configurer l’identité de service administré (MSI) d’un groupe de machines virtuelles identiques Azure à l’aide du portail Azure](msi-qs-configure-portal-windows-vmss.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

