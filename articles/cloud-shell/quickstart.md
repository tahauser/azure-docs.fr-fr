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
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 3f605645e7a53f285cb7e508034ebab0daa0d335
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Démarrage rapide de Bash dans Azure Cloud Shell

Ce document explique comment utiliser Bash dans Azure Cloud Shell dans le [portail Azure](https://ms.portal.azure.com/).

> [!NOTE]
> Un démarrage rapide de [PowerShell dans Azure Cloud Shell](quickstart-powershell.md) est également disponible.

## <a name="start-cloud-shell"></a>Démarrer Cloud Shell
1. Lancez **Cloud Shell** dans le volet de navigation supérieure du portail Azure <br>
![](media/quickstart/shell-icon.png)
2. Sélectionner un abonnement pour créer un compte de stockage et un partage Microsoft Azure Files
3. Sélectionnez Créer le stockage

> [!TIP]
> Vous êtes authentifié automatiquement pour Azure CLI 2.0 dans chaque session.

### <a name="select-the-bash-environment"></a>Sélectionnez l’environnement Bash
1. Sélectionnez la liste déroulante des environnements située à gauche de la fenêtre de l’interpréteur de commandes <br>
![](media/quickstart/env-selector.png)
2. Sélectionnez Bash

### <a name="set-your-subscription"></a>Définissez votre abonnement
1. Répertoriez les événements auxquels vous avez accès : <br>
`az account list`
2. Définissez votre abonnement préféré : <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> Votre abonnement sera mémorisé pour les sessions ultérieures avec `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un nouveau groupe de ressources dans WestUS nommé « MyRG » : <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Créer une machine virtuelle Linux
Créez une machine virtuelle Ubuntu dans votre nouveau groupe de ressources. Azure CLI 2.0 crée les clés SSH et configure la machine virtuelle avec elles. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> Les clés publiques et privées utilisées pour authentifier votre machine virtuelle sont placées dans `/home/<user>/.ssh/id_rsa` et `/home/<user>/.ssh/id_rsa.pub` par Azure CLI 2.0, par défaut. Votre dossier .ssh est conservé dans l’image de 5 Go de votre partage Azure Files attaché.

Votre nom d’utilisateur sur cette machine virtuelle sera votre nom d’utilisateur utilisé dans Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>Se connecter avec SSH à votre machine virtuelle Linux
1. Recherchez le nom de votre machine virtuelle dans la barre de recherche du portail Azure
2. Cliquez sur « Connexion », puis exécutez : `ssh username@ipaddress`

![](media/quickstart/sshcmd-copy.png)

Lors de l’établissement de la connexion SSH, vous devriez voir l’invite de bienvenue sur Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Nettoyage 
Supprimez votre groupe de ressources et toutes les ressources qu’il contient : <br>
Exécutez `az group delete -n MyRG`.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur les fichiers persistants pour Bash dans Cloud Shell](persisting-shell-storage.md) <br>
[En savoir plus sur Azure CLI 2.0](https://docs.microsoft.com/cli/azure/) <br>
[En savoir plus sur le stockage Azure Files](../storage/files/storage-files-introduction.md) <br>
