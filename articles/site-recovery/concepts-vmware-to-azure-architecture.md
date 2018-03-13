---
title: "Architecture de réplication VMware sur Azure avec Azure Site Recovery | Microsoft Docs"
description: "Cet article fournit une vue d’ensemble des composants et de l’architecture utilisés lors de la réplication de machines virtuelles VMware locales vers Azure, à l’aide d’Azure Site Recovery."
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2018
ms.author: raynew
ms.openlocfilehash: 4dd9d4f5f26d70f9130f48e2bf40ce71943a6c6d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2018
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

![Composants](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processus de réplication

1. Préparez des ressources Azure et des composants locaux.
2. Dans le coffre Recovery Services, spécifiez les paramètres de réplication source. Dans le cadre de ce processus, configurez le serveur de configuration local. Pour déployer ce serveur comme une machine virtuelle VMware, téléchargez un modèle OVF préparé, puis importez-le dans VMware pour créer la machine virtuelle.
3. Spécifiez les paramètres de réplication cible, créez une stratégie de réplication et activez la réplication pour vos machines virtuelles VMware.
4. Les machines sont répliquées conformément à la stratégie définie, et une copie initiale des données des machines virtuelles est répliquée dans le stockage Azure.
5. La réplication des modifications delta dans Azure commence à l’issue de la réplication initiale. Le suivi des modifications d’une machine est conservé dans un fichier .hrl.

    * Les machines communiquent avec le serveur de configuration sur le port HTTPS 443 entrant, pour la gestion de la réplication.

    * Les machines envoient des données de réplication au serveur de traitement sur le port HTTPS 9443 entrant (modification possible).

    * Le serveur de configuration orchestre la gestion de la réplication avec Azure sur le port HTTPS 443 sortant.

    * Le serveur de processus reçoit les données à partir des machines sources, puis les chiffre et les envoie au stockage Azure via le port 443 sortant.

    * Si vous activez la cohérence multimachine virtuelle, les machines du groupe de réplication communiquent entre elles sur le port 20004. Le mode multimachine virtuelle est utilisé si vous regroupez plusieurs machines dans des groupes de réplication qui partagent des points de récupération cohérents en cas d’incident et avec les applications lorsqu’ils basculent. Cette méthode est utile si les machines exécutent la même charge de travail et sont cohérentes.

6. Le trafic est répliqué sur des points de terminaison publics de stockage Azure via Internet. L’autre solution consiste à utiliser l’[homologation publique](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) Azure ExpressRoute. La réplication du trafic à partir d’un site local vers Azure via un réseau VPN de site à site n’est pas prise en charge.


**Processus de réplication VMware vers Azure**

![Processus de réplication](./media/concepts-vmware-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processus de basculement et de restauration automatique

Après avoir configuré la réplication et exécuté une simulation de récupération d’urgence (test de basculement) pour vérifier que tout fonctionne comme prévu, vous pouvez exécuter un basculement et une restauration automatique en fonction de vos besoins.

1. Vous pouvez basculer une seule machine ou créer des plans de récupération pour basculer plusieurs machines virtuelles.

2. Quand vous exécutez un basculement, les machines virtuelles Azure sont créées à partir des données répliquées dans le stockage Azure.

3. Après avoir déclenché le basculement initial, validez-le pour accéder à la charge de travail à partir de la machine virtuelle Azure.

Lorsque votre site local principal est à nouveau disponible, vous pouvez lancer la restauration automatique.
1. Vous devez configurer une infrastructure de restauration automatique, notamment les aspects suivants :

    * **Serveur de processus temporaire dans Azure** : pour effectuer une restauration automatique à partir d’Azure, configurez une machine virtuelle Azure comme un serveur de processus pour gérer la réplication à partir d’Azure. Vous pourrez supprimer cette machine virtuelle une fois la restauration terminée.

    * **Connexion VPN** : pour la restauration automatique, vous avez besoin d’une connexion VPN (ou ExpressRoute) du réseau Azure vers le site local.

    * **Serveur cible maître distinct** : par défaut, le serveur cible maître local qui a été installé avec le serveur de configuration, sur la machine virtuelle VMware locale, gère la restauration automatique. Si vous avez besoin de restaurer automatiquement de grands volumes de trafic, configurez un autre serveur cible maître local dédié.

    * **Stratégie de restauration automatique** : pour répliquer vers votre site local, vous avez besoin d’une stratégie de restauration automatique. Cette stratégie a été automatiquement créée au moment de la création de votre stratégie de réplication depuis le site local vers Azure.
2. Une fois les composants en place, la restauration automatique s’effectue en trois étapes :

    a. Étape 1 : Reprotégez les machines virtuelles Azure afin qu’elles répliquent depuis Azure vers les machines virtuelles VMware locales.

    b. Étape 2 : Exécutez un basculement vers le site local.

    c. Étape 3 : Une fois les charges de travail automatiquement restaurées, vous réactivez la réplication pour les machines virtuelles locales.

**Restauration automatique VMware à partir d’Azure**

![Restauration automatique](./media/concepts-vmware-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Étapes suivantes

Suivez [ce didacticiel](tutorial-vmware-to-azure.md) pour savoir comment activer la réplication VMware vers Azure.
