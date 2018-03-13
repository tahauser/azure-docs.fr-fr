---
ms.assetid: 
title: "Azure Key Vault - Utilisation de la suppression réversible avec l’interface CLI"
description: "Exemples d’utilisation de la suppression réversible avec extraits de code CLI"
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: alleonar
ms.openlocfilehash: a9b80cae69c4e5852341385b98fcccc86d7959e9
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>Guide pratique pour utiliser la suppression réversible Key Vault avec l’interface CLI

La fonctionnalité de suppression réversible d’Azure Key Vault permet de récupérer des coffres et des objets de coffres qui ont été supprimés. La suppression réversible traite plus particulièrement les scénarios suivants :

- Prise en charge de la suppression récupérable d’un coffre de clés
- Prise en charge de la suppression récupérable d’objets de coffre de clés (clés, secrets et certificats)

## <a name="prerequisites"></a>configuration requise

- Azure CLI 2.0 - Si vous ne l’avez pas encore installé pour votre environnement, consultez [Gestion de Key Vault à l’aide de l’interface de ligne de commande (CLI) 2.0](key-vault-manage-with-cli2.md).

Pour obtenir des informations de référence propres à Key Vault pour l’interface CLI, consultez la [référence Key Vault Azure CLI 2.0](https://docs.microsoft.com/cli/azure/keyvault).

## <a name="required-permissions"></a>Autorisations requises

Les opérations Key Vault sont gérées séparément par l’intermédiaire d’autorisations de contrôle d’accès en fonction du rôle (RBAC) comme suit :

| Opération | Description | Autorisation utilisateur |
|:--|:--|:--|
|Liste|Énumère les coffres de clé supprimés.|Microsoft.KeyVault/deletedVaults/read|
|Recover|Restaure un coffre de clés supprimé.|Microsoft.KeyVault/vaults/write|
|Purge|Supprime définitivement un coffre de clés supprimé et tout son contenu.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

Pour plus d’informations sur les autorisations et le contrôle d’accès, consultez [Sécuriser votre coffre de clés](key-vault-secure-your-key-vault.md).

## <a name="enabling-soft-delete"></a>Activation de la suppression réversible

Pour pouvoir récupérer un coffre de clés supprimé ou des objets stockés dans un coffre de clés, vous devez d’abord activer la suppression réversible pour ce coffre de clés.

### <a name="existing-key-vault"></a>Coffre de clés existant

Pour un coffre de clés existant nommé ContosoVault, vous pouvez activer la suppression réversible comme suit. 

>[!NOTE]
>Actuellement, vous devez utiliser la manipulation des ressources Azure Resource Manager pour écrire directement la propriété *enableSoftDelete* dans la ressource Key Vault.

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>Nouveau coffre de clés

L’activation de la suppression réversible pour un nouveau coffre de clés s’effectue au moment de la création en ajoutant l’indicateur « soft-delete enable » à votre commande de création.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>Vérifier l’activation de la suppression réversible

Pour vérifier que la suppression réversible est activée pour un coffre de clés, exécutez la commande *show* et recherchez l’attribut « Soft Delete Enabled ? » et sa valeur (true ou false).

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-key-vault-protected-by-soft-delete"></a>Suppression d’un coffre de clés protégé par la suppression réversible

La commande de suppression d’un coffre de clés reste la même, mais son comportement varie selon que vous avez activé ou non la suppression réversible.

```azurecli
az keyvault delete --name ContosoVault
```

> [!IMPORTANT]
>Si vous exécutez la commande précédente pour un coffre de clés pour lequel la suppression réversible n’est pas activée, vous supprimez définitivement ce coffre de clés et tout son contenu, sans aucune option de récupération.

### <a name="how-soft-delete-protects-your-key-vaults"></a>Comment la suppression réversible protège-t-elle votre coffre de clés ?

Quand la suppression réversible est activée :

- Quand un coffre de clés est supprimé, il est enlevé de son groupe de ressources et placé dans un espace de noms réservé qui n’est associé qu’à l’emplacement où il a été créé. 
- Les objets d’un coffre de clés supprimé (tels que les clés, les secrets et les certificats) sont inaccessibles et le demeurent tant que leur coffre de clés conteneur est à l’état supprimé. 
- Le nom DNS d’un coffre de clés à l’état supprimé est toujours réservé. Vous ne pourrez donc pas créer de nouveau coffre de clés avec le même nom.  

Vous pouvez afficher les coffres de clé associés à votre abonnement qui sont à l’état supprimé en exécutant la commande suivante :

```azurecli
az keyvault list-deleted
```

L’*ID de ressource* dans la sortie fait référence à l’ID de ressource d’origine de ce coffre. Ce coffre de clés étant maintenant à l’état supprimé, il n’existe aucune ressource avec cet ID de ressource. Vous pouvez utiliser le champ *Id* pour identifier la ressource lors de la récupération ou du vidage. Le champ *Date de vidage planifiée* indique quand le coffre sera définitivement supprimé (vidé) si aucune action n’est effectuée pour ce coffre supprimé. La période de rétention par défaut, utilisée pour calculer la *Date de vidage planifiée*, est de 90 jours.

## <a name="recovering-a-key-vault"></a>Récupération d’un coffre de clés

Pour récupérer un coffre de clés, vous devez spécifier le nom du coffre de clés, le groupe de ressources et l’emplacement. Notez l’emplacement et le groupe de ressources du coffre de clés supprimé, car vous en aurez besoin pour le processus de récupération de coffre de clés.

```azurecli
az keyvault recover --location westus --name ContosoVault
```

Quand un coffre de clés est récupéré, le résultat est une ressource avec l’ID de ressource d’origine du coffre de clés Si le groupe de ressources dans lequel existait le coffre de clés a été supprimé, vous devez créer un groupe de ressources du même nom pour que le coffre de clés puisse être récupéré.

## <a name="key-vault-objects-and-soft-delete"></a>Objets de coffre de clés et suppression réversible

Pour une clé nommée « ContosoFirstKey » dans un coffre de clés nommé « ContosoVault » avec la suppression réversible activée, voici comment vous supprimeriez cette clé.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

Quand la suppression réversible est activée pour votre coffre de clés, une clé supprimée apparaît toujours comme ayant été supprimée, sauf quand vous énumérez ou récupérez explicitement les clés supprimées. La plupart des opérations effectuées sur une clé à l’état supprimé échoueront, sauf l’énumération d’une clé supprimée, sa récupération ou son vidage. 

Par exemple, pour demander à énumérer les clés supprimées dans un coffre de clés, exécutez la commande suivante :

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>État de transition 

Quand vous supprimez une clé dans un coffre de clés pour lequel la suppression réversible est activée, la transition peut prendre quelques secondes. Pendant cet état de transition, il peut sembler que la clé n’est pas à l’état actif ou à l’état supprimé. Cette commande répertorie toutes les clés supprimées dans votre coffre de clés nommé « ContosoVault ».

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="using-soft-delete-with-key-vault-objects"></a>Utilisation de la suppression réversible avec des objets de coffre de clés

Tout comme les coffres de clés, une clé, un secret ou un certificat supprimé(e) reste à l’état supprimé pendant 90 jours, sauf si vous le récupérez ou si vous le videz. 

#### <a name="keys"></a>Clés

Pour récupérer une clé supprimée

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

Pour supprimer définitivement une clé

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

>[!NOTE]
>Le vidage d’une clé entraîne sa suppression définitive, ce qui signifie que vous ne pourrez pas la récupérer.

Les actions **recover** et **purge** ont leurs propres autorisations associées dans une stratégie d’accès au coffre de clés. Pour qu’un utilisateur ou un principal de service puisse exécuter une action **recover** ou **purge**, il doit disposer de l’autorisation correspondante pour cet objet (clé ou secret) dans la stratégie d’accès au coffre de clés. Par défaut, l’autorisation **purge** n’est pas ajoutée à la stratégie d’accès d’un coffre de clés quand le raccourci « all » est utilisé pour accorder toutes les autorisations à un utilisateur. Vous devez accorder explicitement l’autorisation**purge**. Par exemple, la commande suivante accorde à user@contoso.com l’autorisation d’effectuer plusieurs opérations sur les clés dans *ContosoVault*, notamment **purge**.

#### <a name="set-a-key-vault-access-policy"></a>Définir une stratégie d’accès au coffre de clés

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> Si vous avez un coffre de clés pour lequel la suppression réversible vient d’être activée, vous ne disposerez peut-être pas des autorisations **recover** et **purge**.

#### <a name="secrets"></a>Secrets

Comme les clés, les secrets dans un coffre de clés sont gérés avec leurs propres commandes. Vous trouverez ci-dessous les commandes de suppression, d’énumération, de récupération et de vidage des secrets.

- Supprimer un secret nommé SQLPassword : 
```azurecli
az keyvault secret delete --vault-name ContosoVault -name SQLPassword
```

- Énumérer tous les secrets supprimés dans un coffre de clés : 
```azurecli
az keyvault secret list-deleted --vault-name ContosoVault
```

- Récupérer un secret à l’état supprimé : 
```azurecli
az keyvault secret recover --name SQLPassword --vault-name ContosoVault
```

- Vider un secret à l’état supprimé : 
```azurecli
az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
```

>[!NOTE]
>Le vidage d’un secret entraîne sa suppression définitive, ce qui signifie que vous ne pourrez pas le récupérer.

## <a name="purging-and-key-vaults"></a>Vidage et coffres de clé

### <a name="key-vault-objects"></a>Objets de coffre de clés

Le vidage d’une clé, d’un secret ou d’un certificat entraîne sa suppression définitive, ce qui signifie que vous ne pourrez pas le récupérer. Le coffre de clés qui contenait l’objet supprimé sera toutefois conservé, de même que tous les autres objets dans le coffre de clés. 

### <a name="key-vaults-as-containers"></a>Coffres de clé en tant que conteneurs
Quand un coffre de clés est vidé, tout son contenu, notamment les clés, les secrets et les certificats, sont supprimés définitivement. Pour vider un coffre de clés, utilisez la commande `az keyvault purge`. Vous pouvez trouver l’emplacement des coffres de clés supprimés pour votre abonnement à l’aide de la commande `az keyvault list-deleted`.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

>[!NOTE]
>Le vidage d’un coffre de clés entraîne sa suppression définitive, ce qui signifie que vous ne pourrez pas le récupérer.

### <a name="purge-permissions-required"></a>Autorisations de vidage requises
- Pour vider un coffre de clés supprimé, et ainsi faire en sorte que le coffre et tout son contenu soient supprimés définitivement, l’utilisateur a besoin d’une autorisation RBAC pour effectuer une opération *Microsoft.KeyVault/locations/deletedVaults/purge/action*. 
- Pour répertorier le coffre de clés supprimé, l’utilisateur a besoin de l’autorisation RBAC pour effectuer une opération *Microsoft.KeyVault/deletedVaults/read*. 
- Par défaut, seul un administrateur d’abonnement dispose de ces autorisations. 

### <a name="scheduled-purge"></a>Vidage planifié

La liste des objets de votre coffre de clés supprimés indique quand est planifiée leur suppression définitive par Key Vault. Le champ *Date de vidage planifiée* indique quand un objet de coffre de clés sera supprimé définitivement si aucune action n’est effectuée. Par défaut, la période de rétention d’un objet de coffre de clés supprimé est de 90 jours.

>[!NOTE]
>Un objet de coffre dont le vidage est déclenché par son champ *Date de vidage planifiée* est supprimé définitivement. Il n’est pas récupérable.

## <a name="other-resources"></a>Autres ressources

- Pour obtenir une présentation de la fonctionnalité de suppression réversible, consultez [Présentation de la suppression réversible d’Azure Key Vault](key-vault-ovw-soft-delete.md).
- Pour obtenir une vue d’ensemble de l’utilisation d’Azure Key Vault, consultez [Bien démarrer avec Azure Key Vault](key-vault-get-started.md).

