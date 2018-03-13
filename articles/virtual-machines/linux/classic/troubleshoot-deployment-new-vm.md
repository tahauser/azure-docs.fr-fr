---
title: "Résoudre les problèmes de déploiement de machines virtuelles Linux - Classic | Microsoft Docs"
description: "Résoudre les problèmes de déploiement classiques lorsque vous créez une machine virtuelle Linux dans Azure"
services: virtual-machines-linux
documentationcenter: 
author: JiangChen79
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: c8a963fa-6b2a-4c7a-a1f4-7793adb02b19
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/06/2016
ms.author: cjiang
ms.openlocfilehash: 581fbaa477bd603fea5fdc0ef77c6ef7498b7897
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="troubleshoot-classic-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Résoudre les problèmes de déploiement classiques liés à la création d’une machine virtuelle Linux dans Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

> [!IMPORTANT] 
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../resource-manager-deployment-model.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour la version de Resource Manager de cet article, voir [ici](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Collecter des journaux d’audit
Pour résoudre les problèmes, commencez par collecter les journaux d’audit afin d’identifier l’erreur associée au problème.

Dans le portail Azure, cliquez sur **Parcourir** > **Machines virtuelles** > *votre machine virtuelle Windows* > **Paramètres** > **Journaux d’audit**.

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**O :** si le système d’exploitation est de type Linux généralisé et s’il est chargé et/ou capturé avec le paramètre généralisé, il n’y aura aucune erreur. De même, si le système d’exploitation est de type Linux spécialisé et qu’il est chargé et/ou capturé avec le paramètre spécialisé, il n’y aura aucune erreur.

**Erreurs de chargement :**

**N<sup>1</sup> :** si le système d’exploitation est de type Linux généralisé et qu’il est chargé comme spécialisé, vous obtiendrez une erreur de délai d’attente d’approvisionnement, car la machine virtuelle est bloquée à l’étape d’approvisionnement.

**N<sup>2</sup> :** si le système d’exploitation est de type Linux spécialisé et qu’il est chargé comme généralisé, vous obtiendrez une erreur d’échec d’approvisionnement, car la nouvelle machine virtuelle s’exécute avec le nom d’ordinateur, le nom d’utilisateur et le mot de passe d’origine.

**Résolution :**

Pour corriger ces deux erreurs, chargez le disque dur virtuel d’origine, disponible en mode local, avec le même paramétrage que pour le système d’exploitation (généralisé/spécialisé). Pour effectuer un chargement de type généralisé, n’oubliez pas d’exécuter d’abord -deprovision. Consultez [Création et téléchargement d’un disque dur virtuel contenant le système d’exploitation Linux](create-upload-vhd-classic.md) pour obtenir plus d’informations.

**Erreurs de capture :**

**N<sup>3</sup> :** si le système d’exploitation est de type Linux généralisé et qu’il est capturé comme spécialisé, vous obtiendrez une erreur de délai d’attente d’approvisionnement, car la machine virtuelle d’origine n’est pas utilisable tant qu’elle est identifiée comme généralisée.

**N<sup>4</sup> :** si le système d’exploitation est de type Linux spécialisé et qu’il est capturé comme généralisé, vous obtiendrez une erreur d’échec d’approvisionnement, car la nouvelle machine virtuelle s’exécute avec le nom d’ordinateur, le nom d’utilisateur et le mot de passe d’origine. En outre, la machine virtuelle d’origine n’est pas utilisable tant qu’elle est marquée comme spécialisée.

**Résolution :**

Pour corriger ces deux erreurs, supprimez l’image actuelle du portail, et [effectuez une nouvelle capture à partir des disques durs virtuels en cours](capture-image-classic.md) , avec le même paramétrage que celui du système d’exploitation (généralisé/spécialisé).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problème : image personnalisée / galerie / marketplace ; échec d’allocation
Cette erreur se produit lorsque la nouvelle demande de la machine virtuelle est envoyée à un cluster qui n’a pas d’espace libre pour prendre en charge la demande, ou qui ne peut pas prendre en charge la taille de machine virtuelle demandée. Il n’est pas possible de combiner différentes séries de machines virtuelles dans un même service Cloud. Par conséquent, si vous essayez de créer une machine virtuelle d’une taille que votre service Cloud ne peut pas prendre en charge, la demande de calcul échoue.

Selon les contraintes du service cloud que vous utilisez pour créer la machine virtuelle, vous pouvez rencontrer une erreur provoquée par deux situations distinctes.

**Cause 1 :** le service Cloud est épinglé à un cluster spécifique ou il est lié à un groupe d’affinités, et par conséquent épinglé à un cluster spécifique de par sa conception. Ainsi, les nouvelles demandes de ressources de calcul portant sur ce groupe d’affinités sont tentées dans le cluster hébergeant les ressources existantes. Toutefois, un même cluster peut soit ne pas prendre en charge la taille de la machine virtuelle demandée, soit ne pas avoir suffisamment d’espace, ce qui entraîne une erreur d’allocation. Cela est vrai que les ressources soient créées par le biais d’un nouveau service cloud ou d’un service cloud existant.

**Résolution 1 :**

* Créez un service cloud et associez-le à une région ou à un réseau virtuel basé sur une région.
* Créez une machine virtuelle dans le nouveau service cloud.
  Si vous obtenez une erreur lorsque vous tentez de créer un service cloud, vous pouvez soit réessayer ultérieurement, soit modifier la région du service cloud.

> [!IMPORTANT]
> Si vous avez essayé de créer une machine virtuelle dans un service Cloud existant, mais que vous n’avez pas réussi, et que vous avez donc dû créer un service Cloud pour votre nouvelle machine virtuelle, vous pouvez décider de consolider toutes vos machines virtuelles dans le même service Cloud. Pour ce faire, supprimez les machines virtuelles du service cloud existant, puis recapturez-les à partir de leurs disques dans le nouveau service cloud. Toutefois, il est important de se rappeler que le nouveau service cloud aura un nouveau nom et une nouvelle adresse IP virtuelle. Vous devrez donc mettre à jour ces valeurs pour toutes les dépendances qui utilisent actuellement ces informations pour le service cloud existant.
> 
> 

**Cause 2 :** le service Cloud est associé à un réseau virtuel qui est lié à un groupe d’affinités, et par conséquent épinglé à un cluster spécifique de par sa conception. Toutes les nouvelles demandes de ressources de calcul portant sur ce groupe d’affinités sont donc tentées dans le cluster hébergeant les ressources existantes. Toutefois, un même cluster peut soit ne pas prendre en charge la taille de la machine virtuelle demandée, soit ne pas avoir suffisamment d’espace, ce qui entraîne une erreur d’allocation. Cela est vrai que les ressources soient créées par le biais d’un nouveau service cloud ou d’un service cloud existant.

**Résolution 2 :**

* Créez un réseau virtuel régional.
* Créez la machine virtuelle dans le nouveau réseau virtuel.
* [Connectez votre réseau virtuel existant](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/) au nouveau réseau virtuel. Consultez plus d’informations sur les [réseaux virtuels régionaux](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/). Une autre possibilité consiste à [effectuer la migration de votre réseau virtuel basé sur un groupe d’affinités vers un réseau virtuel régional](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/), puis à créer la machine virtuelle.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lorsque vous démarrez une machine virtuelle Linux arrêtée ou que vous redimensionnez une machine virtuelle Linux existante dans Azure, consultez [Résoudre les problèmes de déploiement classique liés au redémarrage ou au redimensionnement d’une machine virtuelle Linux existante dans Azure](restart-resize-error-troubleshooting.md).

