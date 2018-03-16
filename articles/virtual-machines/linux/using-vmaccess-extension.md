---
title: "Réinitialiser l’accès à une machine virtuelle Linux Azure | Microsoft Docs"
description: "Guide pratique pour gérer les utilisateurs administratifs et réinitialiser l’accès sur des machines virtuelles Linux à l’aide de l’extension VMAccess et Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 261a9646-1f93-407e-951e-0be7226b3064
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 08/04/2017
ms.author: danlep
ms.openlocfilehash: 235a6367ad317945cfeaaa6aae4e060208fb8e8e
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli-20"></a>Gérer les utilisateurs administratifs, SSH et vérifier ou réparer les disques de machines virtuelles Linux à l’aide de l’extension VMAccess avec Azure CLI 2.0
Le disque de votre machine virtuelle Linux affiche des erreurs. Vous avez d'une certaine manière réinitialisé le mot de passe racine de votre machine virtuelle Linux ou supprimé accidentellement votre clé privée SSH. Dans les anciens centres de données, vous deviez aller sur place et ouvrir le KVM pour accéder à la console du serveur. Considérez l’extension Azure VMAccess comme ce commutateur KVM qui vous permet d’accéder à la console pour réinitialiser l’accès à Linux ou effectuer la maintenance au niveau du disque.

Cet article vous explique comment utiliser l’extension Azure VMAccess pour vérifier ou réparer un disque, réinitialiser l’accès des utilisateurs administratifs, gérer les comptes d’utilisateur ou réinitialiser la configuration SSH sous Linux. Vous pouvez également suivre ces étapes avec [Azure CLI 1.0](using-vmaccess-extension-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="ways-to-use-the-vmaccess-extension"></a>Méthodes d’utilisation de l’extension VMAccess
Il existe deux façons d’utiliser l’extension VMAccess sur vos machines virtuelles Linux :

* À l’aide de l’interface Azure CLI 2.0 et des paramètres requis.
* [À l’aide de fichiers JSON bruts que l’extension VMAccess va traiter](#use-json-files-and-the-vmaccess-extension) et exploiter.

Les exemples suivants utilisent des commandes [az vm user](/cli/azure/vm/user). Pour suivre ces étapes, vous devez disposer de la dernière version [d’Azure CLI 2.0](/cli/azure/install-az-cli2) et vous connecter à un compte Azure avec la commande [az login](/cli/azure/reference-index#az_login).

## <a name="reset-ssh-key"></a>Réinitialisation d’une clé SSH
L’exemple suivant réinitialise la clé SSH pour l’utilisateur `azureuser` sur la machine virtuelle `myVM` :

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="reset-password"></a>Réinitialiser le mot de passe
L’exemple suivant réinitialise le mot de passe pour l’utilisateur `azureuser` sur la machine virtuelle `myVM` :

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Redémarrer SSH
L’exemple suivant redémarre le démon SSH et réinitialise la configuration SSH à ses valeurs par défaut sur une machine virtuelle nommée `myVM` :

```azurecli
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Créer un utilisateur administratif/sudo
L’exemple suivant crée un utilisateur nommé `myNewUser` avec des autorisations **sudo**. Le compte utilise une clé SSH pour l’authentification sur la machine virtuelle nommée `myVM`. Cette méthode est conçue pour vous aider à réaccéder à une machine virtuelle en cas de perte ou d’oubli des informations d’identification actuelles. En guise de bonne pratique, vous devez limiter les comptes avec des autorisations **sudo**.

```azurecli
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```



## <a name="delete-a-user"></a>Supprimer un utilisateur
L’exemple suivant supprime un utilisateur nommé `myNewUser` sur la machine virtuelle `myVM` :

```azurecli
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```


## <a name="use-json-files-and-the-vmaccess-extension"></a>Utilisation de fichiers JSON et de l’extension VMAccess
Les exemples suivants utilisent des fichiers JSON bruts. Utilisez [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set), puis appelez vos fichiers JSON. Ces fichiers JSON peuvent également être appelés à partir de modèles Azure. 

### <a name="reset-user-access"></a>Réinitialisation de l’accès utilisateur
Si vous ne pouvez plus accéder à la racine de votre machine virtuelle Linux, vous pouvez lancer un script VMAccess pour réinitialiser la clé SSH ou le mot de passe d’un utilisateur.

Pour réinitialiser la clé SSH d’un utilisateur, créez un fichier nommé `reset_ssh_key.json` et ajoutez des paramètres au format suivant. Remplacez par vos propres valeurs les paramètres `username` et `ssh_key` :

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_ssh_key.json
```

Pour réinitialiser le mot de passe d’un utilisateur, créez un fichier nommé `reset_user_password.json` et ajoutez des paramètres au format suivant. Remplacez par vos propres valeurs les paramètres `username` et `password` :

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Redémarrer SSH
Pour redémarrer le démon SSH et réinitialiser la configuration SSH à ses valeurs par défaut, créez un fichier nommé `reset_sshd.json`. Ajoutez le contenu suivant :

```json
{
  "reset_ssh": true
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Gérer les utilisateurs administratifs

Pour créer un utilisateur avec des autorisations **sudo** qui utilise une clé SSH au moment de l’authentification, créez un fichier nommé `create_new_user.json` et ajoutez des paramètres au format suivant. Remplacez par vos propres valeurs les paramètres `username` et `ssh_key`. Cette méthode est conçue pour vous aider à réaccéder à une machine virtuelle en cas de perte ou d’oubli des informations d’identification actuelles. En guise de bonne pratique, vous devez limiter les comptes avec des autorisations **sudo**.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Pour supprimer un utilisateur, créez un fichier nommé `delete_user.json` avec le contenu suivant. Remplacez par vos propres valeurs le paramètre `remove_user` :

```json
{
  "remove_user":"myNewUser"
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Vérification ou réparation du disque
L’utilisation de VMAccess vous permet également de vérifier et de réparer un disque que vous avez ajouté à la machine virtuelle Linux.

Pour vérifier puis réparer le disque, créez un fichier nommé `disk_check_repair.json` et ajoutez des paramètres au format suivant. Remplacez par vos propres valeurs le nom de `repair_disk` :

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Exécutez le script VMAccess avec :

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```

## <a name="next-steps"></a>Étapes suivantes
La mise à jour de Linux à l’aide d’une extension Azure VMAccess est une méthode permettant d’apporter des modifications à une machine virtuelle Linux en cours d’exécution. Vous pouvez également utiliser des outils tels que cloud-init et des modèles Azure Resource Manager pour modifier votre machine virtuelle Linux au démarrage.

[Extensions et fonctionnalités de machine virtuelle pour Linux](extensions-features.md)

[Création de modèles Azure Resource Manager avec des extensions de machine virtuelle Linux](../windows/template-description.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Utilisation de cloud-init pour personnaliser une machine virtuelle Linux lors de la création](using-cloud-init.md)

