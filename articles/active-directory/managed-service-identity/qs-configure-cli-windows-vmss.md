---
title: "Configurer l’identité du service administré (MSI) sur un groupe de machines virtuelles identiques Azure à l’aide d’Azure CLI"
description: "Instructions détaillées sur la configuration de l’identité du service administré (MSI) sur un groupe de machines virtuelles identiques Azure, à l’aide d’Azure CLI."
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
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: d7a7b0c8b3f9bf0279282dbf1fed4fc8163d9170
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Configurer l’identité du service administré (MSI) d’un groupe de machines virtuelles identiques à l’aide d’Azure CLI

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

L’identité du service administré fournit des services Azure avec une identité gérée automatiquement dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier sur n’importe quel service prenant en charge l’authentification Azure AD, sans avoir d’informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à activer et à supprimer l’identité du service administré d’un groupe de machines virtuelles identiques à l’aide d’Azure CLI.

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pour exécuter les exemples de script d’Azure CLI, vous disposez de trois options :

- Utilisez [Azure Cloud Shell](../../cloud-shell/overview.md) à partir du portail Azure (voir section suivante).
- Utilisez l’interface intégrée Azure Cloud Shell via le bouton « Essayer », situé dans le coin supérieur droit de chaque bloc de code.
- [Installez la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 ou version ultérieure) si vous préférez utiliser une console CLI locale. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Activer l’identité du service administré lors de la création d’un groupe de machines virtuelles identiques Azure

Pour créer un groupe de machines virtuelles identiques dont l’identité du service administré est activée :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az_login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer le groupe de machines virtuelles identiques :

   ```azurecli-interactive
   az login
   ```

2. Créez un [groupe de ressources](../../azure-resource-manager/resource-group-overview.md#terminology) pour l’imbrication et le déploiement de votre groupe de machines virtuelles identiques et de ses ressources connexes, à l’aide de la commande [az group create](/cli/azure/group/#az_group_create). Vous pouvez ignorer cette étape si vous possédez déjà le groupe de ressources que vous souhaitez utiliser à la place :

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Créez un groupe de machines virtuelles identiques avec la commande [az vmss create](/cli/azure/vmss/#az_vmss_create). L’exemple suivant crée un groupe de machines virtuelles identiques nommé *myVMSS* avec une identité du service administré, comme le demande le paramètre `--assign-identity`. Les paramètres `--admin-username` et `--admin-password` spécifient le nom d’utilisateur et le mot de passe d’administration du compte pour la connexion à la machine virtuelle. Mettez à jour ces valeurs en fonction de votre environnement : 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>Activer l’identité du service administré sur un groupe de machines virtuelles identiques Azure existant

Si vous devez activer l’identité du service administré sur un groupe de machines virtuelles identiques Azure existant :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az_login). Utilisez un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

   ```azurecli-interactive
   az login
   ```

2. Utilisez la commande [az vmss assign-identity](/cli/azure/vm/#az_vmss_assign_identity) avec le paramètre `--assign-identity` pour ajouter une identité du service administré à une machine virtuelle existante :

   ```azurecli-interactive
   az vmss assign-identity -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Supprimer l’identité du service administré d’un groupe de machines virtuelles identiques Azure

Si vous disposez d’un groupe de machines virtuelles identiques qui ne nécessite plus d’identité du service administré :

1. Si vous utilisez l’interface de ligne de commande Azure dans une console locale, commencez par vous connecter à Azure avec [az login](/cli/azure/reference-index#az_login). Utilisez un compte associé à l’abonnement Azure qui contient le groupe de machines virtuelles identiques.

   ```azurecli-interactive
   az login
   ```

2. Utilisez le commutateur `--identities` avec la commande [az vmss remove-identity](/cli/azure/vmss/#az_vmss_remove_identity) pour supprimer l’identité du service administré :

   ```azurecli-interactive
   az vmss remove-identity -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’identité du service administré](overview.md)
- Pour le démarrage rapide de la création d’un groupe de machines virtuelles identiques Azure, consultez : 

  - [Créer un groupe de machines virtuelles identiques avec CLI](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
















