---
title: Exemples d’interface de ligne de commande Azure | Microsoft Docs
description: Exemples d’interface de ligne de commande Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/08/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: ec76e211062890b4325b5c2cb1cf186ac7b89c55
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="azure-cli-samples-for-linux-virtual-machines"></a>Exemples d’interface de ligne de commande Azure pour machines virtuelles Linux

Le tableau suivant contient des liens vers des scripts Bash créés à l’aide de l’interface de ligne de commande Azure.

| | |
|---|---|
|**Créer des machines virtuelles**||
| [Créer une machine virtuelle](./../scripts/virtual-machines-linux-cli-sample-create-vm-quick-create.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle Linux avec une configuration minimale. |
| [Créer une machine virtuelle entièrement configurée](./../scripts/virtual-machines-linux-cli-sample-create-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Crée un groupe de ressources, une machine virtuelle et toutes les ressources associées.|
| [Créer des machines virtuelles hautement disponibles](./../scripts/virtual-machines-linux-cli-sample-nlb.md?toc=%2fcli%2fazure%2ftoc.json) | Crée plusieurs machines virtuelles dans une configuration haute disponibilité avec équilibrage de charge. |
| [Créer une machine virtuelle prenant en charge Docker](./../scripts/virtual-machines-linux-cli-sample-create-docker-host.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle, configure cette machine virtuelle en tant qu’hôte Docker et exécute un conteneur NGINX. |
| [Créer une machine virtuelle et exécuter le script de configuration](./../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer NGINX. |
| [Créer une machine virtuelle avec WordPress](./../scripts/virtual-machines-linux-cli-sample-create-vm-wordpress.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle et utilise l’extension de script personnalisé Azure pour installer WordPress. |
| [Créer une machine virtuelle à partir d’un disque de système d’exploitation géré](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-managed-os-disks.md?toc=%2fcli%2fmodule%2ftoc.json) | Crée une machine virtuelle en attachant un disque géré existant en tant que disque de système d’exploitation. |
| [Créer une machine virtuelle à partir d’une capture instantanée](./../scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Crée une machine virtuelle à partir d’une capture instantanée en créant un disque géré à partir de la capture instantanée, puis en attachant le nouveau disque géré en tant que disque de système d’exploitation. |
|**Gérer le stockage**||
| [Créer un disque géré à partir d’un disque dur virtuel](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-vhd.md?toc=%2fcli%2fmodule%2ftoc.json) | Crée un disque géré à partir d’un disque dur virtuel spécialisé en tant que disque du système d’exploitation ou d’un disque dur virtuel de données en tant que disque de données.  |
| [Créer un disque géré à partir d’une capture instantanée](../scripts/virtual-machines-linux-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fcli%2fmodule%2ftoc.json) | Crée un disque géré à partir d’une capture instantanée. |
| [Copier le disque géré vers le même abonnement ou un abonnement différent](../scripts/virtual-machines-linux-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copie le disque géré vers le même abonnement ou un abonnement différent, mais dans la même région que le disque géré parent. 
| [Exporter une capture instantanée en tant que disque dur virtuel vers un compte de stockage](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fcli%2fmodule%2ftoc.json) | Exporte une capture instantanée gérée en tant que disque dur virtuel vers un compte de stockage dans une région différente. |
| [Copier la capture instantanée vers le même abonnement ou un abonnement différent](../scripts/virtual-machines-linux-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Copie la capture instantanée vers le même abonnement ou un abonnement différent, mais dans la même région que la capture instantanée parente. |
|**Mettre en réseau des machines virtuelles**||
| [Sécuriser le trafic réseau entre les machines virtuelles](./../scripts/virtual-machines-linux-cli-sample-create-vm-nsg.md?toc=%2fcli%2fazure%2ftoc.json) | Crée deux machines virtuelles, toutes les ressources associées, ainsi qu’un groupe de sécurité réseau interne et un groupe de sécurité réseau externe. |
|**Sécuriser les machines virtuelles**||
| [Chiffrer une machine virtuelle et des disques de données](./../scripts/virtual-machines-linux-cli-sample-encrypt-vm.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une solution Key Vault, une clé de chiffrement et le principal de service, puis chiffre une machine virtuelle. |
|**Surveiller les machines virtuelles**||
| [Surveiller une machine virtuelle avec Operations Management Suite](./../scripts/virtual-machines-linux-cli-sample-create-vm-oms.md?toc=%2fcli%2fazure%2ftoc.json) | Crée une machine virtuelle, installe l’agent Operations Management Suite et inscrit la machine virtuelle dans un espace de travail OMS.  |
|**Résoudre les problèmes liés aux machines virtuelles**||
| [Résoudre les problèmes liés au disque du système d’exploitation de machines virtuelles](./../scripts/virtual-machines-linux-cli-sample-mount-os-disk.md?toc=%2fcli%2fazure%2ftoc.json) | Monte le disque du système d’exploitation d’une machine virtuelle en tant que disque de données sur une seconde machine virtuelle. |
| | |
