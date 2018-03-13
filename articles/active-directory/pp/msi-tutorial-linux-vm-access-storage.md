---
title: "Utiliser une identité MSI affectée à l’utilisateur sur une machine virtuelle Linux pour accéder au stockage Azure"
description: "Ce didacticiel vous guide tout au long de l’utilisation d’une identité MSI (Managed Service Identity) affectée à l’utilisateur sur une machine virtuelle Linux pour accéder au stockage Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: arluca
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1d8641fef3a60ffcde6d0a4ac7e30d4e6cd3b169
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/03/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-storage"></a>Utiliser une identité MSI (Managed Service Identity) affectée à l’utilisateur sur une machine virtuelle Linux pour accéder au stockage Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ce didacticiel vous montre comment créer et utiliser une identité MSI (Managed Service Identity) affectée à l’utilisateur depuis une machine virtuelle Linux, et comment s’en servir ensuite pour accéder au stockage Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une identité MSI (Managed Service Identity) affectée à l’utilisateur
> * Attribuer l’identité MSI affectée à l’utilisateur à une machine virtuelle Linux
> * Accorder à l’identité MSI l’accès à une instance de stockage Azure
> * Obtenir un jeton d’accès par l’intermédiaire de l’identité MSI affectée à l’utilisateur, et l’utiliser pour accéder au stockage Azure

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Pour exécuter les exemples de script CLI dans ce didacticiel, vous avez deux possibilités :

- Utiliser [Azure Cloud Shell](~/articles/cloud-shell/overview.md) dans le portail Azure ou via le bouton « Essayer » situé en haut à droite de chaque bloc de code.
- [Installer la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou ultérieure) si vous préférez utiliser une console CLI locale.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Créez d’abord une machine virtuelle Linux. Vous pouvez également activer l’identité MSI sur une machine virtuelle existante si vous préférez.

