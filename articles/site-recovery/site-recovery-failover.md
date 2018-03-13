---
title: Basculement dans Site Recovery | Microsoft Docs
description: "Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques. Informez-vous sur le basculement dans Microsoft Azure ou un centre de données secondaire."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: pratshar
ms.openlocfilehash: afdab6e5ee5ae3bb8bc553afd93ff8f1ee18147f
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="failover-in-site-recovery"></a>Basculement via Microsoft Azure Site Recovery
Cet article explique comment basculer des machines virtuelles et des serveurs physiques protégés par Site Recovery.

## <a name="prerequisites"></a>configuration requise
1. Avant de procéder à un basculement, effectuez un [test de basculement](site-recovery-test-failover-to-azure.md) pour vous vérifier que tout fonctionne comme prévu.
1. Avant le basculement, [préparez le réseau](site-recovery-network-design.md) au niveau de l’emplacement cible.  

Utilisez le tableau suivant pour en savoir plus sur les options de basculement fournies par Azure Site Recovery. Ces options sont également répertoriées pour les différents scénarios de basculement.

| Scénario | Spécification de récupération d’application | Flux de travail pour Hyper-V | Flux de travail pour VMware
|---|--|--|--|
|Basculement planifié en raison d’un temps d’arrêt à venir du centre de données| Aucune perte de données pour l’application quand une activité planifiée est effectuée| Pour Hyper-V, ASR réplique les données à une fréquence de copie spécifiée par l’utilisateur. Le basculement planifié est utilisé pour remplacer la fréquence et répliquer les changements finaux avant le lancement d’un basculement. <br/> <br/> 1.    Planifiez une fenêtre de maintenance conformément aux processus de gestion des changements de votre entreprise. <br/><br/> 2. Informez les utilisateurs du temps d’arrêt à venir. <br/><br/> 3. Mettez l’application accessible aux utilisateurs hors connexion.<br/><br/>4. Lancez le basculement planifié à l’aide du portail ASR. La machine virtuelle locale est automatiquement arrêtée.<br/><br/>Perte de données d’application = 0 <br/><br/>Un journal des points de récupération est également fourni dans une fenêtre de rétention si l’utilisateur souhaite utiliser un point de récupération antérieur. (Rétention de 24 heures pour Hyper-V).| Pour VMware, ASR réplique les données en continu à l’aide de CDP. Le basculement permet à l’utilisateur de basculer vers les données les plus récentes (notamment après l’arrêt de l’application)<br/><br/> 1. Planifiez une fenêtre de maintenance conformément aux processus de gestion des changements. <br/><br/>2. Informez les utilisateurs du temps d’arrêt à venir. <br/><br/>3.    Mettez l’application accessible aux utilisateurs hors connexion. <br/><br/>4.  Lancez un basculement planifié à l’aide du portail ASR vers le dernier point après la mise hors connexion de l’application. Utilisez l’option « Basculement non planifié » sur le portail et sélectionnez le dernier point vers lequel basculer. La machine virtuelle locale est automatiquement arrêtée.<br/><br/>Perte de données d’application = 0 <br/><br/>Un journal des points de récupération est fourni dans une fenêtre de rétention si l’utilisateur souhaite utiliser un point de récupération antérieur. (72 heures de rétention pour VMware).
|Basculement en raison d’un temps d’arrêt non planifié du centre de données (naturel ou sinistre informatique) | Perte de données minimale pour l’application | 1. Lancez le plan BCP de l’organisation. <br/><br/>2. Lancez un basculement non planifié à l’aide du portail ASR vers le dernier point ou un point de la fenêtre de rétention (journal).| 1. Lancez le plan BCP de l’organisation. <br/><br/>2.  Lancez un basculement non planifié à l’aide du portail ASR vers le dernier point ou un point de la fenêtre de rétention (journal).


## <a name="run-a-failover"></a>Exécuter un basculement
Cette procédure explique comment exécuter un basculement dans le cadre d’un [plan de récupération](site-recovery-create-recovery-plans.md). Vous pouvez également exécuter le basculement d’une machine virtuelle ou d’un serveur physique unique à partir de la page **Éléments répliqués**.


![Basculement](./media/site-recovery-failover/Failover.png)

