---
title: "Guide pratique pour attribuer à une identité du service administré affectée par l’utilisateur un accès à une ressource Azure, à l’aide d’Azure CLI"
description: "Instructions détaillées pour attribuer, à une identité du service administré affectée par l’utilisateur à une ressource, un accès à une autre ressource à l’aide d’Azure CLI."
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
ms.openlocfilehash: 5bea41999f59fe8be7ae0a0bd5b726527beeddd5
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Attribuer à une identité du service administré (MSI) affectée par l’utilisateur un accès à une ressource à l’aide d’Azure CLI

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Une fois que vous avez créé une identité MSI affectée par l’utilisateur, vous pouvez lui accorder un accès à une autre ressource, tout comme n’importe quel principal de sécurité. Cet exemple montre comment accorder à l’identité MSI affectée par l’utilisateur l’accès à un compte de stockage Azure à l’aide d’Azure CLI.

## <a name="prerequisites"></a>configuration requise

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Pour exécuter les exemples de script CLI dans ce didacticiel, vous avez deux possibilités :

- Utiliser [Azure Cloud Shell](~/articles/cloud-shell/overview.md) dans le portail Azure ou via le bouton « Essayer » situé en haut à droite de chaque bloc de code.
- [Installer la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou ultérieure) si vous préférez utiliser une console CLI locale. Connectez-vous ensuite à Azure avec la commande [az login](/cli/azure/#az_login). Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez déployer l’identité MSI affectée par l’utilisateur et la machine virtuelle :

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Utiliser le contrôle d’accès en fonction du rôle (RBAC) pour attribuer à l’identité du service administré un accès à une autre ressource

Pour que vous puissiez utiliser une identité MSI avec une ressource hôte (par exemple une machine virtuelle), pour l’accès à une ressource ou la connexion, l’accès à la ressource doit d’abord être accordé à l’identité MSI par le biais d’une attribution de rôle. Vous pouvez effectuer cette opération avant ou après avoir associé l’identité MSI à l’hôte. Pour connaître la procédure complète de création et d’association à une machine virtuelle, consultez [Guide pratique pour configurer une identité du service administré affectée par l’utilisateur pour une machine virtuelle, à l’aide d’Azure CLI](msi-qs-configure-cli-windows-vm.md).

Dans l’exemple suivant, l’accès à un compte de stockage est accordé à une identité MSI affectée par l’utilisateur.  

1. Pour accorder l’accès à l’identité MSI, vous avez besoin de l’ID client (également appelé ID d’application) du principal de service de l’identité MSI. L’ID client est fourni par [az identity create](/cli/azure/identity#az_identity_create) lors du provisionnement de l’identité MSI et de son principal de service (illustré ici à titre de référence) :

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   Une réponse correcte contient l’ID client du principal de service de l’identité MSI affectée par l’utilisateur, dans la propriété `clientId` :

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. Une fois l’ID client connu, utilisez [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) pour accorder à l’identité MSI l’accès à une autre ressource. Veillez à utiliser votre ID client pour le paramètre `--assignee`, et l’ID d’abonnement et le nom du groupe de ressources correspondants, tels qu’ils sont retournés quand vous exécutez `az identity create`. Ici, on accorde à l’identité MSI un accès en « Lecture » à un compte de stockage nommé « myStorageAcct » :

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   Une réponse correcte ressemble à la sortie suivante :

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).
- Pour activer une identité MSI affectée par l’utilisateur sur une machine virtuelle Azure, consultez [Configurer une identité MSI (Managed Service Identity) affectée par l’utilisateur pour une machine virtuelle, à l’aide d’Azure CLI](msi-qs-configure-cli-windows-vm.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.

