---
title: "Configurer l’identité du service administré sur une machine virtuelle Azure à l’aide d’Azure CLI"
description: "Instructions détaillées sur la configuration de l’identité du service administré (MSI) sur une machine virtuelle Azure, à l’aide d’Azure CLI."
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 0863d5bec71a79f28017582eaa111f6c4a97c1ec
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Configurer l’identité du service administré (MSI) de la machine virtuelle à l’aide d’Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à activer et à supprimer l’identité du service administré d’une machine virtuelle Azure à l’aide de l’interface de ligne de commande Azure.

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :

- Utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
- Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
- [Installez la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou version ultérieure) si vous préférez utiliser une console CLI locale. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Activer l’identité du service administré lors de la création d’une machine virtuelle Azure

Pour créer une machine virtuelle compatible avec l’identité du service administré :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az_login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer la machine virtuelle :

   ```azurecli-interactive
   az login
   ```

2. Créez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#terminology) pour l’imbrication et le déploiement de votre machine virtuelle et de ses ressources connexes, à l’aide de la commande [az group create](/cli/azure/group/#az_group_create). Vous pouvez ignorer cette étape si vous possédez déjà le groupe de ressources que vous souhaitez utiliser à la place :

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm/#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVM* avec une identité du service administré, comme le demande le paramètre `--assign-identity`. Les paramètres `--admin-username` et `--admin-password` spécifient le nom d’utilisateur et le mot de passe d’administration du compte pour la connexion à la machine virtuelle. Mettez à jour ces valeurs en fonction de votre environnement : 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Activer l’identité du service administré sur une machine virtuelle Azure existante

Si vous devez activer l’identité du service administré sur une machine virtuelle existante :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az_login). Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

   ```azurecli-interactive
   az login
   ```

2. Utilisez la commande [az vm assign-identity](/cli/azure/vm/#az_vm_assign_identity) avec le paramètre `--assign-identity` pour ajouter une identité du service administré à une machine virtuelle existante :

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Supprimer l’identité du service administré d’une machine virtuelle Azure

Si vous disposez d’une machine virtuelle qui ne nécessite plus d’identité du service administré :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az_login). Utilisez un compte associé à l’abonnement Azure qui contient la machine virtuelle. Vérifiez également que votre compte appartient à un rôle qui vous donne des autorisations en écriture sur la machine virtuelle, comme « Contributeur de machines virtuelles » :

   ```azurecli-interactive
   az login
   ```

2. Utilisez le commutateur `-n ManagedIdentityExtensionForWindows` ou `-n ManagedIdentityExtensionForLinux` (selon le type de machine virtuelle) avec [az vm extension delete](https://docs.microsoft.com/cli/azure/vm/#assign-identity) pour supprimer l’identité du service administré :

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>Contenu connexe

- [Vue d’ensemble de l’identité du service administré](overview.md)
- Pour obtenir les guides de démarrages rapides complets sur la création de machines virtuelles Azure, consultez : 

  - [Créer une machine virtuelle Windows avec l’interface Azure CLI](../../virtual-machines/windows/quick-create-cli.md)  
  - [Créer une machine virtuelle Linux avec Azure CLI](../../virtual-machines/linux/quick-create-cli.md) 

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
