1. Sélectionnez **Plans de récupération** > *nom_planrécupération*. Cliquez sur **Basculement**.
2. Sur l’écran **Basculement**, sélectionnez un **point de récupération** vers lequel basculer. Vous pouvez utiliser l’une des options suivantes :
    1.  **Les dernières** (par défaut) : cette option permet de démarrer le travail en commençant par traiter toutes les données qui ont été envoyées au service de Site Recovery. Le traitement des données crée un point de récupération pour chaque machine virtuelle. Ce point de récupération est utilisé par la machine virtuelle pendant le basculement. Cette option offre l’objectif de point de récupération (RPO) le plus faible, car la machine virtuelle créée après le basculement dispose de toutes les données qui ont été répliquées vers le service Site Recovery lorsque le basculement a été déclenché.
    1.  **Dernier point dans le temps traité** : cette option permet de basculer toutes les machines virtuelles du plan de récupération vers le dernier point de récupération ayant déjà été traité par le service Site Recovery. Lorsque vous effectuez un test de basculement d’une machine virtuelle, la date et l’heure du dernier point de récupération traité sont également affichées. Si vous effectuez le basculement d’un plan de récupération, vous pouvez accéder à une machine virtuelle individuelle et consulter la vignette **Latest Recovery Points** (Derniers points de récupération) pour obtenir ces informations. Comme aucun temps n’est passé à traiter les données non traitées, cette option assure un basculement avec un objectif de délai récupération (RTO) faible.
    1.  **Dernier point dans le temps cohérent de l’application** : cette option permet de basculer toutes les machines virtuelles du plan de récupération vers le dernier point de récupération cohérent d’application ayant déjà été traité par le service Site Recovery. Lorsque vous effectuez un test de basculement d’une machine virtuelle, la date et l’heure du dernier point de récupération cohérent d’application sont également affichées. Si vous effectuez le basculement d’un plan de récupération, vous pouvez accéder à une machine virtuelle individuelle et consulter la vignette **Latest Recovery Points** (Derniers points de récupération) pour obtenir ces informations.
    1.  **Latest multi-VM processed** (Dernier point multimachine virtuelle traité) : cette option est disponible uniquement pour les plans de récupération qui incluent au moins une machine virtuelle avec la cohérence multimachine virtuelle activée. Les machines virtuelles appartenant à un groupe de réplication basculent vers le point de récupération multimachine virtuelle cohérent commun. Les autres machines virtuelles basculent vers leur dernier point de récupération traité.  
    1.  **Latest multi-VM app-consistent** (Dernier point d’application multimachine virtuelle cohérent) : cette option est disponible uniquement pour les plans de récupération qui incluent au moins une machine virtuelle avec la cohérence multimachine virtuelle activée. Les machines virtuelles appartenant à un groupe de réplication basculent vers le dernier point de récupération d’application multimachine virtuelle cohérent commun. Les autres machines virtuelles basculent vers leur dernier point de récupération d’application cohérent.
    1.  **Personnalisé** : si vous effectuez un test de basculement d’une machine virtuelle, vous pouvez utiliser cette option pour réaliser le basculement vers un point de récupération spécifique.

    > [!NOTE]
    > L’option permettant de choisir un point de récupération est disponible uniquement lorsque vous effectuez un basculement vers Azure.
    >
    >


1. Si une partie des machines virtuelles concernées par le plan de récupération a été basculée au cours d’une exécution précédente et que ces machines sont maintenant actives sur l’emplacement source et cible, vous pouvez utiliser l’option **Changer de direction** pour choisir la direction du basculement.
1. Si vous effectuez le basculement vers Azure et que la fonction de chiffrement des données est activée pour le cloud (s’applique uniquement lorsque les machines virtuelles Hyper-v sont protégées contre un serveur VMM), accédez à la zone **Clé de chiffrement** et sélectionnez le certificat émis lorsque vous avez activé le chiffrement des données pendant l’installation sur le serveur VMM.
1. Sélectionnez **Arrêter la machine avant de commencer le basculement** si vous souhaitez que Site Recovery tente d’arrêter les machines virtuelles source avant de déclencher le basculement. Le basculement est effectué même en cas d’échec de l’arrêt.  

    > [!NOTE]
    > Si les machines virtuelles Hyper-V sont protégées, l’option d’arrêt tente également de synchroniser les données locales qui n’ont pas encore été envoyées au service avant de déclencher le basculement.
    >
    >

1. Vous pouvez suivre la progression du basculement sur la page **Tâches**. Même si des erreurs se produisent lors d’un basculement non planifié, le plan de récupération s’exécute jusqu’à la fin.
1. Après le basculement, validez la machine virtuelle en vous y connectant. Si vous souhaitez basculer sur un autre point de récupération de la machine virtuelle, utilisez l’option **Changer le point de récupération**.
1. Si vous êtes satisfait de la machine vers laquelle est effectué le basculement, vous pouvez **Valider** le basculement. **La validation supprime tous les points de récupération disponibles avec le service**, et l’option **Changer le point de récupération** n’est plus disponible.

