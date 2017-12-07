---
title: Guide pratique pour restaurer depuis Azure vers VMware | Microsoft Docs
description: "Après leur restauration automatique dans Azure, vous pouvez restaurer des machines virtuelles en local. Pour effectuer la restauration automatique, procédez comme suit."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/28/2017
ms.author: ruturajd
ms.openlocfilehash: ad424818f41e6b48e754dd0d39771248a1cd04fb
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Restauration automatique d’Azure vers un site local

Cet article explique comment restaurer automatiquement des machines virtuelles Azure sur le site local. Suivez les instructions de cet article pour restaurer automatiquement vos machines virtuelles VMware ou vos serveurs physiques Windows/Linux après leur basculement du site local vers Azure, en suivant le didacticiel [Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).

> [!WARNING]
> Vous ne pouvez pas procéder à une restauration automatique après avoir [effectué une migration](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), déplacé une machine virtuelle vers un autre groupe de ressources ou supprimé la machine virtuelle Azure. Si vous désactivez la protection de la machine virtuelle, vous ne pouvez pas effectuer de restauration automatique.

> [!NOTE]
> Si vous avez effectué un basculement vers des machines virtuelles VMware, vous ne pouvez pas effectuer de restauration automatique vers un hôte Hyper-v.

## <a name="overview-of-failback"></a>Vue d’ensemble de la restauration automatique
Voici comment fonctionne la restauration automatique. Une fois que vous avez procédé au basculement vers Azure, vous pouvez effectuer une restauration automatique sur votre site local en quelques étapes :

1. [Reprotégez](site-recovery-how-to-reprotect.md) les machines virtuelles sur Azure afin qu’elles commencent à se répliquer sur les machines virtuelles VMware dans votre site local. Dans le cadre de ce processus, vous devez également :
    1. Configurer un serveur cible maître local : Windows pour les machines virtuelles Windows et [Linux](site-recovery-how-to-install-linux-master-target.md) pour les machines virtuelles Linux.
    2. Configurer un [serveur de processus](site-recovery-vmware-setup-azure-ps-resource-manager.md).
    3. Lancer la [reprotection](site-recovery-how-to-reprotect.md). Cette action éteint la machine virtuelle locale et synchronise les données de la machine virtuelle Azure avec les disques locaux.
5. Une fois que vos machines virtuelles Azure sont répliquées sur votre site local, déclenchez un basculement d’Azure vers le site local.

Une fois vos données restaurées automatiquement, reprotégez les machines virtuelles locales sur lesquelles vous avez effectué la restauration automatique pour qu’elles se répliquent vers Azure.

Pour une vue d’ensemble, regardez la vidéo suivante sur la procédure de basculement d’Azure vers un site local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

### <a name="fail-back-to-the-original-or-alternate-location"></a>Restaurer automatiquement sur l’emplacement d’origine ou un autre emplacement

Si vous avez basculé une machine virtuelle VMware, vous pouvez procéder à une restauration automatique sur la même machine virtuelle source si elle existe toujours. Dans ce scénario, seules les modifications sont restaurées automatiquement. Ce scénario est appelé restauration dans l’emplacement d’origine. Si la machine virtuelle locale n’existe pas, la solution consiste à utiliser un autre emplacement.

> [!NOTE]
> Vous pouvez uniquement procéder à une restauration automatique vers le serveur vCenter et le serveur de configuration d’origine. Vous ne pouvez pas déployer un nouveau serveur de configuration et procéder à une restauration automatique au moyen de celui-ci. En outre, vous ne pouvez pas ajouter un nouveau serveur vCenter au serveur de configuration existant et procéder à une restauration automatique vers le nouveau serveur vCenter.

#### <a name="original-location-recovery"></a>Récupération à l’emplacement d’origine

Si vous procédez à une restauration automatique vers la machine virtuelle d’origine, respectez les conditions suivantes :
* Si la machine virtuelle est gérée par un serveur vCenter, l’ordinateur hôte ESX du serveur maître cible doit avoir accès au magasin de données de la machine virtuelle.
* Si la machine virtuelle se trouve sur un ordinateur hôte ESX, mais n’est pas gérée par vCenter, le disque dur de la machine virtuelle doit se trouver dans un magasin de données auquel l’hôte de la cible principale a accès.
* Si votre machine virtuelle se trouve sur un ordinateur hôte ESX et n’utilise pas vCenter, vous devez exécuter la détection de l’hôte ESX du serveur maître cible avant d’effectuer la reprotection. Cela s’applique si vous restaurez automatiquement les serveurs physiques.
* Vous pouvez effectuer une restauration automatique sur un réseau de stockage virtuel (vSAN) ou un disque RDM (Raw Device Mapping), si les disques existent déjà et sont connectés à la machine virtuelle locale.

