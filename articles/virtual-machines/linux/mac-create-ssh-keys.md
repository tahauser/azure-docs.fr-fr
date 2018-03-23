---
title: Créer et utiliser une paire de clés SSH pour les machines virtuelles Linux dans Azure | Microsoft Docs
description: Comment créer et utiliser une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure afin d’améliorer la sécurité du processus d’authentification.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: ecd3a01ee5591cb09140edb1b1290ff2d4510200
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="how-to-create-and-use-an-ssh-public-and-private-key-pair-for-linux-vms-in-azure"></a>Comment créer et utiliser une paire de clés publique et privée SSHpour les machines virtuelles Linux dans Azure
Avec une paire de clés SSH (secure shell), vous pouvez créer des machines virtuelles sur Azure qui utilisent par défaut des clés SSH pour l’authentification, éliminant ainsi la nécessité de recourir aux mots de passe pour la connexion. Cet article vous indique comment générer et utiliser rapidement une paire de clés publique et privée SSH RSA de version de protocole 2 pour des machines virtuelles Linux. Vous pouvez effectuer ces étapes avec Azure Cloud Shell, un hôte Linux ou macOS ou le sous-système Windows pour Linux. Pour accéder à une procédure plus détaillée et à des exemples supplémentaires, consultez la [procédure détaillée de création de paires de clés SSH et de certificats](create-ssh-keys-detailed.md).

## <a name="create-an-ssh-key-pair"></a>Création d’une paire de clés SSH
Utilisez la commande `ssh-keygen` pour créer des fichiers de clés publique et privée SSH qui, par défaut, sont créés dans le répertoire `~/.ssh`. Vous pouvez spécifier un autre emplacement et une phrase secrète supplémentaire (un mot de passe permettant d’accéder au fichier de clé privée) lorsque vous y êtes invité. Si une paire de clés SSH existe dans l’emplacement actuel, elles sont remplacées.

```bash
ssh-keygen -t rsa -b 2048
```

## <a name="use-the-ssh-key-pair"></a>Utilisation de la paire de clés SSH
La clé publique que vous placez sur votre machine virtuelle Linux dans Azure est stockée par défaut dans `~/.ssh/id_rsa.pub`, sauf si vous avez modifié l’emplacement au moment de sa création. Si vous utilisez [Azure CLI 2.0](/cli/azure) pour créer votre machine virtuelle, spécifiez l’emplacement de cette clé publique lorsque vous utilisez la commande [az vm create](/cli/azure/vm#az_vm_create) avec l’option `--ssh-key-path`. Si vous copiez et collez le contenu du fichier de clé publique pour l’utiliser dans le portail Azure ou dans un modèle Resource Manager, veillez à ne pas copier pas les espaces blancs supplémentaires. Par exemple, si vous utilisez OS X, vous pouvez diriger le fichier de clé publique (par défaut, **~/.ssh/id_rsa.pub**) sur **pbcopy** pour en copier le contenu (d’autres programmes Linux, par exemple `xclip`, peuvent être utilisés à cette fin).

Si vous n’êtes pas familiarisé avec les clés publiques SSH, vous pouvez voir votre clé publique en exécutant `cat` comme suit, en remplaçant `~/.ssh/id_rsa.pub` par l’emplacement de votre propre fichier de clé publique :

```bash
cat ~/.ssh/id_rsa.pub
```

Avec la clé publique sur votre machine virtuelle Azure, appliquez le protocole SSH sur votre machine virtuelle à l’aide de l’adresse IP ou du nom DNS de votre machine virtuelle (n’oubliez pas de remplacer `azureuser` et `myvm.westus.cloudapp.azure.com` ci-dessous par le nom d’utilisateur administrateur et par le nom de domaine complet, ou par l’adresse IP) :

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Si vous avez fourni un mot de passe lorsque vous avez créé votre paire de clés, saisissez-le lorsque vous y êtes invité pendant le processus de connexion. (Le serveur est ajouté à votre dossier `~/.ssh/known_hosts` et vous n’avez pas à vous connecter à nouveau tant que la clé publique sur votre machine virtuelle Azure n’est pas modifiée ou que le nom de serveur n’est pas supprimé du dossier `~/.ssh/known_hosts`.)

## <a name="next-steps"></a>Étapes suivantes

Les machines virtuelles créées à l’aide de clés SSH sont par défaut configurées avec les mots de passe désactivés, ce qui rend les tentatives de déchiffrement par force brute bien plus coûteuses et par conséquent difficiles. Cette rubrique décrit la création d’une simple paire de clés SSH dans le cadre d’une utilisation rapide. Si vous avez besoin d’assistance lors de la création de votre paire de clés SSH ou si vous avez besoin de certificats supplémentaires, consultez [les étapes détaillées pour créer des paires de clés SSH et des certificats](create-ssh-keys-detailed.md).

Vous pouvez créer des machines virtuelles qui utilisent votre paire de clés SSH à l’aide du portail Azure, de l’interface de ligne de commande et de modèles :

* [Créer une machine virtuelle Linux à l’aide du portail Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer une machine virtuelle Linux sécurisée à l’aide de l’interface de ligne de commande Azure 2.0](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