## <a name="planned-failover"></a>Basculement planifié
Les machines virtuelles/serveurs physiques protégés à l’aide de Site Recovery prennent également en charge le **basculement planifié**. Ce basculement planifié évite toute perte de données. Lorsqu’un basculement planifié se déclenche, les machines virtuelles sources sont arrêtées, les données les plus récentes sont synchronisées, puis un basculement est effectué.

> [!NOTE]
> Au cours du basculement des machines virtuelles Hyper-V d’un site local vers un autre site local, pour revenir au site local principal vous devez d’abord exécuter une **réplication inverse** de la machine virtuelle vers le site principal, puis déclencher un basculement. Si la machine virtuelle principale n’est pas disponible, avant de commencer la **réplication inversée** vous devez restaurer la machine virtuelle à partir d’une sauvegarde.   
>
>
## <a name="failover-job"></a>Travail de basculement

![Basculement](./media/site-recovery-failover/FailoverJob.png)

Le déclenchement d’un basculement implique les étapes suivantes :

1. Vérification des conditions requises : cette étape permet de vérifier que toutes les conditions requises pour le basculement sont satisfaites.
1. Basculement : cette étape consiste à traiter les données et à les préparer pour les utiliser pour la création d’une machine virtuelle Azure. Si vous avez choisi le **dernier** point de récupération, cette étape crée un point de récupération à partir des données envoyées au service.
1. Démarrer : cette étape crée une machine virtuelle Azure à l’aide des données traitées à l’étape précédente.

> [!WARNING]
> **N’annulez pas un basculement en cours** : avant le démarrage d’un basculement, la réplication de la machine virtuelle est arrêtée. Si vous **annulez** un travail en cours, le basculement s’arrête, mais la réplication de la machine virtuelle ne démarre pas. Il n’est plus possible ensuite de démarrer à nouveau la réplication.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Durée du basculement vers Azure

Dans certains cas, le basculement des machines virtuelles nécessite une étape supplémentaire intermédiaire qui dure généralement de 8 à 10 minutes. Dans les cas suivants, le temps de basculement sera plus élevé que prévu :

* Machines virtuelles VMware utilisant une version antérieure à la 9.8 pour le service de mobilité
* Serveurs physiques 
* Machines virtuelles VMware Linux
* Machines virtuelles Hyper-V protégées en tant que serveurs physiques
* Machines virtuelles où les pilotes suivants ne sont pas présents en tant que pilotes de démarrage 
    * storvsc 
    * vmbus 
    * storflt 
    * intelide 
    * atapi
* Machines virtuelles VMware qui n’ont pas de service DHCP activé, que vous utilisiez des adresses IP statiques ou DHCP

Dans tous les autres cas, cette étape intermédiaire n’est pas utile et le temps nécessaire au basculement est réduit. 





## <a name="using-scripts-in-failover"></a>Utilisation de scripts lors du basculement
Si vous le souhaitez, vous pouvez automatiser certaines actions pendant l’exécution d’un basculement. Pour ce faire, vous pouvez utiliser des scripts ou des [runbooks Azure Automation](site-recovery-runbook-automation.md) dans les [plans de récupération](site-recovery-create-recovery-plans.md).

## <a name="post-failover-considerations"></a>Points à prendre en compte après le basculement
Après le basculement, vous souhaiterez peut-être prendre en compte les recommandations suivantes :
### <a name="retaining-drive-letter-after-failover"></a>Conserver la lettre de lecteur après le basculement 
Pour conserver la lettre de lecteur sur les machines virtuelles après basculement, vous pouvez définir la **stratégie SAN** de la machine virtuelle sur **Activé**. [En savoir plus](https://support.microsoft.com/en-us/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).



## <a name="next-steps"></a>Étapes suivantes

> [!WARNING]
> Une fois le basculement des machines virtuelles effectué et le centre de données local disponible, vous devez [**à nouveau protéger**](site-recovery-how-to-reprotect.md) les machines virtuelles VMware dans le centre de données local.

Utilisez l’option [**Basculement planifié**](site-recovery-failback-from-azure-to-hyper-v.md) pour **restaurer automatiquement** les machines virtuelles Hyper-v en local à partir d’Azure.

Si vous avez basculé une machine virtuelle Hyper-v vers un autre centre de données local géré par un serveur VMM et que le centre de données principal est disponible, utilisez l’option **Réplication inverse** pour redémarrer la réplication vers le centre de données principal.
