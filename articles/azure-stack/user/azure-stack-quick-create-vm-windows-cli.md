---
title: "Créer une machine virtuelle Windows sur Azure Stack avec Azure CLI | Microsoft Docs"
description: "Découvrez comment créer une machine virtuelle Windows sur Azure Stack à l’aide d’Azure CLI"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: E26B246E-811D-44C9-9BA6-2B3CE5B62E83
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: ea972db9ce3488d9a46a7d059714c8bbe820d47d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-windows-virtual-machine-on-azure-stack-using-azure-cli"></a>Créer une machine virtuelle Windows sur Azure Stack avec Azure CLI

Azure CLI permet de créer et de gérer des ressources Azure Stack à partir de la ligne de commande. Ce guide explique de manière détaillée comment utiliser Azure CLI pour créer une machine virtuelle Windows Server 2016 dans Azure Stack. Une fois la machine virtuelle créée, vous vous connecterez avec le Bureau à distance, installerez IIS, puis afficherez le site web par défaut. 

## <a name="prerequisites"></a>Prérequis 

* Vérifiez que votre opérateur Azure Stack a ajouté l’image « Windows Server 2016 » à la Place de Marché Azure Stack.  

* Azure Stack nécessite une version spécifique d’Azure CLI pour créer et gérer les ressources. Si Azure CLI n’est pas configuré pour Azure Stack, suivez les étapes permettant [d’installer et de configurer Azure CLI](azure-stack-connect-cli.md).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un groupe de ressources est un conteneur logique dans lequel les ressources Azure Stack sont déployées et gérées. À partir de votre kit de développement ou du système intégré Azure Stack, exécutez la commande [az group create](/cli/azure/group#az_group_create) pour créer un groupe de ressources. Nous avons affecté des valeurs à toutes les variables de ce document. Vous pouvez les utiliser en l’état ou affecter une valeur différente. L’exemple suivant crée un groupe de ressources nommé myResourceGroup à l’emplacement local.

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>Création d'une machine virtuelle

Créez une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). L’exemple suivant crée une machine virtuelle nommée myVM. Cet exemple utilise Demouser comme nom d’utilisateur administratif et Demouser@123 comme mot de passe. Mettez à jour ces valeurs avec quelque chose d’approprié pour votre environnement. Ces valeurs sont nécessaires lors de la connexion à la machine virtuelle.

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Quand la machine virtuelle est créée, notez le paramètre *PublicIPAddress* généré. Vous l’utiliserez pour accéder à la machine virtuelle.
 
## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web

Par défaut, seules les connexions RDP à une machine virtuelle Windows déployée dans Azure Stack sont autorisées. Si cet ordinateur virtuel doit être un serveur web, vous devez ouvrir le port 80 à partir d’Internet. Utilisez la commande [az vm open-port](/cli/azure/vm#open-port) pour ouvrir le port souhaité.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>Connectez-vous à la machine virtuelle.

Utilisez la commande suivante pour créer une session Bureau à distance avec la machine virtuelle. Remplacez l’adresse IP par l’adresse IP publique de votre machine virtuelle. À l'invite, saisissez les informations d’identification que vous avez utilisées lors de la création de la machine virtuelle.

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>Installation de IIS à l’aide de PowerShell

Maintenant que vous êtes connecté à la machine virtuelle Azure, vous pouvez utiliser une seule ligne de PowerShell pour installer IIS et activer la règle de pare-feu local pour autoriser le trafic web. Ouvrez une invite PowerShell et exécutez la commande suivante :

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Afficher la page d’accueil IIS

Une fois IIS installé et le port 80, ouvert sur votre machine virtuelle à partir d’Internet, vous pouvez utiliser un navigateur web pour afficher la page d’accueil IIS par défaut. Veillez à utiliser l’adresse IP publique indiquée ci-dessus pour vous rendre sur la page par défaut. 

![Site IIS par défaut](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png) 

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Windows simple. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).
