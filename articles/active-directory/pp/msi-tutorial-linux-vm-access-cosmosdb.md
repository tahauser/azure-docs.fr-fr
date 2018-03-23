---
title: Utiliser une identité MSI affectée à l’utilisateur sur une machine virtuelle Linux pour accéder à Azure Cosmos DB
description: Ce didacticiel vous guide tout au long de l’utilisation d’une identité MSI (Managed Service Identity) affectée à l’utilisateur sur une machine virtuelle Linux en vue d’accéder à Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: dbb5e9e8f9accd618599010ab2bbb4a8760e534f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>Utiliser une identité MSI affectée à l’utilisateur sur une machine virtuelle Linux pour accéder à Azure Cosmos DB 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ce didacticiel vous montre comment créer et utiliser une identité MSI affectée à l’utilisateur sur une machine virtuelle Linux, et comment s’en servir ensuite pour accéder à Azure Cosmos DB. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une identité MSI (Managed Service Identity) affectée à l’utilisateur
> * Attribuer l’identité MSI affectée à l’utilisateur à une machine virtuelle Linux
> * Permettre à l’identité MSI d’accéder à une instance Azure Cosmos DB
> * Obtenir un jeton d’accès par l’intermédiaire de l’identité MSI affectée à l’utilisateur, et l’utiliser pour accéder à Azure Cosmos DB

## <a name="prerequisites"></a>Prérequis


