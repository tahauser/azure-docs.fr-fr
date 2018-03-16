---
title: "Résoudre les problèmes d’échec de basculement vers Azure | Microsoft Docs"
description: "Cet article explique comment résoudre les erreurs courantes qui surviennent lors d’un basculement vers Azure"
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/09/2018
ms.author: ponatara
ms.openlocfilehash: 5c94e26c4639284f7e4c53d924f16040118d996c
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-errors-when-failing-over-a-virtual-machine-to-azure"></a>Résoudre les erreurs se produisant lors du basculement d’une machine virtuelle vers Azure
Vous pouvez recevoir les erreurs suivantes lorsque vous procédez au basculement d’une machine virtuelle vers Azure. Pour résoudre les problèmes, servez-vous des étapes décrites pour chaque condition d’erreur.


## <a name="failover-failed-with-error-id-28031"></a>Le basculement a échoué avec l’ID d’erreur 28031

Site Recovery n’a pas pu créer de machine virtuelle basculée dans Azure. L’une des raisons suivantes peut en être la cause :

* Le quota disponible n’est pas suffisant pour créer la machine virtuelle : vous pouvez vérifier le quota disponible en accédant à Abonnement -> Utilisation + quotas. Vous pouvez ouvrir une [nouvelle demande de support](http://aka.ms/getazuresupport) pour augmenter le quota.

* Vous tentez de basculer des machines virtuelles de familles de taille différentes dans le même groupe à haute disponibilité. Veillez à choisir le même ordre de grandeur de taille pour toutes les machines virtuelles d’un même groupe à haute disponibilité. Changez la taille en accédant aux paramètres Calcul et réseau de la machine virtuelle, puis réessayez le basculement.

* Il existe une stratégie sur l’abonnement qui empêche la création d’une machine virtuelle. Modifiez la stratégie pour permettre la création d’une machine virtuelle, puis réessayez le basculement.

## <a name="failover-failed-with-error-id-28092"></a>Le basculement a échoué avec l’ID d’erreur 28092

Site Recovery n’a pas pu créer d’interface réseau pour la machine virtuelle basculée. Assurez-vous que vous avez un quota suffisant pour créer des interfaces réseau dans l’abonnement. Vous pouvez vérifier le quota disponible en accédant à Abonnement -> Utilisation + quotas. Vous pouvez ouvrir une [nouvelle demande de support](http://aka.ms/getazuresupport) pour augmenter le quota. Si vous disposez d’un quota suffisant, le problème peut être occasionnel, recommencez l’opération. Si le problème persiste même après plusieurs tentatives, laissez un commentaire à la fin de ce document.  

## <a name="failover-failed-with-error-id-70038"></a>Le basculement a échoué avec l’ID d’erreur 70038

Site Recovery n’a pas pu créer de machine virtuelle classique basculée dans Azure. Les raisons suivantes peuvent en être la cause :

* Une des ressources, comme un réseau virtuel, nécessaire à la création de la machine virtuelle n’existe pas. Créez le réseau virtuel, tel que proposé sous les paramètres Calcul et réseau de la machine virtuelle, ou modifiez le paramètre sur un réseau virtuel qui existe déjà et réessayez le basculement.


## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’aide supplémentaire, posez votre question sur le [Forum Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) ou laissez un commentaire à la fin de ce document. Notre communauté est active et doit être en mesure de vous aider.
