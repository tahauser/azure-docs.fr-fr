---
title: "Réinitialisation du mot de passe de machine virtuelle Linux et de la clé SSH à partir de l’interface CLI | Microsoft Docs"
description: "Comment utiliser l’extension VMAccess à partir de l’Interface de ligne de commande (CLI) Azure pour réinitialiser un mot de passe de machine virtuelle Linux ou une clé SSH, corriger la configuration SSH et vérifier la cohérence des disques"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: d975eb70-5ff1-40d1-a634-8dd2646dcd17
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: cynthn
ms.openlocfilehash: 4577b6b9656b6a1cf83e6f9a227526701ba297b4
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2018
---
# <a name="how-to-reset-a-linux-vm-password-or-ssh-key-fix-the-ssh-configuration-and-check-disk-consistency-using-the-vmaccess-extension"></a>Réinitialiser un mot de passe de machine virtuelle Linux ou une clé SSH, corriger la configuration SSH et vérifier la cohérence des disques à l’aide de l’extension VMAccess
Si vous ne pouvez pas vous connecter à une machine virtuelle Linux sur Azure en raison d'un mot de passe oublié, d'une clé SSH (Secure Shell) incorrecte ou d'un problème lié à la configuration SSH, utilisez l'extension VMAccessForLinux avec l'interface de ligne de commande Azure pour réinitialiser le mot de passe ou la clé SSH, corriger la configuration SSH et vérifier la cohérence des disques. 

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment [effectuer ces étapes à l’aide du modèle Resource Manager](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

Grâce à l’interface de ligne de commande Azure, vous pouvez utiliser la commande **azure vm extension set** à partir de votre interface de ligne de commande (interpréteur de commandes (Bash), terminal, invite de commandes) pour accéder aux commandes associées. Exécutez **azure help vm extension set** pour utiliser l’extension détaillée.

L’interface de ligne de commande Microsoft Azure vous permet d’effectuer les tâches suivantes :

* [Réinitialisation du mot de passe](#pwresetcli)
* [Réinitialisation de la clé SSH](#sshkeyresetcli)
* [Réinitialisation du mot de passe et de la clé SSH](#resetbothcli)
* [Création d’un nouveau compte utilisateur sudo](#createnewsudocli)
* [Réinitialisation de la configuration SSH](#sshconfigresetcli)
* [Suppression d’un utilisateur](#deletecli)
* [Affichage de l’état de l’extension VMAccess](#statuscli)
* [Vérification de la cohérence des disques ajoutés](#checkdisk)
* [Réparation des disques ajoutées sur votre VM Linux](#repairdisk)

## <a name="prerequisites"></a>Conditions préalables
Vous devrez effectuer les opérations suivantes :

* Vous devrez [installer l’interface de ligne de commande Azure](../../../cli-install-nodejs.md), et [vous connecter à votre abonnement](/cli/azure/authenticate-azure-cli) pour utiliser des ressources Azure associées à votre compte.
* Définissez le mode approprié pour le modèle de déploiement Classic en tapant ce qui suit à l’invite de commandes :
    ``` 
        azure config mode asm
    ```
* Définissez un nouveau mot de passe ou des clés SSH, si vous souhaitez réinitialiser l’un des deux. Vous n’avez pas besoin de ces derniers si vous souhaitez corriger la configuration SSH.

## <a name="pwresetcli"></a>Réinitialisation du mot de passe
1. Sur votre ordinateur local, créez un fichier appelé PrivateConf.json avec ces lignes. Remplacez **myUserName** et **myP@ssW0rd** par vos propres nom d’utilisateur et mot de passe, et définissez votre propre date d’expiration.

    ```   
        {
        "username":"myUserName",
        "password":"myP@ssW0rd",
        "expiration":"2020-01-01"
        }
    ```
        
2. Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json
    ```

## <a name="sshkeyresetcli"></a>Réinitialisation de la clé SSH
1. Créez un fichier appelé PrivateConf.json avec ces éléments. Remplacez les valeurs **myUserName** et **mySSHKey** par vos propres informations.

    ```   
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey"
        }
    ```
2. Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par **myVM**.
   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>Réinitialisation du mot de passe et de la clé SSH
1. Créez un fichier appelé PrivateConf.json avec ces éléments. Remplacez les valeurs **myUserName**, **mySSHKey** et **myP@ssW0rd** par vos propres informations.

    ``` 
        {
        "username":"myUserName",
        "ssh_key":"mySSHKey",
        "password":"myP@ssW0rd"
        }
    ```

2. Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par **myVM**.

    ```   
        azure vm extension set MyVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="createnewsudocli"></a>Création d’un nouveau compte utilisateur sudo

Si vous avez oublié votre nom utilisateur, utilisez VMAccess pour en créer un nouveau, avec autorité sudo. Le cas échéant, le nom d’utilisateur et le mot de passe existants ne sont pas modifiés.

Pour créer un nouvel utilisateur sudo avec accès par mot de passe, utilisez le script de [Réinitialisation du mot de passe](#pwresetcli) , puis spécifiez le nouveau nom d’utilisateur.

Pour créer un nouvel utilisateur sudo avec accès par clé SSH, utilisez le script de [Réinitialisation de la clé SSH](#sshkeyresetcli) , puis spécifiez le nouveau nom d’utilisateur.

Vous pouvez également utiliser [Réinitialisation du mot de passe et de la clé SSH](#resetbothcli) pour créer un nouvel utilisateur avec accès par mot de passe et clé SSH.

## <a name="sshconfigresetcli"></a>Réinitialisation de la configuration SSH
Si la configuration SSH se trouve dans un état non souhaité, vous pouvez également perdre l’accès à la machine virtuelle. Pour redéfinir l’état par défaut de la configuration, utilisez l’extension VMAccess. Pour ce faire, réinitialisez la clé reset_ssh sur la valeur True. L’extension redémarre le serveur SSH, ouvre le port SSH sur votre machine virtuelle et rétablit la configuration SSH par défaut. Le compte d’utilisateur (nom, mot de passe ou clés SSH) ne sera pas modifié.

> [!NOTE]
> Le fichier de configuration SSH réinitialisé se trouve dans /etc/ssh/sshd_config.
> 
> 

1. Créez un fichier appelé PrivateConf.json avec ces éléments.

    ```   
        {
        "reset_ssh":"True"
        }
    ```

2. Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="deletecli"></a>Suppression d’un utilisateur
Si vous souhaitez supprimer un compte utilisateur sans vous connecter directement à la machine virtuelle, vous pouvez utiliser ce script.

1. Créez un fichier appelé PrivateConf.json avec ces éléments, en remplaçant le nom d’utilisateur à supprimer par **removeUserName**. 

    ```   
        {
        "remove_user":"removeUserName"
        }
    ```

2. Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par **myVM**. 

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json
    ```

## <a name="statuscli"></a>Affichage de l’état de l’extension VMAccess
Pour afficher l’état de l’extension VMAccess, exécutez cette commande.

```
        azure vm extension get
```

## <a name='checkdisk'></a>Vérification de la cohérence des disques ajoutés
Pour exécuter la commande fsck sur tous les disques de votre machine virtuelle Linux, vous devez effectuer les opérations suivantes :

1. Créez un fichier nommé PublicConf.json avec ces éléments. Vérifiez que le disque prend une valeur booléenne pour vérifier les disques attachés à votre machine virtuelle ou non. 

    ```   
        {   
        "check_disk": "true"
        }
    ```

2. Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 
    ```

## <a name='repairdisk'></a>Réparation de disques
Pour réparer les disques qui ne se montent pas ou dont la configuration de montage présente des erreurs, utilisez l’extension VMAccess pour réinitialiser la configuration de montage sur votre machine virtuelle Linux. Remplacez le nom de votre disque par **myDisk**.

1. Créez un fichier nommé PublicConf.json avec ces éléments. 

    ```   
        {
        "repair_disk":"true",
        "disk_name":"myDisk"
        }
    ```

2. Exécutez cette commande, en remplaçant le nom de votre machine virtuelle par **myVM**.

    ```   
        azure vm extension set myVM VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json
    ```

## <a name="next-steps"></a>Étapes suivantes
* Si vous souhaitez utiliser des applets de commande Azure PowerShell ou des modèles Azure Resource Manager pour réinitialiser le mot de passe ou la clé SSH, corrigez la configuration SSH et vérifiez la cohérence des disques, consultez la [documentation de l’extension VMAccess sur GitHub](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess). 
* Vous pouvez également utiliser le [portail Azure](https://portal.azure.com) pour réinitialiser le mot de passe ou la clé SSH d’une machine virtuelle Linux déployée dans le modèle de déploiement Classic. Vous ne pouvez pas utiliser actuellement le portail dans ce but pour une machine virtuelle Linux déployée dans le modèle de déploiement Resource Manager.
* Consultez la page [À propos des extensions et des fonctionnalités des machines virtuelles](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pour plus d’informations sur l’utilisation des extensions de machine virtuelle pour les machines virtuelles Azure.