[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Pour exécuter les exemples de script CLI dans ce didacticiel, vous avez deux possibilités :

- Utiliser [Azure Cloud Shell](~/articles/cloud-shell/overview.md) dans le portail Azure ou via le bouton **Essayer** situé en haut à droite de chaque bloc de code.
- [Installer la dernière version de CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 ou ultérieure) si vous préférez utiliser une console CLI locale.

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Créer une machine virtuelle Linux dans un nouveau groupe de ressources

Pour ce didacticiel, nous créons une machine virtuelle Linux. Vous pouvez également activer l’identité du service administré sur une machine virtuelle existante.

1. Cliquez sur **+ Créer une ressource** dans le coin supérieur gauche du portail Azure.
2. Sélectionnez **Compute**, puis sélectionnez **Ubuntu Server 16.04 LTS VM**.
3. Saisissez les informations de la machine virtuelle. Dans **Type d’authentification**, sélectionnez **Clé publique SSH** ou **Mot de passe**. Les informations d’identification créées vous permettent de vous connecter à la machine virtuelle.

    ![Machine virtuelle Linux MSI](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Choisissez un **Abonnement** pour la machine virtuelle dans la liste déroulante.
5. Sous **Groupe de ressources**, sélectionnez **Créer un nouveau**, puis tapez le nom du groupe de ressources de la machine virtuelle. Lorsque vous avez terminé, cliquez sur **OK**.
6. Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre de type de disque pris en charge. Dans le panneau **Paramètres**, conservez les valeurs par défaut, puis cliquez sur **OK**.

## <a name="create-a-user-assigned-msi"></a>Créer une identité MSI affectée à l’utilisateur

1. Si vous utilisez la console CLI (au lieu d’une session Azure Cloud Shell), commencez par vous connecter à Azure. Utilisez un compte associé à l’abonnement Azure sur lequel vous souhaitez créer l’identité MSI :

    ```azurecli
    az login
    ```

2. Créez une identité MSI affectée à l’utilisateur avec la commande [az identity create](/cli/azure/identity#az_identity_create). Le paramètre `-g` spécifie le groupe de ressources où l’identité MSI est créée, tandis que le paramètre `-n` indique son nom. N’oubliez pas de remplacer les valeurs des paramètres `<RESOURCE GROUP>` et `<MSI NAME>` par vos propres valeurs :

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    La réponse contient des informations sur l’identité MSI affectée à l’utilisateur, qui sont similaires à celles de l’exemple suivant (notez les valeurs `clientId` et `id` de votre identité MSI, car nous allons les utiliser un peu plus tard) :

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
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

## <a name="create-a-cosmos-db-account"></a>Création d’un compte Cosmos DB 

Si vous n’en avez pas déjà un, créez un compte Cosmos DB. Vous pouvez également ignorer cette étape et utiliser un compte Cosmos DB existant. 

1. Cliquez sur le bouton **+/Créer un service** dans l’angle supérieur gauche du portail Azure.
2. Cliquez sur **Bases de données**, puis sur **Azure Cosmos DB** pour afficher le panneau Nouveau compte.
3. Entrez un **ID** pour le compte Cosmos DB, que vous pourrez utiliser plus tard.  
4. **API** doit être défini sur « SQL ». L’approche décrite dans ce didacticiel peut être utilisée avec les autres types d’API disponibles. Toutefois, les étapes de ce didacticiel correspondent à celles de l’API SQL.
5. Assurez-vous que les champs **Abonnement** et **Groupe de ressources** correspondent à ceux que vous avez spécifiés lorsque vous avez créé votre machine virtuelle à l’étape précédente.  Sélectionnez un **emplacement** où Cosmos DB est disponible.
6. Cliquez sur **Créer**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Créer une collection dans le compte Cosmos DB

Ensuite, ajoutez une collection de données dans le compte Cosmos DB que vous pourrez interroger dans les prochaines étapes.

1. Accédez au compte Cosmos DB que vous venez de créer.
2. Sous l’onglet **Vue d’ensemble**, cliquez sur le bouton **+ Ajouter une collection** pour afficher le panneau Ajouter une collection.
3. Attribuez à la collection un ID de base de données et un ID de collection. Sélectionnez une capacité de stockage, entrez une clé de partition et une valeur de débit, puis cliquez sur **OK**.  Pour ce didacticiel, il suffit d’utiliser « Test » comme ID de base de données et ID de collection, et de sélectionner une capacité de stockage fixe et le débit le plus bas (400 RU/s).

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>Permettre à votre identité MSI affectée à l’utilisateur d’accéder aux clés d’accès du compte Cosmos DB

Cosmos DB ne prend pas en charge l’authentification Azure AD en mode natif.  Toutefois, vous pouvez utiliser une identité MSI pour récupérer une clé d’accès Cosmos DB à partir de Resource Manager, puis utiliser cette clé pour accéder à Cosmos DB.  Dans cette étape, vous allez permettre à votre identité MSI affectée à l’utilisateur d’accéder aux clés d’accès du compte Cosmos DB.

Commencez par accorder à votre identité MSI l’accès au compte Cosmos DB dans Azure Resource Manager à l’aide d’Azure CLI. Mettez à jour les valeurs de `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` et `<COSMOS DB ACCOUNT NAME>` en fonction de votre environnement. Remplacez `<MSI PRINCIPALID>` par la propriété `principalId` retournée avec la commande `az identity create` dans [Créer une identité MSI affectée à l’utilisateur](#create-a-user-assigned-msi).  Cosmos DB prend en charge deux niveaux de granularité lors de l’utilisation des clés d’accès : accès en lecture/écriture au compte et accès en lecture seule au compte.  Pour obtenir les clés en lecture/écriture du compte, affectez le rôle `DocumentDB Account Contributor`, et pour obtenir les clés en lecture seule du compte, affectez le rôle `Cosmos DB Account Reader Role` :

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

La réponse comprend les détails de l’attribution de rôle créée :

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>Obtenir un jeton d’accès par l’identité MSI affectée à l’utilisateur et utiliser celui-ci pour appeler Azure Resource Manager

Pour la suite de ce didacticiel, nous allons utiliser la machine virtuelle que nous avons créée précédemment.

Pour effectuer cette procédure, vous avez besoin d’un client SSH. Si vous utilisez Windows, vous pouvez utiliser le client SSH dans le [Sous-système Windows pour Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Si vous avez besoin d’aide pour configurer les clés de votre client SSH, consultez [Comment utiliser les clés SSH avec Windows sur Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Dans le portail Azure, accédez à **Machines virtuelles**, accédez à votre machine virtuelle Linux, puis, en haut de la page **Vue d’ensemble**, cliquez sur **Se connecter**. Copiez la chaîne permettant de se connecter à votre machine virtuelle. 
2. Connectez-vous à votre machine virtuelle en utilisant votre client SSH.  
3. Ensuite, vous serez invité à entrer le **Mot de passe** que vous avez ajouté à la création de la **machine virtuelle Linux**. Vous devez être connecté.  
4. Utilisez CURL pour obtenir un jeton d’accès à partir de Azure Resource Manager.  

    La requête et la réponse CURL pour le jeton d’accès se trouvent ci-dessous.  Remplacez <CLIENT ID> par la valeur clientId de votre identité MSI affectée à l’utilisateur : 
    
    ```bash
    curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>" 
    ```
    
    > [!NOTE]
    > Dans la requête précédente, la valeur du paramètre « resource » doit correspondre exactement à ce qu’Azure AD attend. Lorsque vous utilisez l’ID de ressource Azure Resource Manager, vous devez inclure la barre oblique de fin à l’URI.
    > Dans la réponse suivante, l’élément access_token a été raccourci par souci de concision.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Obtenir les clés d’accès à partir d’Azure Resource Manager pour effectuer des appels Cosmos DB  

Maintenant, utilisez CURL pour appeler Resource Manager à l’aide du jeton d’accès récupéré dans la section précédente, afin de récupérer la clé d’accès du compte Cosmos DB. Une fois la clé d’accès obtenue, nous pouvons interroger Cosmos DB. N’oubliez pas de remplacer les paramètres `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` et `<COSMOS DB ACCOUNT NAME>` par vos propres valeurs. Remplacez la valeur `<ACCESS TOKEN>` par le jeton d’accès que vous avez récupéré précédemment.  Si vous souhaitez récupérer les clés en lecture/écriture, utilisez le type d’opération de clé `listKeys`.  Si vous souhaitez récupérer les clés en lecture seule, utilisez le type d’opération de clé `readonlykeys` :

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Le texte de l’URL précédente respectant la casse, veillez à respecter les majuscules et les minuscules pour vos groupes de ressources. En outre, il est important de savoir qu’il s’agit d’une demande POST et non d’une demande GET. Assurez-vous donc que vous transmettez une valeur pour capturer une limite de longueur avec -d qui peut être zéro.  

La réponse CURL vous donne la liste des clés.  Par exemple, si vous obtenez les clés en lecture seule :  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Maintenant que vous disposez de la clé d’accès pour le compte Cosmos DB, vous pouvez la passer à un SDK Cosmos DB et effectuer des appels pour accéder au compte.  Pour un exemple rapide, vous pouvez passer la clé d’accès à Azure CLI.  Vous pouvez obtenir l’<COSMOS DB CONNECTION URL> sous l’onglet **Vue d’ensemble**, dans le panneau du compte Cosmos DB, dans le portail Azure.  Remplacez <ACCESS KEY> par la valeur que vous avez obtenue ci-dessus :

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Cette commande CLI retourne des informations détaillées sur la collection :

```bash
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

- Pour une présentation de l’identité MSI, consultez [Identité du service administré (MSI) pour les ressources Azure](msi-overview.md).

Utilisez la section Commentaires suivante pour donner votre avis et nous aider à affiner et à mettre en forme notre contenu.