#### <a name="alternate-location-recovery"></a>Récupération à un autre emplacement
Si la machine virtuelle locale n’existe pas avant la reprotection, la solution consiste à utiliser un autre emplacement. Cette procédure recrée la machine virtuelle locale. Cela entraîne également le téléchargement complet de données.

* Lorsque vous effectuez la restauration automatique sur un autre emplacement, la machine virtuelle est restaurée sur le même hôte ESX que celui où le serveur cible est déployé. Le magasin de données utilisé pour créer le disque sera le même que celui sélectionné lors de la reprotection de la machine virtuelle.
* Vous ne pouvez effectuer la restauration automatique que dans une banque de données VMFS (Virtual Machine File System) ou vSAN. Si vous utilisez un disque RDM, la reprotection et la restauration automatique ne fonctionneront pas.
* La reprotection implique un transfert de données initial de grande taille, suivi par les modifications. Ce processus est préconisé lorsque la machine virtuelle n’existe pas sur le site. Toutes les données doivent être répliquées. Cette reprotection prendra également plus de temps que la récupération sur l’emplacement d’origine.
* Vous ne pouvez pas effectuer une restauration automatique sur des disques RDM. Seuls les nouveaux disques de machine virtuelle (VMDK) peuvent être créés dans une banque de données VMFS/vSAN.

Une machine physique basculée vers Azure ne peut être restaurée automatiquement que comme une machine virtuelle VMware (également appelée P2A2V). Ce flux s’inscrit dans le cadre de la récupération d’un autre emplacement.

* Un serveur physique Windows Server 2008 R2 SP1 protégé et basculé vers Azure ne peut pas être restauré automatiquement.
* Veillez à détecter au moins un serveur cible maître et les hôtes ESX/ESXi nécessaires sur lesquels vous devez effectuer une restauration automatique.

## <a name="have-you-completed-reprotection"></a>Vous avez terminé la reprotection ?
Avant toute chose, effectuez la reprotection afin que les machines virtuelles soient répliquées et que vous puissiez opérer une restauration automatique sur un site local. Pour plus d’informations, consultez [Reprotection d’Azure vers votre site local](site-recovery-how-to-reprotect.md).

## <a name="prerequisites"></a>Composants requis

> [!IMPORTANT]
> Pendant le basculement vers Azure, comme le site local risque de ne pas être accessible, le serveur de configuration peut être indisponible ou à l’arrêt. Le serveur de configuration local doit être en cours d’exécution et connecté au cours de la reprotection et de la restauration automatique.

* Un serveur de configuration est requis localement, lorsque vous effectuez une restauration automatique. Il doit être en cours d’exécution et connecté au service de telle sorte que son intégrité soit correcte. Pendant la restauration automatique, la machine virtuelle doit exister dans la base de données du serveur de configuration. Sinon, la restauration automatique échoue. Veillez donc à effectuer des sauvegardes régulières de votre serveur. En cas de problème, vous devez restaurer le serveur avec la même adresse IP pour que la restauration automatique fonctionne.
* Le serveur cible maître ne doit comporter aucun instantané avant le déclenchement de la reprotection/restauration automatique.

## <a name="steps-to-fail-back"></a>Procédure de restauration automatique

> [!IMPORTANT]
> Avant de lancer la restauration automatique, vous devez avoir finalisé la reprotection des machines virtuelles. Celles-ci doivent être protégées et leur intégrité doit être correcte (**OK**). Pour reprotéger les machines virtuelles, consultez la section sur la [reprotection](site-recovery-how-to-reprotect.md).

1. Dans la page des éléments répliqués, sélectionnez la machine virtuelle, cliquez dessus avec le bouton droit et sélectionnez **Unplanned Failover (Basculement imprévu)**.
2. Dans **Confirm Failover (Confirmer le basculement)**, vérifiez le sens du basculement (à partir d’Azure) et sélectionnez le point de récupération à utiliser pour le basculement (le plus récent ou le dernier point de cohérence d’application). Un point de cohérence d’application se situe derrière le point le plus récent et entraîne une perte de données.
3. Site Recovery arrête les machines virtuelles Azure pendant le basculement. Après avoir vérifié que la restauration automatique s’est terminée comme prévu, vous pouvez vous assurer que les machines virtuelles Azure ont été arrêtées.

