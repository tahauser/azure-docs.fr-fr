---
title: "Créer une machine virtuelle Linux à l’aide d’Azure CLI dans Azure Stack | Microsoft Docs"
description: "Créez une machine virtuelle Linux avec l’interface de ligne de commande dans Azure Stack."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 21F7D599-1FEC-4827-A5C3-06495C5F53A4
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/25/2017
ms.author: mabrigg
ms.custom: mvc
ms.openlocfilehash: 160893f5b2e9c78bd62355782c279fb08f7f6b48
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="create-a-linux-virtual-machine-by-using-azure-cli-in-azure-stack"></a>Créer une machine virtuelle Linux à l’aide d’Azure CLI dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack*

Azure CLI permet de créer et de gérer des ressources Azure Stack à partir de la ligne de commande. Ce guide explique de manière détaillée comment utiliser Azure CLI pour créer une machine virtuelle Linux dans Azure Stack.  Une fois la machine virtuelle créée, un serveur web est installé et le port 80 est ouvert pour autoriser le trafic web.

## <a name="prerequisites"></a>Prérequis 

* Vérifiez que votre opérateur Azure Stack a ajouté l’image « Ubuntu Server 16.04 LTS » à la Place de Marché Azure Stack. 

* Azure Stack nécessite une version spécifique d’Azure CLI pour créer et gérer les ressources. Si Azure CLI n’est pas configurée pour Azure Stack, connectez-vous au [kit de développement](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou à un client externe basé sur Windows si vous êtes [connectés via VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) et suivez les étapes pour [installer et configurer Azure CLI](azure-stack-connect-cli.md).

* Une clé SSH publique nommée id_rsa.pub doit être créée dans le répertoire .ssh de votre profil utilisateur Windows. Pour plus d’informations sur la création de clés SSH, consultez [Création de clés SSH sur Windows](../../virtual-machines/linux/ssh-from-windows.md). 

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
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --use-unmanaged-disk \
  --location local
```

Une fois terminé, la commande affiche les paramètres de votre machine virtuelle.  Notez le paramètre *PublicIPAddress*. Vous l’utiliserez pour vous connecter à votre machine virtuelle et pour la gérer.

## <a name="open-port-80-for-web-traffic"></a>Ouvrez le port 80 pour le trafic web

Par défaut, seules les connexions SSH sont autorisées dans les machines virtuelles Linux déployées dans Azure. Si cet ordinateur virtuel doit être un serveur web, vous devez ouvrir le port 80 à partir d’Internet. Utilisez la commande [az vm open-port](/cli/azure/vm#open-port) pour ouvrir le port souhaité.

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="ssh-into-your-vm"></a>Se connecter avec SSH à votre machine virtuelle

Sur un système avec SSH installé, utilisez la commande suivante pour vous connecter à la machine virtuelle. Si vous travaillez sur Windows, [Putty](http://www.putty.org/) peut être utilisé pour créer la connexion. Veillez spécifier l’adresse IP publique correcte de votre machine virtuelle. Dans l’exemple ci-dessus, l’adresse IP était 192.168.102.36.

```bash
ssh <publicIpAddress>
```

## <a name="install-nginx"></a>Installer NGINX

Utilisez le script Bash suivant pour mettre à jour les sources de package et installer le dernier package NGINX. 

```bash 
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Afficher la page d’accueil NGINX

Une fois NGINX installé et le port 80 ouvert sur votre machine virtuelle à partir d’Internet, vous pouvez utiliser un navigateur web de votre choix pour afficher la page d’accueil NGINX par défaut. Veillez à utiliser la *publicIpAddress* indiquée ci-dessus pour vous rendre sur la page par défaut. 

![Site par défaut NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png) 

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, vous pouvez utiliser la commande [az group delete](/cli/azure/group#az_group_delete) pour supprimer le groupe de ressources, la machine virtuelle et toutes les ressources associées.

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>étapes suivantes

Dans ce guide de démarrage rapide, vous avez déployé une machine virtuelle Linux simple. Pour en savoir plus sur les machines virtuelles Azure Stack, continuez avec [Considérations relatives aux machines virtuelles dans Azure Stack](azure-stack-vm-considerations.md).

