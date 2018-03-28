---
title: Résoudre les problèmes liés à la restauration automatique des machines virtuelles VMware sur des machines VMware locales avec Azure Site Recovery | Microsoft Docs
description: Cet article explique comment résoudre les problèmes des restaurations automatiques communes et les erreurs de reprotection lorsque vous restaurez automatiquement des machines virtuelles de VMware à Azure à l’aide d’Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/09/2018
ms.author: rajanaki
ms.openlocfilehash: 480c3524ad4fb8a8c6ea02f09b8d27f254da9b08
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="troubleshoot-failback-from-azure-to-vmware"></a>Résolution des problèmes liés à la restauration automatique entre Azure et VMware

Cet article explique comment résoudre les problèmes que vous pouvez rencontrer lorsque vous restaurez automatiquement des machines virtuelles Azure sur votre infrastructure VMware locale, après le basculement vers Azure à l’aide de [Azure Site Recovery](site-recovery-overview.md).

Une restauration automatique implique essentiellement deux étapes principales. Pour la première étape, après le basculement, vous devez reprotéger les machines virtuelles Azure sur des machines locales pour qu’elles démarrent la réplication. La deuxième étape consiste à exécuter un basculement depuis Azure pour effectuer une restauration automatique sur votre site local.

## <a name="troubleshoot-reprotection-errors"></a>Résoudre les erreurs liées à la reprotection

Cette section explique en détail les erreurs de reprotection courantes et comment les résoudre.

### <a name="error-code-95226"></a>Code d'erreur 95226

**Échec de la reprotection, car la machine virtuelle Azure n’a pas pu contacter le serveur de configuration local.**

Cette erreur se produit quand :

* La machine virtuelle Azure ne peut pas contacter le serveur de configuration local. La machine virtuelle ne peut pas être découverte ni inscrite auprès du serveur de configuration.
* Le service d’application InMage Scout n’est pas exécuté sur la machine virtuelle Azure après le basculement. Ce service est nécessaire pour les communications avec le serveur de configuration local.

Pour résoudre ce problème :

* Vérifiez que le réseau de la machine virtuelle Azure permet à celle-ci de communiquer avec le serveur de configuration local. Vous pouvez soit configurer un VPN de site à site dans votre centre de données local soit configurer une connexion ExpressRoute Azure avec une homologation privée sur le réseau virtuel de la machine virtuelle Azure.
* Si la machine virtuelle peut communiquer avec le serveur de configuration local, connectez-vous à la machine virtuelle. Vérifiez ensuite le service d’application InMage Scout. Si vous voyez qu’il ne s’exécute pas, démarrez le service manuellement. Vérifiez que le type de démarrage du service est défini sur **Automatique**.

### <a name="error-code-78052"></a>Code d'erreur 78052

**Impossible de terminer la protection de la machine virtuelle.**

Ce problème peut se produire si une machine virtuelle portant le même nom se trouve déjà sur le serveur cible principal sur lequel vous effectuez la restauration automatique.

Pour résoudre ce problème :

* Sélectionnez un serveur cible principal situé sur un hôte différent. La reprotection va donc créer la machine sur un autre hôte, ce qui évitera les conflits de noms.
* Vous pouvez également utiliser vMotion pour déplacer la cible principale vers un autre hôte où le conflit de noms ne se produira pas. Si la machine virtuelle existante est une machine isolée, renommez-la pour que la nouvelle machine virtuelle puisse être créée sur le même hôte ESXi.


### <a name="error-code-78093"></a>Code d'erreur 78093

**La machine virtuelle n’est pas en cours d’exécution, est dans un état suspendu ou n’est pas accessible.**

Pour résoudre ce problème :

Pour reprotéger une machine virtuelle basculée, la machine virtuelle Azure doit être exécutée de sorte que le service Mobilité s’inscrive auprès du serveur de configuration local et puisse démarrer la réplication en communiquant avec le serveur de processus. Si la machine ne se trouve pas sur le bon réseau ou n’est pas en cours d’exécution (état suspendu ou arrêtée), le serveur de configuration ne peut pas contacter le service Mobilité sur la machine virtuelle pour démarrer la reprotection.

* Redémarrez la machine virtuelle pour qu’elle puisse recommencer à communiquer localement.
* Redémarrez le travail de reprotection après le démarrage de la machine virtuelle Azure.

### <a name="error-code-8061"></a>Code d’erreur 8061

**La banque de données n’est pas accessible à partir de l’hôte ESXi**.

Pour procéder à la restauration automatique, vérifiez les [composants requis cibles maîtres et les banques de données prises en charge](vmware-azure-reprotect.md#deploy-a-separate-master-target-server).


## <a name="troubleshoot-failback-errors"></a>Résoudre les erreurs liées à la restauration automatique

Cette section décrit les erreurs courantes que vous pouvez rencontrer lors de la restauration automatique.

### <a name="error-code-8038"></a>Code d'erreur 8038

**Impossible d'afficher la machine virtuelle locale en raison de l'erreur**

Ce problème se produit quand la machine virtuelle locale est exécutée sur un hôte dont le provisionnement en mémoire est insuffisant. 

Pour résoudre ce problème :

* Provisionnez davantage de mémoire sur l’hôte ESXi.
* De plus, vous pouvez utiliser vMotion pour déplacer la machine virtuelle vers un autre hôte ESXi disposant de suffisamment de mémoire pour démarrer la machine virtuelle.
