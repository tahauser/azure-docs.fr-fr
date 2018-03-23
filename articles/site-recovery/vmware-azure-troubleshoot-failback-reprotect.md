---
title: Résoudre les problèmes liés à la restauration automatique des machines virtuelles VMware sur des machines VMware locales avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment utiliser Azure Site Recovery pour résoudre les erreurs courantes liées à la restauration automatique et à la reprotection lorsque vous restaurez automatiquement des machines virtuelles VMware sur Azure.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: rajanaki
ms.openlocfilehash: 6dcecce78de3caaefb40cb3fe4853d5d550163b4
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/12/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Résolution des problèmes liés à la restauration automatique entre Azure et VMware

Cet article explique comment résoudre les problèmes que vous pouvez rencontrer lorsque vous restaurez automatiquement des machines virtuelles Azure sur votre infrastructure VMware locale, après le basculement vers Azure avec [Azure Site Recovery](site-recovery-overview.md).

Une restauration automatique implique essentiellement deux étapes principales. Après le basculement, vous devez reprotéger les machines virtuelles Azure sur des machines locales pour qu’elles commencent à être répliquées. La deuxième étape consiste à exécuter un basculement depuis Azure pour effectuer une restauration automatique sur votre site local.

## <a name="troubleshoot-reprotection-errors"></a>Résoudre les erreurs liées à la reprotection

Cette section explique en détail comment résoudre les erreurs courantes liées à la reprotection.

### <a name="error-code-95226"></a>Code d'erreur 95226

**Échec de la reprotection, car la machine virtuelle Azure n’a pas pu contacter le serveur de configuration local.**

Cette erreur se produit quand :

1. La machine virtuelle Azure ne peut pas contacter le serveur de configuration local. La machine virtuelle ne peut pas être découverte ni inscrite auprès du serveur de configuration.
2. Le service InMage Scout Application n’est pas exécuté sur la machine virtuelle Azure après le basculement. Ce service est nécessaire pour les communications avec le serveur de configuration local.

Pour résoudre ce problème :

1. Vérifiez que le réseau de la machine virtuelle Azure permet à celle-ci de communiquer avec le serveur de configuration local. Pour ce faire, configurez un VPN de site à site dans votre centre de données local, ou configurez une connexion ExpressRoute avec un appairage privé sur le réseau virtuel de la machine virtuelle Azure.
2. Si la machine virtuelle peut communiquer avec le serveur de configuration local, ouvrez une session sur la machine virtuelle et vérifiez le service InMage Scout Application. S’il n’est pas en cours d’exécution, démarrez-le manuellement, puis vérifiez que le type de démarrage du service est défini sur Automatique.

### <a name="error-code-78052"></a>Code d'erreur 78052

**Impossible de terminer la protection de la machine virtuelle.**

Cela peut se produire si une machine virtuelle portant le même nom se trouve déjà sur le serveur cible maître sur lequel vous effectuez la restauration automatique.

Pour corriger ce problème, effectuez les étapes suivantes :
1. Sélectionnez un serveur cible maître situé sur un hôte différent. La reprotection va donc créer la machine sur un autre hôte, ce qui évitera les conflits de noms.
2. Vous pouvez également déplacer via vMotion le serveur cible maître sur un autre hôte où le conflit de noms ne se produira pas. Si la machine virtuelle existante est une machine isolée, renommez-la pour que la nouvelle machine virtuelle puisse être créée sur le même hôte ESXi.

### <a name="error-code-78093"></a>Code d'erreur 78093

**La machine virtuelle n’est pas en cours d’exécution, est dans un état suspendu ou n’est pas accessible.**

Pour reprotéger une machine virtuelle basculée, la machine virtuelle Azure doit être en cours d’exécution. De cette façon, le service Mobilité s’inscrit auprès du serveur de configuration local et peut commencer la réplication en communiquant avec le serveur de processus. Si la machine ne se trouve pas sur le bon réseau ou n’est pas en cours d’exécution (état suspendu ou arrêté), le serveur de configuration ne peut pas contacter le service Mobilité sur la machine virtuelle pour commencer la reprotection.

1. Redémarrez la machine virtuelle pour qu’elle puisse recommencer à communiquer localement.
2. Redémarrer le travail de reprotection après le démarrage de la machine virtuelle Azure

### <a name="error-code-8061"></a>Code d’erreur 8061

**La banque de données n’est pas accessible à partir de l’hôte ESXi**.

Pour procéder à la restauration automatique, vérifiez les [composants requis cibles maîtres et les banques de données prises en charge](vmware-azure-reprotect.md#deploy-a-separate-master-target-server).


## <a name="troubleshoot-failback-errors"></a>Résoudre les erreurs liées à la restauration automatique

Cette section décrit les erreurs courantes que vous pouvez rencontrer lors de la restauration automatique.

### <a name="error-code-8038"></a>Code d'erreur 8038

**Impossible d'afficher la machine virtuelle sur site en raison de l'erreur**

Cela se produit quand la machine virtuelle en local est exécutée sur un hôte dont le provisionnement en mémoire est insuffisant. Pour résoudre ce problème :

1. Provisionnez davantage de mémoire sur l’hôte ESXi.
2. De plus, vous pouvez déplacer la machine virtuelle avec vMotion vers un autre hôte ESXi disposant de suffisamment de mémoire pour démarrer la machine virtuelle.