### <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Sur quel point de récupération puis-je restaurer automatiquement les machines virtuelles ?

Lors de la restauration automatique, vous pouvez restaurer automatiquement la machine virtuelle ou le plan de récupération.

Si vous sélectionnez le point traité le plus récent, toutes les machines virtuelles sont basculées vers leur point le plus récent. Si le plan de récupération contient un groupe de réplication, chaque machine virtuelle de ce groupe bascule vers son point indépendant le plus récent.

Vous ne pouvez pas restaurer une machine virtuelle, tant qu’elle n’a pas un point de récupération. Vous ne pouvez pas restaurer automatiquement un plan de récupération, tant que toutes ses machines virtuelles n’ont pas au moins un point de récupération.

> [!NOTE]
> Le point de récupération le plus récent est un point de récupération cohérent en cas d’incident.

Si vous sélectionnez le point de récupération cohérent d’application, la restauration automatique d’une machine virtuelle s’effectue à son dernier point de récupération cohérent d’application disponible. Dans le cas d’un plan de récupération avec un groupe de réplication, chaque groupe de réplication est rétabli à son point de récupération disponible commun.
Notez que les points de récupération cohérents d’application peuvent se trouver dans le passé et qu’une perte de données est susceptible de se produire.

### <a name="what-happens-to-vmware-tools-post-failback"></a>Que se passe-t-il au niveau des outils VMware après une restauration automatique ?

Pendant le basculement vers Azure, les outils VMware ne peuvent pas s’exécuter sur la machine virtuelle Azure. Dans le cas d’une machine virtuelle Windows, ASR désactive les outils VMware pendant le basculement. Dans le cas d’une machine virtuelle Linux, ASR désinstalle les outils VMware pendant le basculement.

Lors du basculement de la machine virtuelle Windows, les outils VMware sont réactivés au moment de la restauration automatique. De même, pour une machine virtuelle Linux, les outils VMware sont réinstallés sur la machine pendant le basculement.

## <a name="next-steps"></a>Étapes suivantes

Une fois la restauration automatique terminée, vous devez valider la machine virtuelle pour garantir la suppression des machines virtuelles récupérées dans Azure.

### <a name="commit"></a>Validation
Une validation est requise pour supprimer la machine virtuelle basculée d’Azure.
Cliquez avec le bouton droit sur l’élément protégé, puis cliquez sur **Valider**. Un travail supprime les machines virtuelles basculées dans Azure.

### <a name="reprotect-from-on-premises-to-azure"></a>Procéder à une reprotection de l’environnement local vers Azure

Une fois la validation terminée, votre machine virtuelle se trouve de nouveau sur le site local, mais elle n’est pas protégée. Pour lancer une nouvelle réplication vers Azure, procédez comme suit :

1. Dans **Coffre** > **Paramètres** > **Éléments répliqués**, sélectionnez les machines virtuelles qui ont été restaurées automatiquement, puis cliquez sur **Reprotéger**.
2. Obtenez la valeur du serveur de processus à utiliser pour renvoyer les données à Azure.
3. Cliquez sur **OK** pour commencer la tâche de reprotection.

> [!NOTE]
> Après le démarrage d’une machine virtuelle locale, il faut un certain temps à l’agent pour se réinscrire auprès du serveur de configuration (jusqu’à 15 minutes). Pendant ce temps, la reprotection échoue et renvoie un message d’erreur indiquant que l’agent n’est pas installé. Patientez quelques minutes et essayez de relancer la reprotection.

Une fois le travail de reprotection terminé, la machine virtuelle est répliquée vers Azure et vous pouvez effectuer un basculement.

## <a name="common-issues"></a>Problèmes courants
Assurez-vous que le serveur vCenter est connecté avant de procéder à une restauration automatique. Sinon, la déconnexion des disques et leur attachement à la machine virtuelle échoueront.

### <a name="common-error-codes"></a>Codes d’erreur courants

#### <a name="error-code-8038"></a>Code d'erreur 8038

*Impossible d'afficher la machine virtuelle sur site en raison de l'erreur*

Cela se produit dans les situations suivantes 
1. La machine virtuelle sur site est exécutée sur un hôte qui n'a pas suffisamment de mémoire provisionnée.

Pour résoudre ce problème
1. Vous pouvez provisionner plus de mémoire sur l'hôte ESXi.
2. Déplacez la machine virtuelle vers un autre hôte ESXi disposant de suffisamment de mémoire pour démarrer la machine virtuelle.

