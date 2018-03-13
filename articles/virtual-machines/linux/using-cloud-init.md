---
title: "Vue d’ensemble de la prise en charge cloud-init pour les machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Vue d’ensemble des fonctionnalités cloud-init dans Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: rickstercdn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 5c1d4eb0825d132037cc3a20a17c1f417578d35d
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Prise en charge cloud-init pour les machines virtuelles dans Azure
Cet article décrit la prise en charge existante pour [cloud-init](https://cloudinit.readthedocs.io) destinée à la configuration d’une machine virtuelle ou de groupes de machines virtuelles identiques au moment de l’approvisionnement dans Azure. Ces scripts cloud-init s’exécutent au premier démarrage une fois que les ressources ont été approvisionnées par Azure.  

## <a name="cloud-init-overview"></a>Présentation de cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) est une méthode largement utilisée pour personnaliser une machine virtuelle Linux lors de son premier démarrage. Vous pouvez utiliser cloud-init pour installer des packages et écrire des fichiers, ou encore pour configurer des utilisateurs ou des paramètres de sécurité. cloud-init étant appelé pendant le processus de démarrage initial, aucune autre étape ni aucun agent ne sont nécessaires pour appliquer votre configuration.  Pour plus d’informations sur la façon de mettre correctement en forme vos fichiers `#cloud-config`, consultez le [site de documentation cloud-init](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  Les fichiers `#cloud-config` sont des fichiers texte encodés en base64.

Cloud-init fonctionne aussi sur les différentes distributions. Par exemple, vous n’utilisez pas **apt-get install** ou **yum install** pour installer un package. Au lieu de cela, vous pouvez définir une liste des packages à installer, après quoi cloud-init se charge d’utiliser automatiquement l’outil de gestion de package natif correspondant à la distribution que vous sélectionnez.

 Nous travaillons activement avec nos partenaires de distribution Linux afin de mettre des images compatibles cloud-init à disposition sur la Place de marché Azure. Ces images permettront à vos déploiements et configurations cloud-init de fonctionner de manière fluide avec des machines virtuelles et des groupes de machines virtuelles identiques Virtual Machine Scale Sets. Le tableau suivant présente la disponibilité actuelle des images compatibles avec cloud-init sur la plateforme Azure :

| Publisher | Offre | SKU | Version | Compatible avec cloud-init
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |le plus récent |Oui | 
|Canonical |UbuntuServer |14.04.5-LTS |le plus récent |Oui |
|CoreOS |CoreOS |Stable |le plus récent |Oui |
|OpenLogic |CentOS |7-CI |le plus récent |preview |
|Red Hat |RHEL |7-RAW-CI |le plus récent |preview |

Azure Stack ne prend pas en charge le provisionnement de RHEL 7.4 et de CentOS 7.4 à l’aide de cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Quelle est la différence entre cloud-init et l’agent Linux (WALA) ?
WALA est un agent spécifique à la plateforme Azure, qui est utilisé pour configurer des machines virtuelles et gérer des extensions Azure. Nous améliorons la tâche de configuration des machines virtuelles afin d’utiliser cloud-init en lieu et place de l’agent Linux, ceci pour permettre aux clients cloud-init existants de s’appuyer sur leurs scripts cloud-init actuels.  Si vous possédez des investissements existants dans des scripts cloud-init pour la configuration de systèmes Linux, **aucun autre paramétrage n’est requis** pour les activer. 

Si vous n’incluez pas le commutateur de l’interface de ligne de commande Azure `--custom-data` au moment de l’approvisionnement, WALA prend les paramètres minimaux d’approvisionnement de machines virtuelles requis pour configurer la machine virtuelle et exécuter le déploiement avec les valeurs par défaut.  Si vous référencez le commutateur cloud-init `--custom-data`, le contenu de vos données personnalisées (paramètres individuels ou script entier) écrase les données WALA définies par défaut. 

Les configurations WALA de machines virtuelles ont une durée limitée, ceci pour tenir dans le délai maximal d’approvisionnement des machines virtuelles.  Les configurations cloud-init appliquées aux machines virtuelles n’étant soumises à aucune contrainte de temps, aucune mise en échec de déploiement par expiration de ces dernières n’est possible. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Déploiement d’une machine virtuelle compatible cloud-init
Il est aussi simple de déployer une machine virtuelle cloud-init que de référencer une distribution compatible cloud-init durant le déploiement.  Les gestionnaires de la distribution Linux doivent décider d’activer cloud-init et de l’intégrer dans leurs images publiées Azure. Une fois que vous avez confirmé que l’image que vous souhaitez déployer est compatible cloud-init, vous pouvez utiliser l’interface de ligne de commande Azure pour le déploiement. 

La première étape du déploiement de cette image est la création d’un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). Un groupe de ressources Azure est un conteneur logique dans lequel les ressources Azure sont déployées et gérées. 

