---
title: "Problèmes de redémarrage ou de redimensionnement de machines virtuelles | Microsoft Docs"
description: "Résoudre les problèmes de déploiement Classic liés au redémarrage ou au redimensionnement d’une machine virtuelle Windows existante dans Azure"
services: virtual-machines-windows
documentationcenter: 
author: Deland-Han
manager: felixwu
editor: 
tags: top-support-issue
ms.assetid: aa854fff-c057-4b8e-ad77-e4dbc39648cc
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: required
ms.date: 11/03/2017
ms.devlang: na
ms.author: delhan
ms.openlocfilehash: bed5da25042d29983bad9a80cd44bdd7df261c2e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="troubleshoot-classic-deployment-issues-with-restarting-or-resizing-an-existing-windows-virtual-machine-in-azure"></a>Résoudre les problèmes de déploiement Classic liés au redémarrage ou au redimensionnement d’une machine virtuelle Windows existante dans Azure
> [!div class="op_single_selector"]
> * [Classique](virtual-machines-windows-classic-restart-resize-error-troubleshooting.md)
> * [Gestionnaire de ressources](../restart-resize-error-troubleshooting.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> 
> 

Lorsque vous essayez de démarrer une machine virtuelle Azure arrêtée ou de redimensionner une machine virtuelle Azure existante, l’erreur la plus fréquemment rencontrée est un échec d’allocation. Cette erreur se produit lorsque le cluster ou la région n’ont pas de ressources disponibles ou ne prennent pas en charge la taille de machine virtuelle demandée.

> [!IMPORTANT]
> Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : [Resource Manager et classique](../../../azure-resource-manager/resource-manager-deployment-model.md).  Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager.
> 
> 

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="collect-audit-logs"></a>Collecter des journaux d’audit
Pour résoudre les problèmes, commencez par collecter les journaux d’audit afin d’identifier l’erreur associée au problème.

Dans le portail Azure, cliquez sur **Parcourir** > **Machines virtuelles** > *votre machine virtuelle Windows* > **Paramètres** > **Journaux d’audit**.

## <a name="issue-error-when-starting-a-stopped-vm"></a>Problème : erreur lors du démarrage d’une machine virtuelle arrêtée
Vous essayez de démarrer une machine virtuelle arrêtée, mais obtenez un échec d’allocation.

### <a name="cause"></a>Cause :
La demande de démarrage de la machine virtuelle arrêtée doit être exécutée sur le cluster d’origine qui héberge le service cloud. Toutefois, le cluster n’a pas d’espace libre pour répondre à la demande.

### <a name="resolution"></a>Résolution :
* Créez un service cloud et associez-le à une région ou à un réseau virtuel basé sur une région, mais non à un groupe d’affinités.
* Supprimez la machine virtuelle arrêtée.
* Recréez la machine virtuelle dans le nouveau service cloud à l’aide des disques.
* Démarrez la machine virtuelle recréée.

Si vous obtenez une erreur lorsque vous tentez de créer un service cloud, vous pouvez soit réessayer ultérieurement, soit modifier la région du service cloud.

> [!IMPORTANT]
> Le nouveau service cloud aura un nouveau nom et une nouvelle adresse IP virtuelle. Vous devrez donc modifier ces valeurs pour toutes les dépendances qui utilisent ces informations pour le service cloud existant.
> 
> 

## <a name="issue-error-when-resizing-an-existing-vm"></a>Problème : erreur lors du redimensionnement d’une machine virtuelle existante
Vous essayez de redimensionner une machine virtuelle existante, mais obtenez un échec d’allocation.

### <a name="cause"></a>Cause :
La demande de redimensionnement de la machine virtuelle doit être exécutée sur le cluster d’origine qui héberge le service cloud. Toutefois, le cluster ne prend pas en charge la taille de machine virtuelle demandée.

### <a name="resolution"></a>Résolution :
Réduisez la taille de la machine virtuelle demandée, puis relancez la demande de redimensionnement.

* Cliquez sur **Parcourir tout** > **Machines virtuelles (classiques)** > *votre machine virtuelle* > **Paramètres** > **Taille**. Pour connaître la procédure détaillée, consultez [Redimensionner la machine virtuelle](https://msdn.microsoft.com/library/dn168976.aspx).

S’il est impossible de réduire la taille de la machine virtuelle, procédez comme suit :

* Créez un service Cloud, en vous assurant qu’il n’est pas lié à un groupe d’affinités et pas associé à un réseau virtuel lié à un groupe d’affinités.
* Créez dans ce service une machine virtuelle plus volumineuse.

Vous pouvez consolider toutes vos machines virtuelles dans le même service cloud. Si votre service cloud existant est associé à un réseau virtuel basé sur une région, vous pouvez connecter le nouveau service cloud au réseau virtuel existant.

Si le service cloud existant n’est pas associé à un réseau virtuel basé sur une région, vous devez supprimer les machines virtuelles du service cloud existant, puis les recréer dans le nouveau service cloud à partir de leurs disques. Toutefois, il est important de se rappeler que le nouveau service cloud aura un nouveau nom et une nouvelle adresse IP virtuelle. Vous devrez donc mettre à jour ces valeurs pour toutes les dépendances qui utilisent actuellement ces informations pour le service cloud existant.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes lorsque vous créez une machine virtuelle Windows dans Azure, consultez [Résoudre les problèmes de déploiement liés à la création d’une machine virtuelle Windows dans Azure](../troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

