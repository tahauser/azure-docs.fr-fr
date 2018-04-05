---
title: Architecture de réplication VMware sur Azure avec Azure Site Recovery | Microsoft Docs
description: Cet article fournit une vue d’ensemble des composants et de l’architecture utilisés lors de la réplication de machines virtuelles VMware locales vers Azure, à l’aide d’Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2018
ms.author: raynew
ms.openlocfilehash: c1aa89f14edab7d0e560c20d6bc48480aff1631f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="vmware-to-azure-replication-architecture"></a>Architecture de la réplication VMware vers Azure

Cet article décrit l’architecture et les processus utilisés quand vous répliquez, basculez et récupérez des machines virtuelles VMware entre un site VMware local et Azure, en utilisant [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Composants architecturaux

Le tableau et le graphique suivants fournissent une vue d’ensemble des composants utilisés pour la réplication VMware vers Azure.

**Composant** | **Prérequis** | **Détails**
--- | --- | ---
**Microsoft Azure** | Un abonnement Azure, un compte de stockage Azure et un réseau Azure. | Les données répliquées à partir de machines virtuelles locales sont stockées dans le compte de stockage. Les machines virtuelles Azure sont créées avec les données répliquées quand vous exécutez un basculement depuis le site local vers Azure. Les machines virtuelles Azure se connectent au réseau virtuel Azure lors de leur création.
**Machine du serveur de configuration** | Une seule machine locale. Nous vous recommandons de l’exécuter en tant que machine virtuelle VMware pouvant être déployée à partir d’un modèle OVF téléchargé.<br/><br/> La machine exécute tous les composants locaux de Site Recovery, y compris le serveur de configuration, le serveur de processus et le serveur cible maître. | **Serveur de configuration** : coordonne la communication entre les ordinateurs locaux et Azure.et gère la réplication des données.<br/><br/> **Serveur de processus** : installé par défaut sur le serveur de configuration. Il reçoit les données de réplication, les optimise grâce à la mise en cache, la compression et le chiffrement, et les envoie vers le stockage Azure. De plus, le serveur de processus installe le service Mobilité d’Azure Site Recovery sur les machines virtuelles que vous voulez répliquer, et effectue une découverte automatique sur les machines locales. À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs de traitement distincts afin de gérer de plus grands volumes de trafic de réplication.<br/><br/> **Serveur cible maître** : Installé par défaut sur le serveur de configuration. Il gère les données de réplication pendant la restauration automatique à partir d’Azure. Pour les déploiements à grande échelle, vous pouvez ajouter un serveur cible maître distinct à des fins de restauration automatique.
**Serveurs VMware** | Les machines virtuelles VMware sont hébergées sur des serveurs vSphere ESXi locaux. Nous recommandons d’utiliser un serveur vCenter pour gérer les hôtes. | Pendant le déploiement de Site Recovery, vous ajoutez des serveurs VMware au coffre Recovery Services.
**Machines répliquées** | Le service Mobilité est installé sur chaque machine virtuelle VMware que vous répliquez. | Nous vous recommandons d’autoriser l’installation automatique à partir du serveur de processus. Vous pouvez également installer le service manuellement, ou utiliser une méthode de déploiement automatisée, telle que System Center Configuration Manager.

**Architecture VMware vers Azure**

![Composants](./media/vmware-azure-architecture/arch-enhanced.png)

## <a name="configuration-steps"></a>Configuration

Les grandes étapes pour configurer VMware pour la migration ou récupération d’urgence Azure sont les suivantes :

1. **Configurez les composants Azure**. Vous avez besoin d’un compte Azure avec les autorisations appropriées, d’un compte de stockage Azure, d’un réseau virtuel Azure et d’un coffre Recovery Services. [Plus d’informations](tutorial-prepare-azure.md)
2. **Configurez les composants locaux**. Cela comprend la configuration d’un compte sur le serveur VMware de façon que Site Recovery puisse détecter automatiquement les machines virtuelles à répliquer, la configuration d’un compte qui peut être utilisé pour installer le composant de service Mobilité sur les machines virtuelles que vous souhaitez répliquer et la vérification des machines virtuelles et serveurs VMware pour s’assurer qu’ils respectent les prérequis. Vous pouvez éventuellement vous préparer pour vous connecter à ces machines virtuelles Azure après le basculement. Site Recovery réplique les données de machines virtuelles dans un compte de stockage Azure, et crée des machines virtuelles Azure en utilisant les données lorsque vous exécutez un basculement vers Azure. [Plus d’informations](vmware-azure-tutorial-prepare-on-premises.md)
3. **Configurez la réplication**. Vous choisissez l’emplacement de la réplication. Vous configurez l’environnement de réplication source en configurant une machine virtuelle VMware locale unique (le serveur de configuration) qui exécute tous les composants Site Recovery locaux dont vous avez besoin. Après la configuration, vous inscrivez la machine du serveur de configuration dans le coffre Recovery Services. Ensuite, vous sélectionnez les paramètres de la cible. [Plus d’informations](vmware-azure-tutorial.md)
4. **Créez une stratégie de réplication**. Vous créez une stratégie de réplication qui spécifie comment la réplication doit se produire. 
    - **Seuil d’objectif de point de récupération** : ce paramètre de surveillance établit que si la réplication n’a pas lieu au cours de la période spécifiée, une alerte (et éventuellement un e-mail) est émise. Par exemple, si vous définissez le seuil d’objectif de point de récupération sur 30 minutes, et si un problème empêche la réplication de se produire pendant 30 minutes, un événement est généré. Ce paramètre n’affecte pas la réplication. La réplication est continue et des points de récupération sont créés toutes les quelques minutes.
    - **Rétention** : la rétention du point de récupération spécifie la durée de conservation des points de récupération dans Azure. Vous pouvez spécifier une valeur comprise entre 0 et 24 heures pour le stockage Premium, ou jusqu’à 72 heures pour le stockage Standard. Vous pouvez effectuer un basculement vers le point de récupération le plus récent ou vers un point stocké si vous avez défini une valeur supérieure à zéro. Après la période de rétention, les points de récupération sont vidés.
    - **Instantanés cohérents d’incident** : par défaut, Site Recovery prend des instantanés cohérents d’incident et crée des points de récupération associés toutes les quelques minutes. Un point de récupération est cohérent avec l’incident si tous les composants de données reliés entre eux sont cohérents au niveau de l’ordre d’écriture, comme ils l’étaient lors de la création du point de récupération. Pour mieux comprendre, imaginez l’état des données sur le disque dur de votre ordinateur après une panne d’alimentation ou un événement similaire. Un point de récupération cohérent d’incident est généralement suffisant si votre application est conçue pour récupérer d’un incident sans incohérence de données.
    - **Instantanés cohérents d’application** : si cette valeur n’est pas nulle, le service Mobilité s’exécutant sur la machine virtuelle tente de générer des instantanés cohérents de système de fichiers et des points de récupération. Le premier instantané est pris une fois la réplication initiale terminée. Ensuite, les instantanés sont pris à la fréquence que vous spécifiez. Un point de récupération est cohérent avec l’application si, en plus d’être cohérent au niveau de l’ordre d’écriture, les applications en cours d’exécution terminent toutes leurs opérations et vident leur mémoire tampon sur le disque (suspension de l’application). Les points de récupération cohérents d’application sont recommandés pour les applications de base de données telles que SQL, Oracle et Exchange. Si un instantané cohérent d’incident est suffisant, cette valeur peut être définie sur 0.  
    - **Cohérence avec plusieurs machines virtuelles** : vous pouvez éventuellement créer un groupe de réplication. Ensuite, lorsque vous activez la réplication, vous pouvez rassembler des machines virtuelles dans ce groupe. Les machines virtuelles d’un groupe de réplication sont répliquées ensemble et elles ont des points de récupération cohérents d’incident et d’application lorsqu’elles basculent. Restez vigilant si vous devez utiliser cette option, car elle peut affecter les performances de la charge de travail étant donné que des instantanés doivent être collectés sur plusieurs ordinateurs. Utilisez cette option uniquement si les machines virtuelles exécutent la même charge de travail et doivent être cohérentes, et si elles ont des activités identiques. Vous pouvez ajouter jusqu’à 8 machines virtuelles à un groupe. 
5. **Activez la réplication de machines virtuelles**. Enfin, vous activez la réplication pour vos machines virtuelles VMware locales. Si vous avez créé un compte pour installer le service Mobilité et spécifié que Site Recovery doit effectuer une installation push, alors le service Mobilité est installé sur chacune des machines virtuelles pour lesquelles vous activez la réplication. [Plus d’informations](vmware-azure-tutorial.md#enable-replication) Si vous avez créé un groupe de réplication pour la cohérence avec plusieurs machines virtuelles, vous pouvez ajouter des machines virtuelles à ce groupe.
6. **Testez le basculement**. Une fois que tout est configuré, vous pouvez effectuer un test de basculement pour vérifier que les machines virtuelles basculent vers Azure comme prévu. [Plus d’informations](tutorial-dr-drill-azure.md)
7. **Effectuez le basculement**. Si vous effectuez simplement une migration des machines virtuelles sur Azure, vous pouvez exécuter un basculement complet pour ce faire. Si vous configurez la récupération d’urgence, vous pouvez exécuter un basculement complet comme nécessaire. Pour une récupération d’urgence complète, après le basculement vers Azure, vous pouvez effectuer une restauration automatique sur votre site local quand il est disponible. [Plus d’informations](vmware-azure-tutorial-failover-failback.md)

## <a name="replication-process"></a>Processus de réplication

1. Lorsque vous activez la réplication pour une machine virtuelle, elle commence à répliquer conformément à la stratégie de réplication. 
2. Le trafic est répliqué sur des points de terminaison publics de stockage Azure via Internet. L’autre solution consiste à utiliser [l’homologation publique](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) Azure ExpressRoute. La réplication du trafic à partir d’un site local vers Azure via un réseau VPN de site à site n’est pas prise en charge.
3. Une copie initiale des données de machine virtuelle est répliquée vers le stockage Azure.
4. La réplication des modifications delta dans Azure commence à l’issue de la réplication initiale. Le suivi des modifications d’une machine est conservé dans un fichier .hrl.
5. La communication s’effectue comme suit :

    - Les machines virtuelles communiquent avec le serveur de configuration local sur le port HTTPS 443 entrant, pour la gestion de la réplication.
    - Le serveur de configuration orchestre la réplication avec Azure sur le port HTTPS 443 sortant.
    - Les machines virtuelles envoient des données de réplication au serveur de traitement (s’exécutant sur l’ordinateur du serveur de configuration) sur le port HTTPS 9443 entrant. Ce port peut être modifié.
    - Le serveur de traitement reçoit les données de réplication, les optimise et les chiffre, puis les envoie au stockage Azure via le port 443 sortant.


**Processus de réplication VMware vers Azure**

![Processus de réplication](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processus de basculement et de restauration automatique

Après avoir configuré la réplication et exécuté une simulation de reprise après sinistre (test de basculement) pour vérifier que tout fonctionne comme prévu, vous pouvez exécuter un basculement et une restauration automatique en fonction de vos besoins.

1. Vous pouvez basculer un seul ordinateur ou créer des plans de récupération pour basculer plusieurs machines virtuelles en même temps. Utiliser un plan de récupération plutôt que de basculer un seul ordinateur présente les avantages suivants :
    - Vous pouvez modéliser des dépendances d’application en incluant toutes les machines virtuelles sur l’application dans un seul plan de récupération.
    - Vous pouvez ajouter des scripts, des runbooks Azure et mettre en pause pour effectuer des actions manuelles.
2. Après avoir déclenché le basculement initial, validez-le pour accéder à la charge de travail à partir de la machine virtuelle Azure.
3. Lorsque votre site local principal est à nouveau disponible, vous pouvez vous préparer pour la restauration automatique. Pour effectuer la restauration automatique, vous devez configurer une infrastructure de restauration automatique, notamment les éléments suivants :

    * **Serveur de processus temporaire dans Azure** : pour effectuer une restauration automatique à partir d’Azure, configurez une machine virtuelle Azure faisant office de serveur de processus pour gérer la réplication à partir d’Azure. Vous pourrez supprimer cette machine virtuelle une fois la restauration terminée.
    * **Connexion VPN** : pour la restauration automatique, vous avez besoin d’une connexion VPN (ou ExpressRoute) du réseau Azure vers le site local.
    * **Serveur cible maître distinct** : par défaut, le serveur cible maître local qui a été installé avec le serveur de configuration, sur la machine virtuelle VMware locale, gère la restauration automatique. Si vous avez besoin de restaurer automatiquement de grands volumes de trafic, configurez un autre serveur cible maître local dédié.
    * **Stratégie de restauration automatique** : pour répliquer vers votre site local, vous avez besoin d’une stratégie de restauration automatique. Cette stratégie a été automatiquement créée au moment de la création de votre stratégie de réplication depuis le site local vers Azure.
4. Une fois les composants en place, la restauration automatique s’effectue en trois actions :

    - Étape 1 : Reprotégez les machines virtuelles Azure afin qu’elles répliquent depuis Azure vers les machines virtuelles VMware locales.
    -  Étape 2 : Exécutez un basculement vers le site local.
    - Étape 3 : Une fois les charges de travail automatiquement restaurées, vous réactivez la réplication pour les machines virtuelles locales.
    
 
**Restauration automatique VMware à partir d’Azure**

![Restauration automatique](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Étapes suivantes

Suivez [ce didacticiel](vmware-azure-tutorial.md) pour savoir comment activer la réplication VMware vers Azure.
