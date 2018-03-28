---
title: Restaurer depuis Azure vers VMware avec Azure Site Recovery | Microsoft Docs
description: Après le basculement des machines virtuelles dans Azure, vous pouvez démarrer une restauration automatique pour replacer les machines virtuelles en local. Pour effectuer la restauration automatique, procédez comme suit.
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 8f580fa40bade2bf586dd116729881b249bbba88
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="fail-back-from-azure-to-an-on-premises-site"></a>Restauration automatique d’Azure vers un site local

Cet article explique comment restaurer automatiquement des machines virtuelles à partir des machines virtuelles Azure vers un environnement VMware local. Suivez les instructions qu’il contient pour restaurer automatiquement vos machines virtuelles VMware ou vos serveurs physiques Windows/Linux après leur basculement du site local vers Azure en suivant le didacticiel [Basculement dans Azure Site Recovery](site-recovery-failover.md).

## <a name="prerequisites"></a>Prérequis

- Vérifiez que vous avez bien lu les détails sur les [différents types de restauration automatique](concepts-types-of-failback.md) et les avertissements correspondants.

> [!WARNING]
> Vous ne pouvez pas procéder à une restauration automatique après avoir [effectué une migration](migrate-overview.md#what-do-we-mean-by-migration), déplacé une machine virtuelle vers un autre groupe de ressources ou supprimé la machine virtuelle Azure. Si vous désactivez la protection de la machine virtuelle, vous ne pouvez pas effectuer de restauration automatique.

> [!WARNING]
> Un serveur physique Windows Server 2008 R2 SP1, protégé et basculé vers Azure, ne peut pas être restauré automatiquement.

> [!NOTE]
> Si vous avez effectué un basculement vers des machines virtuelles VMware, vous ne pouvez pas effectuer de restauration automatique vers un hôte Hyper-v.


- Avant toute chose, suivez les étapes de reprotection afin que les machines virtuelles soient répliquées et que vous puissiez opérer une restauration automatique sur un site local. Pour plus d’informations, consultez [Reprotection d’Azure vers votre site local](vmware-azure-reprotect.md).

- Assurez-vous que le serveur vCenter est connecté avant de procéder à une restauration automatique. Sinon, la déconnexion des disques et leur attachement à la machine virtuelle échouent.

- Pendant le basculement vers Azure, le site local risque de ne pas être accessible et le serveur de configuration peut être indisponible ou à l’arrêt. Le serveur de configuration local doit être en cours d’exécution et connecté au cours de la reprotection et de la restauration automatique. 

- Pendant la restauration automatique, la machine virtuelle doit exister dans la base de données du serveur de configuration. Sinon, la restauration automatique échoue. Veillez à effectuer des sauvegardes régulières de votre serveur. En cas d’urgence, vous devez restaurer le serveur avec l’adresse IP d’origine pour que la restauration automatique fonctionne.

- Le serveur cible maître ne doit comporter aucun instantané avant le déclenchement de la reprotection/restauration automatique.

## <a name="overview-of-failback"></a>Vue d’ensemble de la restauration automatique
Une fois que vous avez procédé au basculement vers Azure, vous pouvez effectuer une restauration automatique sur votre site local en quelques étapes :

1. [Reprotégez](vmware-azure-reprotect.md) les machines virtuelles sur Azure afin qu’elles commencent à se répliquer sur les machines virtuelles VMware dans votre site local. Dans le cadre de ce processus, vous devez également :

    * Configurez une cible principale locale. Utilisez une cible principale Windows pour les machines virtuelles Windows et une [cible principale Linux](vmware-azure-install-linux-master-target.md) pour les machines virtuelles Linux.
    * Configurer un [serveur de processus](vmware-azure-set-up-process-server-azure.md).
    * Démarrez [reprotection](vmware-azure-reprotect.md) pour désactiver la machine virtuelle locale et synchroniser les données de la machine virtuelle Azure avec les disques locaux.

2. Une fois que vos machines virtuelles Azure sont répliquées sur votre site local, démarrez un basculement d’Azure vers le site local.

3. Une fois vos données restaurées automatiquement, reprotégez les machines virtuelles locales pour que la réplication vers Azure démarre.

Pour une vue d’ensemble rapide, regardez la vidéo suivante sur la procédure de restauration automatique vers un site local :
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Procédure de restauration automatique

> [!IMPORTANT]
> Avant de commencer la restauration automatique, assurez-vous terminé la reprotection des machines virtuelles. Celles-ci doivent être protégées et leur intégrité doit être correcte (**OK**). Pour reprotéger les machines virtuelles, consultez [Comment reprotéger](vmware-azure-reprotect.md).

1. Dans la page des éléments répliqués, sélectionnez la machine virtuelle. Cliquez avec le bouton de droite dessus pour sélectionner **Basculement non planifié**.
2. Dans **Confirmer le basculement**, vérifiez la direction du basculement (depuis Azure). Puis, sélectionnez le point de récupération à utiliser pour le basculement (le plus récent ou le dernier point de cohérence d’application). Un point de cohérence d’application se situe derrière le point le plus récent et entraîne une perte de données.
3. Site Recovery arrête les machines virtuelles Azure pendant le basculement. Après avoir vérifié que la restauration automatique est terminée comme prévu, vous pouvez vous assurer que les machines virtuelles sur Azure sont arrêtées.
4. **Valider** est requis pour supprimer la machine virtuelle basculée depuis Azure. Cliquez avec le bouton de droite sur l’élément protégé, puis sélectionnez **Valider**. Un travail supprime les machines virtuelles basculées dans Azure.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Sur quel point de récupération puis-je restaurer automatiquement les machines virtuelles ?

Lors de la restauration automatique, vous pouvez restaurer automatiquement la machine virtuelle ou le plan de récupération.

- Si vous sélectionnez le point traité le plus récent à temps, toutes les machines virtuelles sont basculées vers leur point le plus récent à temps. Si le plan de récupération contient un groupe de réplication, chaque machine virtuelle du groupe de réplication bascule vers son point indépendant le plus récent à temps.

  Vous ne pouvez pas restaurer une machine virtuelle tant qu’elle n’a pas au moins un point de récupération. Vous ne pouvez pas restaurer automatiquement un plan de récupération tant que toutes ses machines virtuelles n’ont pas au moins un point de récupération.

  > [!NOTE]
  > Le point de récupération le plus récent est un point de récupération cohérent en cas d’incident.

- Si vous sélectionnez le point de récupération cohérent d’application, la restauration automatique d’une machine virtuelle unique s’effectue à son dernier point de récupération cohérent d’application disponible. Dans le cas d’un plan de récupération avec un groupe de réplication, chaque groupe de réplication est rétabli à son point de récupération disponible commun.
Les points de récupération cohérents d’application peuvent se trouver dans le passé et une perte de données est susceptible de se produire.

## <a name="what-happens-to-vmware-tools-post-failback"></a>Que se passe-t-il au niveau des outils VMware après une restauration automatique ?

Dans le cas d’une machine virtuelle Windows, Site Recovery désactive les outils VMware pendant le basculement. Lors de la restauration automatique de la machine virtuelle Windows, les outils VMware sont réactivés. 


## <a name="reprotect-from-on-premises-to-azure"></a>Procéder à une reprotection de l’environnement local vers Azure
Une fois la restauration automatique terminée et que l’action Valider est démarrée, les machines virtuelles récupérées dans Azure sont supprimées. La machine virtuelle se trouve de nouveau sur le site local, mais elle n’est pas protégée. Pour lancer une nouvelle réplication vers Azure, procédez comme suit :

1. Sélectionnez **Coffre** > **Paramètres** > **Éléments répliqués**, sélectionnez les machines virtuelles qui ont été restaurées automatiquement, puis sélectionnez **Reprotéger**.
2. Entrez la valeur du serveur de processus à utiliser pour renvoyer les données vers Azure.
3. Sélectionnez **OK** pour commencer le travail de reprotection.

> [!NOTE]
> Après le démarrage d’une machine virtuelle locale, il faut un certain temps à l’agent pour se réinscrire auprès du serveur de configuration (jusqu’à 15 minutes). Pendant ce temps, la reprotection échoue et renvoie un message d’erreur indiquant que l’agent n’est pas installé. Patientez quelques minutes et essayez de relancer la reprotection.

## <a name="next-steps"></a>Étapes suivantes

Une fois le travail de reprotection terminé, la machine virtuelle est de nouveau répliquée vers Azure et vous pouvez effectuer un [basculement](site-recovery-failover.md) pour déplacer à nouveau vos machines virtuelles vers Azure.


