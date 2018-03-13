---
title: "Créer un réseau virtuel - Azure CLI | Microsoft Docs"
description: "Découvrez rapidement comment créer un réseau virtuel à l’aide d’Azure CLI. Un réseau virtuel permet à de nombreux types de ressources Azure de communiquer en privé."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 792b92731f89f3d0bab4f23221223e469ddf9550
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Créer un réseau virtuel à l’aide d’Azure CLI

Dans cet article, vous allez apprendre à créer un réseau virtuel. Une fois le réseau virtuel créé, déployez deux machines virtuelles dans le réseau virtuel pour tester la communication réseau en privé entre elles.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si vous choisissez d’installer et d’utiliser l’interface de ligne de commande localement, vous devez exécuter Azure CLI version 2.0.4 ou une version ultérieure pour poursuivre la procédure décrite dans cet article. Pour trouver la version installée, exécutez `az --version`. Si vous devez installer ou mettre à niveau, consultez [Installation d’Azure CLI 2.0](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*. Toutes les ressources Azure sont créées dans une localisation (ou région) Azure.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Utilisez la commande [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) pour créer un réseau virtuel. L’exemple suivant crée un réseau virtuel par défaut nommé *myVirtualNetwork* avec un sous-réseau nommé *default*. Comme aucune localisation n’est spécifiée, Azure crée le réseau virtuel dans la même localisation que le groupe de ressources.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

Une fois le réseau virtuel créé, voici une partie des informations retournées :

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

Un ou plusieurs préfixes d’adresse sont affectés à tous les réseaux virtuels. Étant donné qu’aucun préfixe d’adresse n’a été spécifié au moment de la création du réseau virtuel, Azure définit par défaut l’espace d’adressage 10.0.0.0/16. L’espace d’adressage est spécifié en notation CIDR. L’espace d’adressage 10.0.0.0/16 englobe 10.0.0.0-10.0.255.254.

Le préfixe d’adresse (**addressPrefix**) *10.0.0.0/24* fait partie des informations retournées pour le sous-réseau *default* spécifié dans la commande. Un réseau virtuel contient zéro ou plusieurs sous-réseaux. La commande a créé un sous-réseau unique nommé *default*, mais aucun préfixe d’adresse n’a été spécifié pour le sous-réseau. Quand un préfixe d’adresse n’est pas spécifié pour un réseau virtuel ou sous-réseau, Azure définit par défaut 10.0.0.0/24 comme préfixe d’adresse pour le premier sous-réseau. Le sous-réseau englobe donc 10.0.0.0-10.0.0.254, mais seule la plage 10.0.0.4-10.0.0.254 est disponible car Azure réserve les quatre premières adresses (0-3) et la dernière adresse de chaque sous-réseau.

## <a name="test-network-communication"></a>Test de communication réseau

Un réseau virtuel permet à plusieurs types de ressources Azure de communiquer en privé. Une machine virtuelle est l’un des types de ressource que vous pouvez déployer dans un réseau virtuel. Créez deux machines virtuelles dans le réseau virtuel pour pouvoir valider ultérieurement la communication en privé entre elles.

### <a name="create-virtual-machines"></a>Créer des machines virtuelles

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée *myVm1*. Si des clés SSH n’existent pas déjà dans un emplacement de clé par défaut, la commande les crée. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`. L’option `--no-wait` crée la machine virtuelle en arrière-plan. Vous pouvez donc passer à l’étape suivante.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

Azure crée automatiquement la machine virtuelle dans le sous-réseau *default* du réseau virtuel *myVirtualNetwork*. En effet, le réseau virtuel existe dans le groupe de ressources et aucun sous-réseau ou réseau virtuel n’est spécifié dans la commande. Azure DHCP affecte automatiquement 10.0.0.4 à la machine virtuelle durant la création, car il s’agit de la première adresse disponible dans le sous-réseau *default*. Une machine virtuelle doit être créée dans la même localisation que le réseau virtuel. Bien que cela soit le cas dans cet article, la machine virtuelle ne doit pas nécessairement figurer dans le même groupe de ressources que le réseau virtuel.

Créez une deuxième machine virtuelle. Par défaut, Azure crée également cette machine virtuelle dans le sous-réseau *default*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

La création de la machine virtuelle prend quelques minutes. Une fois la machine virtuelle créée, Azure CLI retourne une sortie similaire à celle-ci : 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Dans l’exemple, vous pouvez constater que l’adresse IP privée (**privateIpAddress**) est *10.0.0.5*. Azure DHCP affecte automatiquement *10.0.0.5* à la machine virtuelle durant la création, car il s’agit de la prochaine adresse disponible dans le sous-réseau *default*. Veuillez noter **publicIpAddress**. Cette adresse est utilisée pour accéder à la machine virtuelle à partir d’Internet dans une étape ultérieure. L’adresse IP publique n’est affectée ni à partir du réseau virtuel ni à partir des préfixes d’adresse de sous-réseau. Les adresses IP publiques sont affectées à partir d’un [pool d’adresses affecté à chaque région Azure](https://www.microsoft.com/download/details.aspx?id=41653). Si Azure a connaissance de l’adresse IP publique qui est affectée à une machine virtuelle, le système d’exploitation en cours d’exécution dans une machine virtuelle l’ignore.

### <a name="connect-to-a-virtual-machine"></a>Connexion à une machine virtuelle

Utilisez la commande suivante pour créer une session SSH avec la machine virtuelle *myVm2*. Remplacez `<publicIpAddress>` par l’adresse IP publique de votre machine virtuelle. Dans l’exemple ci-dessus, l’adresse IP est *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

### <a name="validate-communication"></a>Valider la communication

Utilisez la commande suivante pour vérifier la communication avec *myVm1* à partir de *myVm2* :

```bash
ping myVm1 -c 4
```

Vous recevez quatre réponses de *10.0.0.4*. Vous pouvez communiquer avec *myVm1* à partir de *myVm2*, car des adresses IP privées sont affectées aux deux machines virtuelles à partir du sous-réseau *default*. Vous pouvez effectuer un test ping par nom d’hôte, car Azure fournit automatiquement la résolution de noms DNS pour tous les hôtes au sein d’un réseau virtuel.

Pour confirmer les communications sortantes à destination d’Internet, utilisez la commande suivante :

```bash
ping bing.com -c 4
```

Vous recevez quatre réponses de bing.com. Par défaut, toute machine virtuelle dans un réseau virtuel prend en charge les communications sortantes à destination d’Internet.

Quittez la session SSH vers votre machine virtuelle.

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin d’un groupe de ressources, utilisez la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe et toutes les ressources qu’il contient :

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez déployé un réseau virtuel par défaut avec un sous-réseau. Pour découvrir comment créer un réseau virtuel personnalisé avec plusieurs sous-réseaux, passez au didacticiel couvrant la création d’un réseau virtuel personnalisé.

> [!div class="nextstepaction"]
> [Créer un réseau virtuel personnalisé](virtual-networks-create-vnet-arm-pportal.md#azure-cli)
