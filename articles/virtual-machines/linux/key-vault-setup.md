---
title: Configurer Azure Key Vault pour les machines virtuelles Linux | Microsoft Docs
description: "Guide pratique de configuration de Key Vault pour l’utiliser avec une machine virtuelle Azure Resource Manager par le biais de l’interface CLI 2.0."
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: afb8fce7ce7ef432518c58cb6f58951337aebcff
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>Guide pratique de configuration de Key Vault pour des machines virtuelles avec Azure CLI 2.0

Dans la pile Azure Resource Manager, les secrets/certificats sont modélisés en tant que ressources fournies par Key Vault. Pour en savoir plus sur Azure Key Vault, consultez [Qu’est-ce qu’Azure Key Vault ?](../../key-vault/key-vault-whatis.md) Pour que Key Vault puisse être utilisé avec des machines virtuelles Azure Resource Manager, la propriété *EnabledForDeployment* doit être définie sur true dans Key Vault. Cet article explique comment configurer Key Vault pour une utilisation avec des machines virtuelles Azure à l’aide d’Azure CLI 2.0. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour suivre ces étapes, vous devez disposer de la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec la commande [az login](/cli/azure/#az_login).

## <a name="create-a-key-vault"></a>Créer un coffre de clés
Créez un coffre de clés et affectez la stratégie de déploiement avec [az keyvault create](/cli/azure/keyvault#az_keyvault_create). L’exemple suivant crée un coffre de clés nommé `myKeyVault` dans le groupe de ressources `myResourceGroup` :

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Mettre à jour un coffre de clés pour l’utiliser avec des machines virtuelles
Définissez la stratégie de déploiement sur un coffre de clés avec [az keyvault update](/cli/azure/keyvault#az_keyvault_update). Les mises à jour suivantes créent un coffre de clés nommé `myKeyVault` dans le groupe de ressources `myResourceGroup` :

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Utilisation de modèles pour configurer Key Vault
Quand vous utilisez un modèle, vous devez définir la propriété `enabledForDeployment` sur `true` pour la ressource Key Vault de la façon suivante :

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Étapes suivantes
Pour connaître les autres options que vous pouvez configurer lorsque vous créez un coffre de clés à l’aide de modèles, consultez la rubrique [Créer un coffre de clés](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
