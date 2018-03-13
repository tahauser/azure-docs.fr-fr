---
title: "Résoudre les problèmes de déploiement de machine virtuelle Windows dans Azure | Microsoft Docs"
description: "Résoudre les problèmes de déploiement de Resource Manager lors de la création d’une machine virtuelle Windows dans Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: JiangChen79
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fff29f6cfed4989386ca5bbd12184dce525add76
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2018
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Résoudre les problèmes de déploiement lors de la création d’une machine virtuelle Windows dans Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Problèmes principaux
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Pour toute autre question ou problème concernant le déploiement de machine virtuelle, consultez [Troubleshoot deploying Linux virtual machine issues in Azure](troubleshoot-deploy-vm.md) (Résolution des problèmes de déploiement de la machine virtuelle Linux dans Azure).

## <a name="collect-activity-logs"></a>Collecte des journaux d’activité
Pour résoudre les problèmes, commencez par collecter les journaux d’activité afin d’identifier l’erreur associée au problème. Les liens suivants contiennent des informations détaillées sur la marche à suivre.

[Voir les opérations de déploiement](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Afficher les journaux d’activité pour gérer les ressources Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**O :** si le système d’exploitation est de type Windows généralisé et s’il est téléchargé et/ou capturé avec le paramètre généralisé, il n’y a aucune erreur. De même, si le système d’exploitation est de type Windows spécialisé et s’il est téléchargé et/ou capturé avec le paramètre spécialisé, il n’y a aucune erreur.

**Erreurs de téléchargement :**

**N<sup>1</sup> :** si le système d’exploitation est de type Windows généralisé et qu’il est téléchargé avec le paramètre spécialisé, cela entraîne une erreur de délai d’attente de configuration et la machine virtuelle est bloquée au niveau de l’écran OOBE.

**N<sup>2</sup> :** si le système d’exploitation est de type Windows spécialisé et qu’il est chargé avec le paramètre généralisé, vous obtiendrez une erreur d’échec d’approvisionnement, et la machine virtuelle sera bloquée au niveau de l’écran OOBE, car la nouvelle machine virtuelle s’exécutera avec le nom d’ordinateur, le nom d’utilisateur et le mot de passe d’origine.

**Résolution :**

Pour corriger ces deux erreurs, utilisez [Add-AzureRmVhd pour télécharger le disque dur virtuel d’origine](https://msdn.microsoft.com/library/mt603554.aspx), disponible en mode local, avec le même paramétrage que pour le système d’exploitation (généralisé/spécialisé). Pour effectuer un téléchargement de type généralisé, n’oubliez pas d’exécuter d’abord sysprep.

**Erreurs de capture :**

**N<sup>3</sup> :** si le système d’exploitation est de type Windows généralisé et s’il est capturé avec le paramètre spécialisé, cela entraîne une erreur de délai d’attente de configuration, car la machine virtuelle d’origine n’est pas utilisable tant qu’elle est marquée comme généralisée.

**N<sup>4</sup> :** si le système d’exploitation est de type Windows spécialisé et s’il est capturé avec le paramètre généralisé, cela entraîne une erreur d’échec d’approvisionnement, car la nouvelle machine virtuelle s’exécute avec le nom de l’ordinateur, le nom d’utilisateur et le mot de passe d’origine. En outre, la machine virtuelle d’origine n’est pas utilisable tant qu’elle est marquée comme spécialisée.

**Résolution :**

Pour corriger ces deux erreurs, supprimez l’image actuelle du portail, et [effectuez une nouvelle capture à partir des disques durs virtuels en cours](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), avec le même paramétrage que celui du système d’exploitation (généralisé/spécialisé).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problème : image personnalisée/de la galerie/de la Place de marché ; échec d’allocation
Cette erreur se produit lorsque la nouvelle demande de la machine virtuelle est épinglée à un cluster qui ne prend pas en charge la taille de machine virtuelle requise ou qui n’a pas d’espace libre suffisant pour prendre en charge la demande.

**Cause 1 :** le cluster ne peut pas prendre en charge la taille de machine virtuelle demandée.

**Résolution 1 :**

* Relancez la requête en utilisant une taille inférieure pour la machine virtuelle.
* Si la taille de la machine virtuelle requise ne peut pas être modifiée :
  * Arrêtez toutes les machines virtuelles dans le groupe à haute disponibilité.
    Cliquez sur **Groupes de ressources** > *votre groupe de ressources* > **Ressources** > *votre groupe à haute disponibilité* > **Machines virtuelles** > *votre machine virtuelle* > **Arrêter**.
  * Une fois que toutes les machines virtuelles sont arrêtées, créez une machine virtuelle à la taille souhaitée.
  * Démarrez la nouvelle machine virtuelle en premier, puis sélectionnez chacune des machines virtuelles arrêtées et cliquez sur **Démarrer**.

**Cause 2 :** le cluster n’a pas de ressources libres.

**Résolution 2 :**

* Relancez la demande ultérieurement.
* Si la nouvelle machine virtuelle peut faire partie d’un autre groupe à haute disponibilité
  * Créez une machine virtuelle dans un autre groupe à haute disponibilité (dans la même région).
  * Ajoutez la nouvelle machine virtuelle au même réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lorsque vous démarrez une machine virtuelle Windows arrêtée ou que vous redimensionnez Windows une machine virtuelle existante dans Azure, consultez [Résoudre les problèmes de déploiement Resource Manager liés au redémarrage ou au redimensionnement d’une machine virtuelle Windows existante dans Azure](restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