L’exemple suivant crée un groupe de ressources nommé *myResourceGroup* à l’emplacement *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Il est ensuite question de créer un fichier dans l’interpréteur de commandes actuel, nommé *cloud-init.txt*, et de le coller dans la configuration suivante. Pour cet exemple, créez le fichier dans Cloud Shell et non sur votre ordinateur local. Vous pouvez utiliser l’éditeur de votre choix. Entrez `sensible-editor cloud-init.txt` pour créer le fichier et afficher la liste des éditeurs disponibles. Choisissez le n°1 pour utiliser l’éditeur **nano**. Vérifiez que l’intégralité du fichier cloud-init est copiée, en particulier la première ligne :

```yaml
#cloud-config
package_upgrade: true
packages:
  -httpd
```
Appuyez sur `ctrl-X` pour quitter le fichier, saisissez `y` pour l’enregistrer, puis appuyez sur `enter` pour confirmer le nom lors de la sortie.

Enfin, vous devez créer une machine virtuelle avec la commande [az vm create](/cli/azure/vm#az_vm_create). 

L’exemple suivant crée une machine virtuelle nommée *centos74* et des clés SSH si elles n’existent pas déjà dans un emplacement de clé par défaut. Pour utiliser un ensemble spécifique de clés, utilisez l’option `--ssh-key-value`.  Utilisez le paramètre `--custom-data` à transmettre dans votre fichier de configuration cloud-init. Indiquez le chemin complet vers la configuration *cloud-init.txt* si vous avez enregistré le fichier en dehors de votre répertoire de travail actuel. L’exemple suivant crée une machine virtuelle nommée *centos74* :

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Lorsque la machine virtuelle est créée, l’interface de ligne de commande Azure affiche des informations spécifiques à votre déploiement. Notez la valeur de `publicIpAddress`. Cette adresse est utilisée pour accéder à la machine virtuelle.  Vous devez patienter un certain temps que la machine virtuelle soit créée, que les packages soient installés et que l’application démarre. Certaines tâches en arrière-plan continuent à s’exécuter une fois que l’interface CLI Azure vous renvoie à l’invite de commandes. Vous pouvez exécuter une commande SSH dans la machine virtuelle et suivre la procédure décrite dans la section de résolution des problèmes afin d’afficher les journaux cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Résolution des problèmes cloud-init
Une fois la machine virtuelle configurée, cloud-init est exécuté dans l’ensemble des modules et des scripts définis dans `--custom-data` pour la configuration de la machine virtuelle.  Si vous devez corriger des erreurs ou des omissions dans la configuration, vous devez chercher le nom du module (`disk_setup` ou `runcmd` par exemple) dans le journal cloud-init, situé dans **/var/log/cloud-init.log**.

> [!NOTE]
> Toutes les défaillances de module n’entraînent pas d’échec irrécupérable de configuration cloud-init. Par exemple, si vous utilisez le module `runcmd`, si le script est mis en échec, cloud-init fera tout de même état d’une réussite de configuration permise par l’exécution du module runcmd.

Pour plus d’informations sur la journalisation de cloud-init, consultez la [documentation cloud-init](http://cloudinit.readthedocs.io/en/latest/topics/logging.html). 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des exemples cloud-init de modifications de configuration, consultez les documents suivants :
 
- [Ajouter un utilisateur Linux supplémentaire à une machine virtuelle](cloudinit-add-user.md)
- [Exécuter un gestionnaire de package pour mettre à jour les packages existants au premier démarrage](cloudinit-update-vm.md)
- [Use cloud-init to set hostname for a Linux VM in Azure](cloudinit-update-vm-hostname.md) (Utiliser cloud-init pour définir un nom d’hôte pour une machine virtuelle Linux dans Azure) 
- [Installer un package d’application, mettre à jour des fichiers de configuration et injecter des clés](tutorial-automate-vm-deployment.md)
