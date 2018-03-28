---
title: Démarrage rapide de Bash dans Azure Cloud Shell | Microsoft Docs
description: Démarrage rapide de Bash dans Cloud Shell
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: juluk
ms.openlocfilehash: e48c54216c5c4ae8e53d4802aafce8883ee97c11
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Démarrage rapide de Bash dans Azure Cloud Shell

Ce document explique comment utiliser Bash dans Azure Cloud Shell dans le [portail Azure](https://ms.portal.azure.com/).

> [!NOTE]
> Un démarrage rapide de [PowerShell dans Azure Cloud Shell](quickstart-powershell.md) est également disponible.

## <a name="start-cloud-shell"></a>Démarrer Cloud Shell
1. Lancez **Cloud Shell** dans le volet de navigation en haut du Portail Azure. <br>
![](media/quickstart/shell-icon.png)

2. Sélectionnez un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files.
3. Sélectionnez Créer le stockage

> [!TIP]
> L’authentification sur Azure CLI 2.0 est automatique à chaque session.

### <a name="select-the-bash-environment"></a>Sélectionnez l’environnement Bash
Vérifiez que la liste déroulante des environnements située à gauche de la fenêtre de l’interpréteur de commandes indique `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Définissez votre abonnement
1. Listez les abonnements auxquels vous avez accès.
```azurecli-interactive
az account list
```

2. Définissez votre abonnement préféré : <br>
```azurecli-interactive
az account set --subscription my-subscription-name`
```

> [!TIP]
> Votre abonnement sera mémorisé pour les sessions ultérieures avec `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un nouveau groupe de ressources, nommé « MyRG », dans la région États-Unis de l’Ouest.
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Créer une machine virtuelle Linux
Créez une machine virtuelle Ubuntu dans votre nouveau groupe de ressources. Azure CLI 2.0 crée des clés SSH et les utilise pour configurer la machine virtuelle. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Si l’option `--generate-ssh-keys` est utilisée, Azure CLI 2.0 crée et configure des clés publiques et privées dans le répertoire `$Home` et la machine virtuelle. Par défaut, les clés sont placées dans Cloud Shell aux emplacements `/home/<user>/.ssh/id_rsa` et `/home/<user>/.ssh/id_rsa.pub`. Le dossier `.ssh` est conservé dans l’image de 5 Go du partage de fichiers Azure attaché servant à conserver `$Home`.

Votre nom d’utilisateur sur cette machine virtuelle sera votre nom d’utilisateur utilisé dans Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>Se connecter avec SSH à votre machine virtuelle Linux
1. Recherchez le nom de votre machine virtuelle dans la barre de recherche du Portail Azure.
2. Cliquez sur « Se connecter » pour obtenir le nom de votre machine virtuelle et votre adresse IP publique. <br>
![](media/quickstart/sshcmd-copy.png)

3. Établissez une connexion SSH avec votre machine virtuelle avec la commande `ssh`.
```
ssh username@ipaddress
```

Lors de l’établissement de la connexion SSH, vous devriez voir l’invite de bienvenue sur Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Nettoyage 
1. Fermez votre session SSH.
```azurecli-interactive
exit
```

2. Supprimez votre groupe de ressources et toutes les ressources qu’il contient.
```azurecli-interactive
Run `az group delete -n MyRG`
```

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les fichiers persistants pour Bash dans Cloud Shell](persisting-shell-storage.md) <br>
[En savoir plus sur Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[En savoir plus sur le stockage Azure Files](../storage/files/storage-files-introduction.md) <br>
