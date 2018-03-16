---
title: "Basculer et restaurer automatiquement des machines virtuelles et des serveurs physiques VMware répliqués sur Azure avec Site Recovery | Microsoft Docs"
description: "Découvrez comment basculer des machines virtuelles et des serveurs physiques VMware vers Azure, et comment restaurer automatiquement sur le site local avec Azure Site Recovery"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/05/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 618e2bb7ef124883af2d73264e01922ad2329d12
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2018
---
# <a name="fail-over-and-fail-back-vmware-vms-and-physical-servers-replicated-to-azure"></a>Basculer et restaurer automatiquement des machines virtuelles et des serveurs physiques VMware répliqués vers Azure

Ce didacticiel explique comment basculer une machine virtuelle VMware sur Azure. Une fois que vous avez procédé au basculement, vous restaurez automatiquement sur votre site local quand il est disponible. Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Vérifier la conformité des propriétés des machines virtuelles VMware aux spécifications d’Azure
> * Effectuer un basculement vers Azure
> * Créer un serveur de processus et un serveur cible maître pour la restauration automatique
> * Reprotéger des machines virtuelles Azure pour le site local
> * Basculer depuis Azure vers un site local
> * Reprotéger des machines virtuelles locales pour redémarrer la réplication vers Azure

Il s’agit du cinquième didacticiel d’une série. Ce didacticiel suppose que vous avez déjà effectué les tâches des didacticiels précédents.

1. [Préparer Azure](tutorial-prepare-azure.md)
2. [Préparer des machines virtuelles VMware locales](vmware-azure-tutorial-prepare-on-premises.md)
3. [Configurer une récupération d’urgence](vmware-azure-tutorial.md)
4. [Effectuer un test de récupération d’urgence](tutorial-dr-drill-azure.md)

## <a name="preparing-for-failover-and-failback"></a>Préparation pour le basculement et la restauration automatique

Vérifiez qu’il n’existe aucune capture instantanée sur la machine virtuelle. La machine virtuelle locale est désactivée au cours de la reprotection.
Cela permet de garantir la cohérence des données pendant la réplication. N’activez pas la machine virtuelle une fois la reprotection terminée. Utilisez le même serveur cible maître pour reprotéger un groupe de réplication. Si vous utilisez un autre serveur cible maître pour reprotéger un groupe de réplication, le serveur ne peut fournir aucun point dans le temps commun.

Le basculement et la restauration automatique comportent quatre étapes :

1. **Basculer vers Azure** : basculer les machines du site local vers Azure.
2. **Reprotéger des machines virtuelles Azure** : reprotégez les machines virtuelles Azure afin qu’elles soient répliquées sur les machines virtuelles VMware locales.
3. **Basculer sur un site local** : effectuez un basculement pour restaurer automatiquement depuis Azure.
4. **Reprotéger les machines virtuelles locales** : une fois les données restaurées automatiquement, reprotégez les machines virtuelles locales vers lesquelles vous avez restauré automatiquement pour qu’elles soient répliquées vers Azure.

## <a name="verify-vm-properties"></a>Vérifier les propriétés de la machine virtuelle

