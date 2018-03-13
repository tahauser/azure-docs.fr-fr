---
title: "Utiliser cloud-init pour mettre à jour et installer des packages dans une machine virtuelle Linux sur Azure | Microsoft Docs"
description: "Comment utiliser cloud-init pour mettre à jour et installer des packages dans une machine virtuelle Linux lors de la création avec Azure CLI 2.0"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: e45bec2a71f94c66ce3044fb81bd2d7cefdf53a5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2018
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Utiliser cloud-init pour mettre à jour et installer des packages dans une machine virtuelle Linux sur Azure
Cet article montre comment utiliser [cloud-init](https://cloudinit.readthedocs.io) pour mettre à jour des packages sur une machine virtuelle Linux ou un groupe de machines virtuelles identiques au moment de l’approvisionnement dans Azure. Ces scripts cloud-init s’exécutent au premier démarrage une fois que les ressources ont été approvisionnées par Azure. Pour plus d’informations sur le fonctionnement de cloud-init en mode natif dans Azure et sur les versions de Linux prises en charge, consultez [Présentation de cloud-init](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Mettre à jour une machine virtuelle avec cloud-init
Pour des raisons de sécurité, configurez une machine virtuelle pour appliquer les dernières mises à jour au premier démarrage. Étant donné que cloud-init fonctionne sur différentes distributions Linux, il est inutile de spécifier `apt` ou `yum` pour le gestionnaire de package. Vous définissez plutôt `package_upgrade` et laissez le processus cloud-init déterminer le mécanisme approprié pour la distribution en cours d’utilisation. Ce flux de travail vous permet d’utiliser les mêmes scripts cloud-init sur toutes les distributions.

Pour voir le processus de mise à niveau en action, créez dans l’interpréteur de commandes actif un fichier nommé *cloud_init_upgrade.txt* et collez-y la configuration suivante. Pour cet exemple, créez le fichier dans Cloud Shell et non sur votre ordinateur local. Vous pouvez utiliser l’éditeur de votre choix. Entrez `sensible-editor cloud_init_upgrade.txt` pour créer le fichier et afficher la liste des éditeurs disponibles. Choisissez le n°1 pour utiliser l’éditeur **nano**. Vérifiez que l’intégralité du fichier cloud-init est copiée correctement, en particulier la première ligne.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Avant de déployer cette image, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maintenant, créez une machine virtuelle avec [az vm create](/cli/azure/vm#az_vm_create) et spécifiez le fichier cloud-init avec `--custom-data cloud_init_upgrade.txt` comme suit :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Établissez une connexion SSH à l’adresse IP publique de votre machine virtuelle indiquée dans la sortie de la commande précédente. Entrez votre propre **publicIpAddress** comme suit :

```bash
ssh <publicIpAddress>
```

Exécutez l’outil de gestion de package et vérifiez s’il existe des mises à jour. L’exemple suivant utilise `apt-get` sur une machine virtuelle Ubuntu :

```bash
sudo apt-get upgrade
```

Étant donné que cloud-init a recherché et installé des mises à jour au démarrage, il ne doit y avoir aucune mise à jour à appliquer, comme indiqué dans l’exemple de sortie suivant :

```bash
Reading package lists... Done
Building dependency tree
Reading state information... Done
Calculating upgrade... Done
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

Vous pouvez également constater que `httpd` a été installé en exécutant `yum history` et passer en revue le référencement de sortie `httpd`. 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des exemples cloud-init supplémentaires de modifications de configuration, consultez les rubriques suivantes :
 
- [Ajouter un utilisateur Linux supplémentaire à une machine virtuelle](cloudinit-add-user.md)
- [Exécuter un gestionnaire de package pour mettre à jour les packages existants au premier démarrage](cloudinit-update-vm.md)
- [Use cloud-init to set hostname for a Linux VM in Azure](cloudinit-update-vm-hostname.md) (Utiliser cloud-init pour définir un nom d’hôte pour une machine virtuelle Linux dans Azure) 
- [Installer un package d’application, mettre à jour des fichiers de configuration et injecter des clés](tutorial-automate-vm-deployment.md)