1. Cliquez sur le bouton **+/Créer un service** dans l’angle supérieur gauche du portail Azure.
2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS**.
3. Saisissez les informations de la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH** ou **Mot de passe**. Les informations d’identification créées vous permettent de vous connecter à la machine virtuelle.

    ![Texte de remplacement d’image](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Choisissez un **Abonnement** pour la machine virtuelle dans la liste déroulante.
5. Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer la machine virtuelle, choisissez **Créer nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre de type de disque pris en charge. Conservez les valeurs par défaut dans le panneau des paramètres et cliquez sur **OK**.

## <a name="create-a-user-assigned-msi"></a>Créer une identité MSI affectée à l’utilisateur

1. Si vous utilisez la console CLI (au lieu d’une session Azure Cloud Shell), commencez par vous connecter à Azure. Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez créer l’identité MSI :

    ```azurecli
    az login
    ```

2. Créez une identité MSI affectée à l’utilisateur avec la commande [az identity create](/cli/azure/identity#az_identity_create). Le paramètre `-g` spécifie le groupe de ressources où l’identité MSI est créée, tandis que le paramètre `-n` indique son nom. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<MSI NAME>` par vos propres valeurs :

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La réponse contient les détails de l’identité MSI affectée à l’utilisateur qui a été créée, comme dans l’exemple suivant. Notez la valeur `id` pour votre identité MSI, car elle est utilisée à l’étape suivante :

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "9012",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Attribuer votre identité MSI affectée à l’utilisateur à votre machine virtuelle Linux

Contrairement à une identité MSI affectée au système, une identité MSI affectée à l’utilisateur peut être utilisée par des clients sur plusieurs ressources Azure. Pour ce didacticiel, vous l’attribuez à une seule machine virtuelle. Vous pouvez également l’attribuer à plusieurs machines virtuelles.

Attribuez l’identité MSI affectée à l’utilisateur à votre machine virtuelle Linux à l’aide de la commande [az vm assign-identity](/cli/azure/vm#az_vm_assign_identity). N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<VM NAME>` par vos propres valeurs. Utilisez la propriété `id` retournée à l’étape précédente comme valeur du paramètre `--identities` :

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-storage-account"></a>Créez un compte de stockage. 

Si vous n’en avez pas déjà un, créez maintenant un compte de stockage. Vous pouvez également ignorer cette étape et utiliser un compte de stockage existant, si vous préférez. 

1. Cliquez sur le bouton **+/Créer un service** dans l’angle supérieur gauche du portail Azure.
2. Cliquez sur **Stockage**, puis sur **Compte de stockage** ; un nouveau panneau « Créer un compte de stockage » s’affiche.
3. Entrez un **Nom** pour le compte de stockage, vous allez vous en servir plus tard.  
4. **Modèle de déploiement** et **Type de compte** doivent être respectivement définis sur « Gestionnaire de ressources » et « Usage général ». 
5. Assurez-vous que les champs **Abonnement** et **Groupe de ressources** correspondent à ceux que vous avez spécifiés lorsque vous avez créé votre machine virtuelle à l’étape précédente.
6. Cliquez sur **Créer**.

    ![Créer un nouveau compte de stockage](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Création d’un conteneur d’objets blob dans le compte de stockage

Étant donné que les fichiers nécessitent un stockage d’objets blob, vous devez créer un conteneur d’objets blob dans lequel stocker le fichier. Chargez et téléchargez ensuite un fichier sur le conteneur d’objets blob, dans le nouveau compte de stockage.

1. Revenez à votre compte de stockage nouvellement créé.
2. Cliquez sur le lien **Conteneurs** sur la gauche, sous « Service blob ».
3. Cliquez sur **+ Conteneur** en haut de la page et un panneau « Nouveau conteneur » apparait.
4. Nommez le conteneur, sélectionnez un niveau d’accès, puis cliquez sur **OK**. Le nom spécifié est également utilisé plus loin dans le didacticiel. 

    ![Créer un conteneur de stockage](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Chargez un fichier sur le conteneur nouvellement créé en cliquant sur le nom du conteneur, puis sur **Charger** ; sélectionnez ensuite un fichier et cliquez sur **Charger**.

    ![Charger un fichier texte](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-user-assigned-msi-access-to-an-azure-storage-container"></a>Accorder à votre identité MSI affectée à l’utilisateur l’accès à un conteneur de stockage Azure

Par l’intermédiaire d’une identité MSI, votre code peut obtenir des jetons d’accès pour vous authentifier sur des ressources qui prennent en charge l’authentification Azure AD. Dans ce didacticiel, vous utilisez le stockage Azure.

Vous accordez tout d’abord à l’identité MSI l’accès à un conteneur de stockage Azure. Ici, en l’occurrence, vous utilisez le conteneur créé précédemment. Mettez à jour les valeurs de `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>` et `<CONTAINER NAME>` en fonction de votre environnement. En outre, remplacez `<MSI PRINCIPALID>` par la propriété `principalId` retournée par la commande `az identity create` dans [Créer une identité MSI affectée à l’utilisateur](#create-a-user-assigned-msi) :

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/blobServices/default/containers/<CONTAINER NAME>"
```

La réponse comprend les détails de l’attribution de rôle créée :

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.storage/storageAccounts/<STORAGE ACCOUNT NAME>/blogServices/default/<CONTAINER NAME>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msis-identity-and-use-it-to-call-azure-storage"></a>Obtenir un jeton d’accès via l’identité MSI affectée à l’utilisateur et l’utiliser pour appeler le stockage Azure

Pour la suite de ce didacticiel, vous devez utiliser la machine virtuelle que vous avez créée précédemment.

Pour effectuer cette procédure, vous avez besoin d’un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/about). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Dans le portail Azure, accédez à **Machines virtuelles**, accédez à votre machine virtuelle Linux, puis, en haut de la page **Vue d’ensemble**, cliquez sur **Se connecter**. Copiez la chaîne permettant de se connecter à votre machine virtuelle.
2. **Connectez-vous** à la machine virtuelle à l’aide du client SSH de votre choix. 
3. Dans la fenêtre de terminal, envoyez une requête contenant CURL au point de terminaison MSI local en vue d’obtenir un jeton d’accès pour le stockage Azure.

   La requête CURL permettant d’acquérir un jeton d’accès est illustrée dans l’exemple suivant. Veillez à remplacer `<CLIENT ID>` par la propriété `clientId` retournée avec la commande `az identity create` dans [Créer une identité MSI affectée à l’utilisateur](#create-a-user-assigned-msi) :

   ```bash
   curl -H Metadata:true "http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fstorage.azure.com/&client_id=<CLIENT ID>"
   ```

   > [!NOTE]
   > Dans la requête précédente, la valeur du paramètre `resource` doit correspondre exactement à ce qui est attendu par Azure AD. Lorsque vous utilisez l’ID de ressource du stockage Azure, vous devez inclure la barre oblique de fin à l’URI.
   > Dans la réponse suivante, l’élément access_token a été raccourci par souci de concision.

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
   "refresh_token":"",
   "expires_in":"3599",
   "expires_on":"1504130527",
   "not_before":"1504126627",
   "resource":"https://storage.azure.com",
   "token_type":"Bearer"}
   ```

4. Utilisez à présent le jeton d’accès pour vous rendre sur le stockage Azure, par exemple pour y lire le contenu de l’exemple de fichier précédemment chargé sur le conteneur. Remplacez les valeurs de `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` et `<FILE NAME>` par les valeurs que vous avez spécifiées auparavant, et `<ACCESS TOKEN>` par le jeton retourné à l’étape précédente.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME>?api-version=2017-11-09 -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   La réponse contient le contenu du fichier :

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>étapes suivantes

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](msi-overview.md).
- Pour savoir comment suivre ce didacticiel en utilisant des informations d’identification de stockage SAP, consultez [Utiliser l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux pour accéder au stockage Azure à l’aide d’informations d’identification SAP](msi-tutorial-linux-vm-access-storage-sas.md).
- Pour plus d’informations sur la fonctionnalité de SAP de compte de Stockage Azure, voir :
  - [Utilisation des signatures d’accès partagé (SAP)](~/articles/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Construction d’un service SAP](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.





