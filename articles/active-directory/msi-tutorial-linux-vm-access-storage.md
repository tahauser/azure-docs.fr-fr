---
title: "Utiliser une MSI de machine virtuelle Linux pour accéder au stockage Azure"
description: "Ce didacticiel vous guide tout au long de l’utilisation d’une identité MSI (Managed Service Identity) sur une machine virtuelle Linux pour accéder au stockage Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: de397117f6891d28d24a4279d94e4e4ecc1e7afc
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2018
---
# <a name="use-a-linux-vm-managed-service-identity-to-access-azure-storage-via-access-key"></a>Utiliser l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux pour accéder au stockage Azure via une clé d’accès

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous montre comment activer une identité MSI (Managed Service Identity) sur une machine virtuelle Linux, puis utiliser cette identité pour accéder aux clés de stockage. Vous pouvez utiliser les clés d’accès de stockage comme d’habitude lors de l’exécution d’opérations de stockage, par exemple lors de l’utilisation du SDK Stockage. Pour ce didacticiel, nous chargeons et téléchargeons des objets blob à l’aide de Azure CLI. Vous apprendrez à :

> [!div class="checklist"]
> * Activer l’identité du service administré sur une machine virtuelle Linux 
> * Autoriser votre machine virtuelle à accéder aux clés d’accès de stockage dans le Gestionnaire des ressources 
> * Obtenir un jeton d’accès à l’aide de l’identité de votre machine virtuelle et l’utiliser pour récupérer les clés d’accès de stockage à partir du Gestionnaire des ressources  

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure
Connectez-vous au portail Azure depuis l’adresse [https://portal.azure.com](https://portal.azure.com).


## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Pour ce didacticiel, nous créons une machine virtuelle Linux. Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1. Cliquez sur le bouton **+/Créer un service** dans l’angle supérieur gauche du portail Azure.
2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS**.
3. Saisissez les informations de la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH** ou **Mot de passe**. Les informations d’identification créées vous permettent de vous connecter à la machine virtuelle.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Choisissez un **Abonnement** pour la machine virtuelle dans la liste déroulante.
5. Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer la machine virtuelle, choisissez **Créer nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre de type de disque pris en charge. Conservez les valeurs par défaut dans le panneau des paramètres et cliquez sur **OK**.

## <a name="enable-msi-on-your-vm"></a>Activer l’identité du service administré sur votre machine virtuelle

L’identité du service administré d’une machine virtuelle permet d’obtenir des jetons d’accès d’Azure AD sans avoir à placer des informations d’identification dans votre code. En arrière-plan, l’activation de l’identité du service administré entraîne deux actions : elle installe l’extension de l’identité du service administré de la machine virtuelle et active l’identité du service administré pour la machine virtuelle.  

1. Accédez au groupe de ressources de votre nouvelle machine virtuelle et sélectionnez la machine virtuelle que vous avez créée à l’étape précédente.
2. Dans les paramètres de la machine virtuelle situés sur la gauche, cliquez sur **Configuration**.
3. Pour enregistrer et activer l’identité du service administré, sélectionnez **Oui**. Si vous souhaitez la désactiver, sélectionnez Non.
4. Assurez-vous d’avoir cliqué sur **Enregistrer** pour enregistrer la configuration.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Si vous souhaitez vérifier les extensions présentes sur la machine virtuelle, cliquez sur **Extensions**. Si l’identité du service administré est activée, **ManagedIdentityExtensionforLinux** apparaît dans la liste.

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="create-a-storage-account"></a>Créez un compte de stockage. 

Si vous n’en avez pas déjà un, vous allez maintenant créer un compte de stockage.  Vous pouvez également ignorer cette étape et accorder l’accès MSI de votre machine virtuelle aux clés d’un compte de stockage existant. 

1. Cliquez sur le bouton **+/Créer un service** dans l’angle supérieur gauche du portail Azure.
2. Cliquez sur **Stockage**, puis **Compte de stockage**, et un nouveau panneau « Créer un compte de stockage » s’affiche.
3. Saisissez un **nom** pour le compte de stockage, vous l’utiliserez ultérieurement.  
4. **Modèle de déploiement** et **Type de compte** doivent être respectivement définis sur « Gestionnaire de ressources » et « Usage général ». 
5. Assurez-vous que les champs **Abonnement** et **Groupe de ressources** correspondent à ceux que vous avez spécifiés lorsque vous avez créé votre machine virtuelle à l’étape précédente.
6. Cliquez sur **Créer**.

    ![Créer un nouveau compte de stockage](media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Création d’un conteneur d’objets blob dans le compte de stockage

Plus tard, nous chargerons et téléchargerons un fichier vers le nouveau compte de stockage. Étant donné que les fichiers nécessitent un stockage d’objets blob, nous devons créer un conteneur d’objets blob dans lequel stocker le fichier.

1. Revenez à votre compte de stockage nouvellement créé.
2. Cliquez sur le lien **Conteneurs** sur la gauche, sous « Service blob ».
3. Cliquez sur **+ Conteneur** en haut de la page et un panneau « Nouveau conteneur » apparait.
4. Nommez le conteneur, sélectionnez un niveau d’accès, puis cliquez sur **OK**. Le nom spécifié sera utilisé plus loin dans le didacticiel. 

    ![Créer un conteneur de stockage](media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-msi-access-to-use-storage-account-access-keys"></a>Autoriser l’identité MSI de votre machine virtuelle à accéder aux clés d’accès du compte de stockage

Le stockage Azure ne prend pas en charge l’authentification Azure AD en mode natif.  Toutefois, vous pouvez utiliser une identité MSI pour récupérer les clés d’accès du compte de stockage à partir du Gestionnaire des ressources, puis utiliser ces clés pour accéder au stockage.  Dans cette étape, vous autorisez la MSI de votre machine virtuelle à accéder aux clés de votre compte de stockage.   

1. Revenez à votre compte de stockage nouvellement créé.
2. Cliquez sur le lien **(IAM) de contrôle d’accès** dans le panneau de gauche.  
3. Cliquez sur **+ Ajouter** en haut de la page pour ajouter une nouvelle attribution de rôle à votre machine virtuelle.
4. Définissez le **Rôle** sur « Rôle de service d’opérateur de clé de compte de stockage », sur le côté droit de la page. 
5. Dans la liste déroulante suivante, définissez **Attribuer l’accès à** sur la ressource « Machine virtuelle ».  
6. Ensuite, assurez-vous que l’abonnement approprié est répertorié dans la liste déroulante **Abonnement**, puis définissez **Groupe de ressources** sur « Tous les groupes de ressources ».  
7. Enfin, sous **Sélectionner**, choisissez votre machine virtuelle Linux dans la liste déroulante, puis cliquez sur **Enregistrer**. 

    ![Texte de remplacement d’image](media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Obtenir un jeton d’accès à l’aide de l’identité de la machine virtuelle et l’utiliser pour appeler Azure Resource Manager

Pour la suite de ce didacticiel, nous allons utiliser la machine virtuelle que nous avons créée précédemment.

Pour effectuer cette procédure, vous avez besoin d'un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](../virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Dans le portail Azure, accédez à **Machines virtuelles**, accédez à votre machine virtuelle Linux, puis, en haut de la page **Vue d’ensemble**, cliquez sur **Se connecter**. Copiez la chaîne permettant de se connecter à votre machine virtuelle. 
2. Connectez-vous à votre machine virtuelle en utilisant votre client SSH.  
3. Ensuite, vous serez invité à entrer le **Mot de passe** que vous avez ajouté à la création de la **machine virtuelle Linux**. Vous devez être connecté.  
4. Utilisez CURL pour obtenir un jeton d’accès à partir de Azure Resource Manager.  

    La requête et la réponse CURL pour le jeton d’accès se trouvent ci-dessous :
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true    
    ```
    
    > [!NOTE]
    > Dans la requête précédente, la valeur du paramètre « resource » doit correspondre exactement à ce qu’Azure AD attend. Lorsque vous utilisez l’ID de ressource Azure Resource Manager, vous devez inclure la barre oblique de fin à l’URI.
    > Dans la réponse suivante, l’élément access_token a été raccourci par souci de concision.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>Obtenir les clés d’accès du compte de stockage à partir d’Azure Resource Manager pour effectuer des appels de stockage  

Maintenant, utilisez CURL pour appeler le Gestionnaire des ressources à l’aide du jeton d’accès récupéré dans la section précédente, afin de récupérer la clé d’accès de stockage. Une fois la clé d’accès de stockage récupérée, nous pouvons appeler des opérations de chargement/téléchargement de stockage. N’oubliez pas de remplacer les paramètres `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` et `<STORAGE ACCOUNT NAME>` par vos propres valeurs. Remplacez la valeur `<ACCESS TOKEN>` par le jeton d’accès que vous avez récupéré précédemment :

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Le texte de l’URL précédente respectant la casse, veillez à respecter les majuscules et les minuscules pour vos groupes de ressources. En outre, il est important de savoir qu’il s’agit d’une demande POST et non d’une demande GET. Assurez-vous donc que vous transmettez une valeur pour capturer une limite de longueur avec -d qui peut être zéro.  

La réponse CURL vous donne la liste des clés :  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Créez un exemple de fichier blob à charger dans votre conteneur de stockage d’objets blob. Ceci peut se faire sur une machine virtuelle Linux avec la commande suivante : 

```bash
echo "This is a test file." > test.txt
```

Ensuite, authentifiez-vous à l’aide de la commande d’interface CLI `az storage` en utilisant la clé d’accès de stockage, puis chargez le fichier dans le conteneur d’objets blob. Pour cette étape, vous devez [installer la dernière version d’Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) sur votre machine virtuelle, si ce n’est déjà fait.
 

```azurecli-interactive
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Réponse : 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Vous pouvez également télécharger le fichier à l’aide d’Azure CLI et vous authentifier avec la clé d’accès de stockage. 

Demande : 

```azurecli-interactive
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Réponse : 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>étapes suivantes

- Pour une vue d’ensemble de l’identité du service administré, consultez [Vue d’ensemble de l’identité du service administré](../active-directory/msi-overview.md).
- Pour savoir comment suivre ce didacticiel en utilisant des informations d’identification de stockage SAP, consultez [Utiliser l’identité MSI (Managed Service Identity) d’une machine virtuelle Linux pour accéder au stockage Azure à l’aide d’informations d’identification SAP](msi-tutorial-linux-vm-access-storage-sas.md).
- Pour plus d’informations sur la fonctionnalité de SAP de compte de Stockage Azure, voir :
  - [Utilisation des signatures d’accès partagé (SAP)](/azure/storage/common/storage-dotnet-shared-access-signature-part-1.md)
  - [Construction d’un service SAP](/rest/api/storageservices/Constructing-a-Service-SAS.md)

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
