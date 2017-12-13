---
title: "Utiliser cloud-init pour exécuter un script bash dans une machine virtuelle Linux sur Azure | Microsoft Docs"
description: "Comment utiliser cloud-init pour exécuter un script bash dans une machine virtuelle Linux lors de la création avec Azure CLI 2.0"
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
ms.openlocfilehash: 1fcc432e8437a7fd284a75aa40454848a2af3006
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2017
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>Utiliser cloud-init pour exécuter un script bash dans une machine virtuelle Linux sur Azure
Cet article montre comment utiliser [cloud-init](https://cloudinit.readthedocs.io) pour exécuter un script bash existant sur une machine virtuelle Linux ou un groupe de machines virtuelles identiques au moment de l’approvisionnement dans Azure. Ces scripts cloud-init s’exécutent au premier démarrage une fois que les ressources ont été approvisionnées par Azure. Pour plus d’informations sur le fonctionnement de cloud-init en mode natif dans Azure et sur les versions de Linux prises en charge, consultez [Présentation de cloud-init](using-cloud-init.md).

## <a name="run-a-bash-script-with-cloud-init"></a>Exécuter un script bash avec init-cloud
Avec cloud-init, vous n’avez pas besoin de convertir vos scripts existant en un cloud-config, cloud-init accepte plusieurs types d’entrées, dont l’une d’entre elles est un script bash.

Si vous utilisez l’extension Azure de script personnalisé Linux pour exécuter vos scripts, vous pouvez les migrer pour utiliser cloud-init. Toutefois, les extensions Azure ont une fonctionnalité de création de rapports intégrée pour signaler les échecs de script ; un déploiement d’image cloud-init n’échouera PAS si le script échoue.

Pour voir cette fonctionnalité en action, créez un script bash pour le test. Comme le fichier `#cloud-config` cloud-init, ce script doit se trouver en local, à l’emplacement où vous allez exécuter les commandes Azure CLI pour approvisionner votre machine virtuelle.  Pour cet exemple, créez le fichier dans Cloud Shell et non sur votre ordinateur local. Vous pouvez utiliser l’éditeur de votre choix. Entrez `sensible-editor simple_bash.sh` pour créer le fichier et afficher la liste des éditeurs disponibles. Choisissez le n°1 pour utiliser l’éditeur **nano**. Vérifiez que l’intégralité du fichier cloud-init est copiée correctement, en particulier la première ligne.  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

Avant de déployer cette image, vous devez créer un groupe de ressources avec la commande [az group create](/cli/azure/group#create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Maintenant, créez une machine virtuelle avec [az vm create](/cli/azure/vm#create) et spécifiez le fichier de script bash avec `--custom-data simple_bash.sh` comme suit :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Vérifier qu’un script bash a été exécuté
Établissez une connexion SSH à l’adresse IP publique de votre machine virtuelle indiquée dans la sortie de la commande précédente. Entrez votre propre **publicIpAddress** comme suit :

```bash
ssh <publicIpAddress>
```

Remplacez par le répertoire **/tmp** et vérifiez que ce fichier myScript.txt existe et qu’il contient le texte approprié.  Si ce n’est pas le cas, vous pouvez vérifier **/var/log/cloud-init.log** pour plus d’informations.  Recherchez l’entrée suivante :

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des exemples cloud-init supplémentaires de modifications de configuration, consultez les rubriques suivantes :
 
- [Use cloud-init to add a user to a Linux VM in Azure](cloudinit-add-user.md) (Utiliser cloud-init pour ajouter un utilisateur à une machine virtuelle dans Azure)
- [Use cloud-init to update and install packages in a Linux VM in Azure](cloudinit-update-vm.md) (Utiliser cloud-init pour mettre à jour et installer des packages dans une machine virtuelle Linux dans Azure)
- [Use cloud-init to set hostname for a Linux VM in Azure](cloudinit-update-vm-hostname.md) (Utiliser cloud-init pour définir un nom d’hôte pour une machine virtuelle Linux dans Azure) 
- [Comment personnaliser une machine virtuelle Linux au premier démarrage](tutorial-automate-vm-deployment.md)