Vérifiez les propriétés de la machine virtuelle et que la machine virtuelle est conforme aux [conditions requises pour Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués** > Machine virtuelle.

2. Dans le volet **Élément répliqué**, vous voyez un récapitulatif des informations de la machine virtuelle, son état d’intégrité et ses derniers points de récupération disponibles. Cliquez sur **Propriétés** pour obtenir plus de détails.

3. Dans **Calcul et réseau**, vous pouvez modifier le nom Azure, le groupe de ressources, la taille cible, le [groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md) et les [paramètres de disque managé](#managed-disk-considerations).

4. Vous pouvez afficher et modifier les paramètres réseau, notamment le réseau/sous-réseau dans lequel la machine virtuelle Azure se trouvera après le basculement et l’adresse IP à lui affecter.

5. Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques** .

## <a name="run-a-failover-to-azure"></a>Effectuer un basculement vers Azure

1. Dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle > **Basculer**.

2. Dans **Basculer**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
   - **Dernier** (par défaut) : cette option traite d’abord toutes les données envoyées à Site Recovery. Elle fournit l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle Azure créée après le basculement a toutes les données qui ont été répliquées vers Site Recovery quand le basculement a été déclenché.
   - **Dernier point traité** : cette option bascule la machine virtuelle vers le dernier point de récupération traité par Site Recovery. Cette option fournit un objectif de délai de récupération (RTO) faible, car aucun temps n’est consacré à traiter les données non traitées.
   - **Dernier point de cohérence des applications** : cette option bascule la machine virtuelle vers le dernier point de récupération de cohérence des applications traité par Site Recovery.
   - **Personnalisé** : spécifiez un point de récupération.

3. Sélectionnez **Arrêter la machine avant de commencer le basculement** pour tenter d’arrêter les machines virtuelles sources avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt. Vous pouvez suivre la progression du basculement sur la page **Tâches**.

4. Si vous avez préparé la connexion à la machine virtuelle Azure, connectez-vous pour la vérifier après le basculement.

5. Après vérification, **validez** le basculement. Ceci supprime tous les points de récupération disponibles.

> [!WARNING]
> **N’annulez pas un basculement en cours** : avant que le basculement soit démarré, la réplication de la machine virtuelle est arrêtée.
> Si vous annulez un basculement en cours, le basculement s’arrête mais la machine virtuelle ne sera pas à nouveau répliquée.

Dans certains scénarios, le basculement nécessite un traitement supplémentaire qui dure environ huit à dix minutes. Vous constaterez peut-être des délais de basculement plus longs pour les serveurs physiques, les machines virtuelles VMware Linux, les machines virtuelles VMware pour lesquelles le service DHCP n’est pas activé, et les machines virtuelles VMware qui ne disposent pas des pilotes de démarrage suivants : storvsc, vmbus, storflt, intelide, atapi.

## <a name="create-a-process-server-in-azure"></a>Créer un serveur de processus dans Azure

Le serveur de processus reçoit des données de la machine virtuelle Azure et les envoie vers le site local. Un réseau à faible latence est nécessaire entre le serveur de processus et la machine virtuelle protégée.

- À des fins de test, si vous avez une connexion Azure ExpressRoute, vous pouvez utiliser le serveur de processus local qui est installé automatiquement sur le serveur de configuration.
- Si vous avez une connexion VPN ou si vous effectuez la restauration automatique dans un environnement de production, vous devez configurer une machine virtuelle Azure comme serveur de processus basé sur Azure pour la restauration automatique.
- Pour configurer un serveur de processus dans Azure, suivez les instructions de [cet article](vmware-azure-set-up-process-server-azure.md).

## <a name="configure-the-master-target-server"></a>Configurer le serveur cible maître

Par défaut, le serveur cible maître s’exécute sur le serveur de configuration local. Dans le cadre de ce didacticiel, nous utilisons le maître par défaut. Le serveur cible maître reçoit les données de la restauration automatique.

Si la machine virtuelle est sur un hôte ESXi qui est géré par un serveur vCenter, le serveur cible maître doit avoir accès au magasin de données de la machine virtuelle (VMDK) pour écrire les données répliquées sur les disques de la machine virtuelle. Vérifiez que le magasin de données de la machine virtuelle est monté sur l’hôte du serveur cible maître avec accès en lecture/écriture.

Si la machine virtuelle est sur un hôte ESXi qui n’est pas géré par un serveur vCenter, le service Site Recovery crée une machine virtuelle lors de la reprotection. La machine virtuelle est créée sur l’hôte ESX où vous créez le serveur cible maître.
Le disque dur de la machine virtuelle doit être dans un magasin de données accessible par l’hôte sur lequel le serveur cible maître s’exécute.

Si la machine virtuelle n’utilise pas vCenter, vous devez effectuer la détection de l’hôte sur lequel le serveur cible maître s’exécute avant de pouvoir reprotéger la machine. Ceci est vrai également pour la restauration automatique des serveurs physiques. Une autre option, si la machine virtuelle locale existe, consiste à la supprimer avant de procéder à une restauration automatique. L’opération de restauration automatique crée ensuite une machine virtuelle sur le même hôte que l’ordinateur hôte ESX cible maître. Si vous effectuez la restauration automatique vers un autre emplacement, les données sont récupérées dans la même banque de données et sur le même ordinateur hôte ESX que ceux qui sont utilisés par le serveur cible maître local.

Vous ne pouvez pas utiliser Storage vMotion sur le serveur cible maître. Si vous le faites, la restauration automatique ne fonctionne pas, car les disques ne sont pas disponibles pour cela. Excluez les serveurs cibles maîtres de votre liste vMotion.

## <a name="reprotect-azure-vms"></a>Reprotéger les machines virtuelles Azure

Cette procédure suppose que la machine virtuelle locale n’est pas disponible et que vous reprotégez à un autre emplacement.

1. Dans **Paramètres** > **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle qui a été basculée > **Reprotéger**.
2. Dans **Reprotéger**, vérifiez que **D’Azure à local** est sélectionné.
3. Spécifiez le serveur cible maître local et le serveur de processus.

4. Dans **Magasin de données**, sélectionnez le magasin de données où vous voulez récupérer les disques locaux. Utilisez cette option quand la machine virtuelle locale a été supprimée et que vous devez créer de nouveaux disques. Cette option est ignorée si les disques existent déjà, mais vous devez spécifier une valeur.
5. Sélectionnez le lecteur de conservation du serveur cible maître. La stratégie de restauration automatique est sélectionnée automatiquement.
6. Cliquez sur **OK** pour commencer l’opération de reprotection. Un travail est lancé pour répliquer la machine virtuelle à partir d’Azure vers le site local. Vous pouvez en suivre la progression sous l’onglet **Tâches** .

> [!NOTE]
> Si vous voulez récupérer la machine virtuelle Azure sur une machine virtuelle locale existante, montez le magasin de données de la machine virtuelle locale avec un accès en lecture/écriture sur l’hôte ESXi du serveur cible maître.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Effectuer un basculement depuis Azure vers le site local

Pour effectuer une réplication en retour vers votre site local, une stratégie de restauration automatique est utilisée. Cette stratégie a été créée automatiquement quand vous avez créé une stratégie de réplication pour la réplication vers Azure :

- La stratégie est automatiquement associée au serveur de configuration.
- La stratégie ne peut pas être modifiée.
- Les valeurs de la stratégie sont :
    - Seuil d’objectif de point de récupération : 15 minutes
    - Rétention de point de récupération : 24 heures
    - Fréquence des captures instantanées de cohérence d’application : 60 minutes

Effectuez le basculement en procédant comme suit :

1. Dans la page **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle > **Basculement non planifié**.
2. Dans **Confirmer le basculement**, vérifiez que le sens du basculement est depuis Azure.

3. Sélectionnez le point de récupération à utiliser pour le basculement. Un point de récupération de cohérence au niveau application se produit avant le dernier point dans le temps et entraîne une perte de données. Quand le basculement est effectué, Site Recovery arrête les machines virtuelles Azure et démarre la machine virtuelle locale. Il faut prévoir un temps d’indisponibilité : choisissez donc un moment approprié.
4. Cliquez avec le bouton droit sur la machine, puis cliquez sur **Valider**. Ceci déclenche une tâche qui supprime les machines virtuelles Azure.
5. Vérifiez que les machines virtuelles Azure ont été arrêtées comme attendu.


## <a name="reprotect-on-premises-machines-to-azure"></a>Reprotéger les machines locales sur Azure

Les données doivent maintenant être de retour sur votre site local, mais elles ne sont pas répliquées vers Azure. Vous pouvez redémarrer la réplication vers Azure comme suit :

1. Dans le coffre > **Paramètres**>**Éléments répliqués**, sélectionnez les machines virtuelles qui ont été restaurées automatiquement, puis cliquez sur **Reprotéger**.
2. Sélectionnez le serveur de processus qui est utilisé pour envoyer les données répliquées vers Azure, puis cliquez sur **OK**.

Une fois que la reprotection est terminée, la machine virtuelle est répliquée vers Azure et vous pouvez effectuer un basculement si nécessaire.
