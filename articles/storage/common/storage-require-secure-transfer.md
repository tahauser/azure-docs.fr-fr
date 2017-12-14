---
title: "Exiger un transfert sécurisé dans le stockage Azure | Microsoft Docs"
description: "Découvrez la fonctionnalité Transfert sécurisé requis pour Stockage Azure et apprenez à l’activer."
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: 
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.openlocfilehash: 797ac45a41cdf655e7465a01875a0394081c08a7
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="require-secure-transfer-in-azure-storage"></a>Exiger un transfert sécurisé dans Stockage Azure

L’option Transfert sécurisé requis améliore la sécurité de votre compte de stockage en autorisant uniquement les requêtes relatives au compte provenant de connexions sécurisées. Par exemple, lorsque vous appelez des API REST pour accéder à votre compte de stockage, vous devez vous connecter à l’aide du protocole HTTPS. La fonctionnalité « Transfert sécurisé requis » rejette les requêtes qui utilisent le protocole HTTP.

Lorsque vous utilisez le service Azure Files, toute connexion sans chiffrement échoue si l’option Transfert sécurisé requis est activée. Cela inclut des scénarios utilisant SMB 2.1, SMB 3.0 sans chiffrement et certaines versions du client SMB Linux. 

Par défaut, l’option Transfert sécurisé requis est désactivée.

> [!NOTE]
> Étant donné que Stockage Azure ne prend pas en charge le protocole HTTPS pour les noms de domaine personnalisés, cette option n’est pas appliquée lorsque vous utilisez un nom de domaine personnalisé. En outre, les comptes de stockage classiques ne sont pas pris en charge.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Activer l’option Transfert sécurisé requis dans le portail Azure

Vous pouvez activer le paramètre Transfert sécurisé requis lorsque vous créez un compte de stockage dans le [portail Azure](https://portal.azure.com). Vous pouvez également l’activer pour les comptes de stockage existants.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Exiger un transfert sécurisé pour un nouveau compte de stockage

1. Ouvrez le volet **Créer un compte de stockage** dans le portail Azure.
1. Sous **Transfert sécurisé requis**, sélectionnez **Activé**.

  ![Panneau Créer un compte de stockage](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Exiger un transfert sécurisé pour un compte de stockage existant

1. Sélectionnez un compte de stockage existant dans le portail Azure.
1. Dans le volet de menu du compte de stockage, sous **PARAMÈTRES**, sélectionnez **Configuration**.
1. Sous **Transfert sécurisé requis**, sélectionnez **Activé**.

  ![Volet de menu du compte de stockage](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Activer le paramètre « Transfert sécurisé requis » par programmation

Pour exiger un transfert sécurisé par programme, utilisez le paramètre _supportsHttpsTrafficOnly_ dans les propriétés de compte de stockage avec l’API REST, des outils ou des bibliothèques :

* [API REST](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (version : 01/12/2016)
* [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (version : 4.1.0)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (version : 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (version : 1.1.0)
* [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (version : 6.3.0)
* [SDK Python](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (version : 1.1.0)
* [SDK Ruby](https://rubygems.org/gems/azure_mgmt_storage) (version : 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Activer le paramètre « Transfert sécurisé requis » avec PowerShell

Cet exemple requiert le module Azure PowerShell version 4.1 ou ultérieure. Exécutez ` Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

Exécutez `Login-AzureRmAccount` pour créer une connexion avec Azure.

 Utilisez la ligne de commande ci-dessous pour vérifier le paramètre :

```powershell
> Get-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Utilisez la ligne de commande ci-dessous pour activer le paramètre :

```powershell
> Set-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Activer le paramètre « Transfert sécurisé requis » avec l’interface de ligne de commande

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Utilisez la ligne de commande ci-dessous pour vérifier le paramètre :

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Utilisez la ligne de commande ci-dessous pour activer le paramètre :

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Étapes suivantes
Stockage Azure propose un ensemble complet de fonctionnalités de sécurité qui, une fois réunies, permettent aux développeurs de créer des applications sécurisées. Pour plus d’informations, consultez notre [guide de sécurité Stockage Azure](storage-security-guide.md).
