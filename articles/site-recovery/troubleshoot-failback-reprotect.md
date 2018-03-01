---
title: "Résoudre des problèmes durant une restauration automatique locale à partir d’Azure, puis reprotéger dans Azure | Microsoft Docs"
description: "Cet article décrit comment résoudre des erreurs courantes survenant lors d’une restauration automatique locale à partir d’Azure et de la reprotection"
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/19/2017
ms.author: rajanaki
ms.openlocfilehash: d487c1fcf7fb6444c2b8489df839a6450715ae0a
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshoot-errors-reported-during-the-process-of-failback"></a>Résoudre les erreurs signalées pendant le processus de restauration automatique
Une restauration automatique implique essentiellement deux étapes principales. L’une consiste à reprotéger les machines virtuelles locales à partir d’Azure, et l’autre à effectuer réellement la restauration automatique à partir d’Azure vers le site local.

## <a name="troubleshoot-errors-when-reprotecting-a-virtual-machine-back-to-on-premises-after-failover"></a>Résoudre les erreurs lors de la reprotection d’une machine virtuelle restaurée localement après basculement
Vous pouvez rencontrer l’une les erreurs suivantes lorsque vous procédez à la reprotection d’une machine virtuelle sur Azure. Pour résoudre les problèmes, servez-vous des étapes décrites pour chaque condition d’erreur.


### <a name="error-code-95226"></a>Code d'erreur 95226

*Échec de la reprotection, car la machine virtuelle Azure n’a pas pu contacter le serveur de configuration local.*

Cela se produit dans les situations suivantes 
1. Comme la machine virtuelle Azure n’a pas pu contacter le serveur de configuration local, elle ne peut pas être découverte et inscrite sur le serveur de configuration. 
2. Le service InMage Scout Application sur la machine virtuelle Azure qui doit être en cours d’exécution pour communiquer avec le serveur de configuration local n’est peut-être plus exécuté après le basculement.

Pour résoudre ce problème
1. Vous devez vérifier que le réseau de la machine virtuelle Azure est configuré pour que la machine virtuelle puisse communiquer avec le serveur de configuration local. Pour ce faire, configurez un VPN de site à site dans votre centre de données local ou une connexion ExpressRoute avec un appairage privé sur le réseau virtuel de la machine virtuelle Azure. 
2. Si vous avez déjà configuré un réseau pour que la machine virtuelle Azure puisse communiquer avec le serveur de configuration local, connectez-vous à la machine virtuelle et vérifiez le « service InMage Scout Application ». Si le service InMage Scout Application n’est pas en cours d’exécution, démarrez-le manuellement et vérifiez que le type de démarrage du service est défini sur Automatique.

### <a name="error-code-78052"></a>Code d'erreur 78052
Échec de la reprotection avec le message d’erreur : *Impossible d’appliquer la protection à la machine virtuelle.*

Cela peut se produire pour deux raisons
1. La machine virtuelle que vous reprotégez est un serveur Windows Server 2016. Actuellement, ce système d’exploitation n’est pas pris en charge pour la restauration automatique, mais le sera bientôt.
2. Une machine virtuelle de même nom existe déjà sur le serveur cible maître sur lequel vous effectuez la restauration automatique.

Pour résoudre ce problème, vous pouvez sélectionner un autre serveur cible maître sur un hôte différent. La reprotection crée alors la machine sur l’autre hôte où les noms ne sont pas en conflit. Vous pouvez également déplacer via vMotion le serveur cible maître sur un autre hôte où le conflit de noms ne se produira pas. Si la machine virtuelle existante est isolée, vous pouvez simplement la renommer pour que la nouvelle machine virtuelle puisse être créée sur le même hôte ESXi.

### <a name="error-code-78093"></a>Code d'erreur 78093

*La machine virtuelle n’est pas en cours d’exécution, est dans un état suspendu ou n’est pas accessible.*

Pour reprotéger une machine virtuelle basculée sur un emplacement local, la machine virtuelle Azure doit être en cours d’exécution. De cette façon, le service Mobilité s’inscrit auprès du serveur de configuration local et peut commencer la réplication en communiquant avec le serveur de processus. Si la machine ne se trouve pas sur le bon réseau ou n’est pas en cours d’exécution (état suspendu ou arrêté), le serveur de configuration ne peut pas contacter le service Mobilité sur la machine virtuelle pour commencer la reprotection. Vous pouvez redémarrer la machine virtuelle pour qu’elle puisse recommencer à communiquer localement. Redémarrer le travail de reprotection après le démarrage de la machine virtuelle Azure

### <a name="error-code-8061"></a>Code d’erreur 8061

*La banque de données n’est pas accessible à partir de l’hôte ESXi*.

Consultez les sections relatives à la [configuration requise du serveur cible maître](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server) et aux [banques de données prises en charge](site-recovery-how-to-reprotect.md#what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback) pour procéder à la restauration automatique.


## <a name="troubleshoot-errors-when-performing-a-failback-of-an-azure-virtual-machine-back-to-on-premises"></a>Résoudre les erreurs lors de la restauration automatique d’une machine virtuelle Azure en local
Lors de la restauration automatique d’une machine virtuelle Azure en local, vous pouvez rencontrer l’une des erreurs suivantes. Pour résoudre les problèmes, servez-vous des étapes décrites pour chaque condition d’erreur.

### <a name="error-code-8038"></a>Code d'erreur 8038

*Impossible d'afficher la machine virtuelle sur site en raison de l'erreur*

Cela se produit quand la machine virtuelle en local est exécutée sur un hôte qui n’est pas suffisamment approvisionné en mémoire.

Pour résoudre ce problème

1. Vous pouvez provisionner plus de mémoire sur l'hôte ESXi.
1. Déplacez la machine virtuelle vers un autre hôte ESXi disposant de suffisamment de mémoire pour démarrer la machine virtuelle.