---
title: "Guide pratique pour configurer une identité MSI affectée à l’utilisateur sur une machine virtuelle Azure avec Azure CLI"
description: "Instructions détaillées pour configurer une identité MSI (Managed Service Identity) affectée à l’utilisateur sur une machine virtuelle Azure avec Azure CLI."
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 495ed6daf0d73d89a4bc572f6bccf294cee7decb
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-azure-cli"></a>Configurer une identité MSI (Managed Service Identity) affectée à l’utilisateur pour une machine virtuelle avec Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

L’identité MSI fournit aux services Azure une identité managée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez apprendre à activer et à supprimer l’identité MSI affectée à l’utilisateur pour une machine virtuelle Azure avec Azure CLI.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Pour exécuter les exemples de script CLI dans ce didacticiel, vous avez deux possibilités :

- Utiliser [Azure Cloud Shell](~/articles/cloud-shell/overview.md) dans le portail Azure ou via le bouton « Essayer » situé en haut à droite de chaque bloc de code.
- [Installer la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou ultérieure) si vous préférez utiliser une console CLI locale. Connectez-vous ensuite à Azure avec la commande [az login](/cli/azure/#az_login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer l’identité MSI affectée par l’utilisateur et la machine virtuelle :

   ```azurecli
   az login
   ```

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Activer l’identité du service administré lors de la création d’une machine virtuelle Azure

Cette section vous guide tout au long de la création de la machine virtuelle et de l’attribution à la machine virtuelle de l’identité MSI affectée à l’utilisateur. Si vous disposez déjà d’une machine virtuelle que vous souhaitez utiliser, ignorez cette section et passez à la suivante.

1. Vous pouvez ignorer cette étape si vous disposez déjà d’un groupe de ressources que vous souhaitez utiliser. Créez un [groupe de ressources](~/articles/azure-resource-manager/resource-group-overview.md#terminology) pour contenir et déployer votre identité MSI en utilisant la commande [az group create](/cli/azure/group/#az_group_create). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<LOCATION>` par vos propres valeurs. :

   ```azurecli-interactive 
   az group create --name <RESOURCE GROUP> --location <LOCATION>
   ```

2. Créez une identité MSI affectée à l’utilisateur avec la commande [az identity create](/cli/azure/identity#az_identity_create).  Le paramètre `-g` spécifie le groupe de ressources où l’identité MSI est créée, tandis que le paramètre `-n` indique son nom. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<MSI NAME>` par vos propres valeurs :

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La réponse contient les détails de l’identité MSI affectée à l’utilisateur qui a été créée, comme dans l’exemple suivant. La valeur `id` de ressource affectée à l’identité MSI est utilisée dans l’étape suivante.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

3. Créez une machine virtuelle à l’aide de la commande [az vm create](/cli/azure/vm/#az_vm_create). L’exemple suivant crée une machine virtuelle associée à la nouvelle identité MSI affectée à l’utilisateur, tel que spécifié par le paramètre `--assign-identity`. Veillez à remplacer les valeurs des paramètres `<RESOURCE GROUP>`, `<VM NAME>`, `<USER NAME>`, `<PASSWORD>` et `<`MSI ID` parameter values with your own values. For ` par vos propres valeurs. Pour <MSI ID>`, use the user-assigned MSI's resource `, utilisez la propriété `id` de ressource de l’identité MSI affectée à l’utilisateur, créée à l’étape précédente : 

   ```azurecli-interactive 
   az vm create --resource-group <RESOURCE GROUP> --name <VM NAME> --image UbuntuLTS --admin-username <USER NAME> --admin-password <PASSWORD> --assign-identity <MSI ID>
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Activer l’identité du service administré sur une machine virtuelle Azure existante

1. Créez une identité MSI affectée à l’utilisateur avec la commande [az identity create](/cli/azure/identity#az_identity_create).  Le paramètre `-g` spécifie le groupe de ressources où l’identité MSI est créée, tandis que le paramètre `-n` indique son nom. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<MSI NAME>` par vos propres valeurs :

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```
La réponse contient les détails de l’identité MSI affectée à l’utilisateur qui a été créée, comme dans l’exemple suivant. La valeur `id` de ressource affectée à l’identité MSI est utilisée dans l’étape suivante.

   ```json
   {
        "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=73444643-8088-4d70-9532-c3a0fdc190fz",
        "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
        "location": "westcentralus",
        "name": "<MSI NAME>",
        "principalId": "e5fdfdc1-ed84-4d48-8551-fe9fb9dedfll",
        "resourceGroup": "<RESOURCE GROUP>",
        "tags": {},
        "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"    
   }
   ```

2. Attribuez l’identité MSI affectée à l’utilisateur à votre machine virtuelle à l’aide de la commande [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VM NAME>` par vos propres valeurs. Le `<MSI ID>` est la propriété `id` de ressource de l’identité MSI affectée à l’utilisateur, tel que créée à l’étape précédente :

    ```azurecli-interactive
    az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI ID>
    ```

## <a name="remove-msi-from-an-azure-vm"></a>Supprimer l’identité du service administré d’une machine virtuelle Azure

1. Supprimez de votre machine virtuelle l’identité MSI affectée à l’utilisateur à l’aide de la commande [az vm remove-identity](/cli/azure/vm#az_vm_remove_identity). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VM NAME>` par vos propres valeurs. Le `<MSI NAME>` est la propriété `name` de l’identité MSI affectée à l’utilisateur, comme indiqué durant la commande `az identity create` (voir les exemples dans les sections précédentes) :

   ```azurecli-interactive
   az vm remove-identity -g <RESOURCE GROUP> -n <VM NAME> --identities <MSI NAME>
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble de l’identité du service administré](msi-overview.md)
- Pour obtenir les guides de démarrages rapides complets sur la création de machines virtuelles Azure, consultez : 

  - [Créer une machine virtuelle Windows avec l’interface Azure CLI](~/articles/virtual-machines/windows/quick-create-cli.md)  
  - [Créer une machine virtuelle Linux avec Azure CLI](~/articles/virtual-machines/linux/quick-create-cli.md) 

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
















