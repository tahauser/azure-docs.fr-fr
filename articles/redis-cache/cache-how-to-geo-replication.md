---
title: "Comment configurer la géoréplication pour Cache Redis Azure | Microsoft Docs"
description: "Découvrez comment répliquer vos instances Cache Redis Azure dans des régions géographiques."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 375643dc-dbac-4bab-8004-d9ae9570440d
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: wesmc
ms.openlocfilehash: 883683f6af7943fa4da49095c9a15aefd5cfa719
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-geo-replication-for-azure-redis-cache"></a>Comment configurer la géoréplication pour Cache Redis Azure

La géoréplication fournit un mécanisme permettant de lier deux instances Cache Redis Azure de niveau Premium. Un cache est désigné comme cache lié principal et l’autre comme cache lié secondaire. Le cache lié secondaire est en lecture seule et les données écrites dans le cache principal sont répliquées vers le cache lié secondaire. Cette fonctionnalité peut être utilisée pour répliquer un cache entre des régions Azure. Cet article fournit un guide de configuration de la géoréplication pour vos instances Cache Redis Azure de niveau Premium.

## <a name="geo-replication-prerequisites"></a>Conditions préalables à la géoréplication

Pour configurer la géoréplication entre deux caches, les conditions préalables suivantes doivent être remplies :

- Les deux caches doivent être de [niveau Premium](cache-premium-tier-intro.md).
- Les deux caches doivent figurer dans le même abonnement Azure.
- Le niveau tarifaire du cache lié secondaire doit être égal ou supérieur à celui du cache lié principal.
- Si le clustering est activé sur le cache lié principal, il doit également l’être sur le cache lié secondaire, avec le même nombre de partitions que sur le cache lié principal.
- Les deux caches doivent être créés et en cours d’exécution.
- La persistance ne doit être activée sur aucun des caches.
- La géoréplication entre caches figurant dans un même réseau virtuel est prise en charge. La géoréplication entre caches figurant dans des réseaux virtuels différents est également prise en charge pour autant que les deux réseaux soient configurés de telle sorte que leurs ressources soient en mesure de s’atteindre mutuellement via des connexions TCP.

Une fois la géoréplication configurée, les restrictions suivantes s’appliquent à votre paire de caches liés :

- Le cache lié secondaire est en lecture seule. Il n’est pas possible d’y écrire des données. 
- Toutes les données présentes dans le cache lié secondaire avant l’ajout du lien sont supprimées. Toutefois, en cas de suppression de la géoréplication par la suite, les données répliquées restent dans le cache lié secondaire.
- Vous ne pouvez pas lancer une [opération de mise à l’échelle](cache-how-to-scale.md) sur un cache ou en [modifier le nombre de partitions](cache-how-to-premium-clustering.md) si le clustering est activé sur celui-ci.
- Vous ne pouvez activer la persistance sur aucun des caches.
- Vous pouvez [Exporter](cache-how-to-import-export-data.md#export) à partir de chaque cache, mais vous ne pouvez pas [Importer](cache-how-to-import-export-data.md#import) dans le cache lié principal.
- Vous ne pouvez pas supprimer les caches liés ou le groupe de ressources qui les contient aussi longtemps que le lien de géoréplication existe. Pour plus d’informations, consultez [Pourquoi ma tentative de suppression de mon cache lié a-t-elle échoué ?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Si les deux caches se trouvent dans des régions différentes, des frais de sortie de réseau s’appliquent aux données répliquées entre des régions sur le cache lié secondaire. Pour plus d’informations, consultez [Combien coûte la réplication de mes données entre des régions Azure ?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Aucun basculement automatique vers le cache lié secondaire n’a lieu si le cache lié principal (et son réplica) sont défaillants. Pour basculer des applications clientes, vous devez supprimer manuellement le lien de géoréplication et pointer les applications clientes sur l’ancien cache lié secondaire. Pour plus d’informations, consultez [Comment fonctionne le basculement vers le cache lié secondaire ?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Ajouter un lien de géoréplication

1. Pour lier deux caches Premium à des fins de géoréplication, dans le menu Ressource du cache prévu en tant que cache lié principal, cliquez sur **Géoréplication**, puis, dans le panneau **Géoréplication**, cliquez sur **Ajouter un lien de réplication du cache**.

    ![Ajouter un lien](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Dans la liste **Caches compatibles**, cliquez sur le nom du cache secondaire souhaité. Si le cache souhaité ne figure pas dans la liste, vérifiez que les [conditions préalables à la géoréplication](#geo-replication-prerequisites) pour le cache secondaire souhaité sont remplies. Pour filtrer les caches par région, cliquez sur la région souhaitée dans la carte pour afficher uniquement les caches figurant dans la liste **Caches compatibles**.

    ![Caches compatibles avec la géoréplication](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Vous pouvez également lancer le processus de liaison ou afficher des détails sur le cache secondaire à l’aide du menu contextuel.

    ![Menu contextuel de la géoréplication](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Cliquez sur **Lier** pour lier les deux caches et commencer le processus de réplication.

    ![Lier des caches](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Vous pouvez voir la progression du processus de réplication sur le panneau **Géoréplication**.

    ![État du lien](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Vous pouvez également voir l’état du lien dans le panneau **Vue d’ensemble** des caches principal et secondaire.

    ![État du cache](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Une fois le processus de réplication terminé, l’**État du lien** devient **Réussi**.

    ![État du cache](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Pendant le processus de liaison, le cache lié principal reste disponible, mais le cache lié secondaire ne l’est pas tant que le processus de liaison n’est pas terminé.

## <a name="remove-a-geo-replication-link"></a>Supprimer un lien de géoréplication

1. Pour supprimer le lien entre deux caches et arrêter la géoréplication, dans le panneau **Géoréplication**, cliquez sur **Dissocier les caches**.
    
    ![Dissocier les caches](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Une fois le processus de dissociation terminé, le cache secondaire est disponible tant en lecture qu’en écriture.

>[!NOTE]
>En cas de suppression du lien de géoréplication, les données répliquées à partir du cache lié principal restent dans le cache secondaire.
>
>

## <a name="geo-replication-faq"></a>FAQ concernant la géoréplication

- [Puis-je utiliser la géoréplication avec un cache de niveau Standard ou De base ?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Mon cache est-il disponible pendant le processus de liaison ou de dissociation ?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Puis-je lier plus de deux caches ?](#can-i-link-more-than-two-caches-together)
- [Puis-je lier deux caches d’abonnements Azure différents ?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Puis-je lier deux caches de tailles différentes ?](#can-i-link-two-caches-with-different-sizes)
- [Puis-je utiliser la géoréplication quand le clustering est activé ?](#can-i-use-geo-replication-with-clustering-enabled)
- [Puis-je utiliser la géoréplication avec mes caches dans un réseau virtuel ?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Quelle est la planification de réplication pour la géoréplication Redis ?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Quelle est la durée de réplication pour la géoréplication ?](#how-long-does-geo-replication-replication-take)
- [Y a-t-il un point de récupération de la réplication garanti ?](#is-the-replication-recovery-point-guaranteed)
- [Puis-je utiliser PowerShell ou Azure CLI pour gérer la géoréplication ?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Combien coûte la réplication de mes données entre des régions Azure ?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Pourquoi ma tentative de suppression de mon cache lié a-t-elle échoué ?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Quelle région dois-je utiliser pour mon cache lié secondaire ?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Comment fonctionne le basculement vers le cache lié secondaire ?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Puis-je utiliser la géoréplication avec un cache de niveau Standard ou De base ?

Non, la géoréplication est disponible uniquement pour des caches de niveau Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Mon cache est-il disponible pendant le processus de liaison ou de dissociation ?

- Lorsque vous liez deux caches à des fins de géoréplication, le cache lié principal reste disponible, mais le cache lié secondaire ne l’est pas tant que le processus de liaison n’est pas terminé.
- Lorsque vous supprimez le lien de géoréplication entre deux caches, ceux-ci restent disponibles.

### <a name="can-i-link-more-than-two-caches-together"></a>Puis-je lier plus de deux caches ?

Non, lors de l’utilisation de la géoréplication, vous ne pouvez pas lier plus de deux caches.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Puis-je lier deux caches d’abonnements Azure différents ?

Non, les deux caches doivent figurer dans le même abonnement Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Puis-je lier deux caches de tailles différentes ?

Oui, pour autant que le cache lié secondaire soit plus grand que le cache lié principal.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Puis-je utiliser la géoréplication quand le clustering est activé ?

Oui, pour autant que les deux caches aient le même nombre de partitions.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Puis-je utiliser la géoréplication avec mes caches dans un réseau virtuel ?

Oui, la géoréplication de caches dans des réseaux virtuels est prise en charge. 

- La géoréplication entre caches figurant dans un même réseau virtuel est prise en charge.
- La géoréplication entre caches figurant dans des réseaux virtuels différents est également prise en charge pour autant que les deux réseaux soient configurés de telle sorte que leurs ressources soient en mesure de s’atteindre mutuellement via des connexions TCP.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Quelle est la planification de réplication pour la géoréplication Redis ?

La réplication n’est pas effectuée selon une planification établie. Elle s’effectue en continu et de manière asynchrone, c’est-à-dire que toutes les écritures dans le cache principal sont instantanément répliquées de façon asynchrone dans le cache secondaire.

### <a name="how-long-does-geo-replication-replication-take"></a>Quelle est la durée de réplication pour la géoréplication ?

La réplication s’effectue en continu de manière incrémentielle et asynchrone. Sa durée est généralement proche de la latence entre les régions. Dans certaines circonstances, le cache secondaire a parfois besoin d’effectuer une synchronisation complète des données à partir du cache principal. Dans ce cas, la durée de la réplication dépend de plusieurs facteurs tels que la charge du cache principal, la bande passante disponible sur la machine du cache, la latence entre les régions, etc. Par exemple, d’après certains tests réalisés, la durée de réplication d’une paire géorépliquée complète de 53 Go entre les régions de l’Est et de l’Ouest des États-Unis se situe entre 5 et 10 minutes.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Y a-t-il un point de récupération de la réplication garanti ?

Actuellement, pour les caches en mode géorépliqué, la fonctionnalité de persistance et d’importation/exportation est désactivée. Si un basculement a été lancé par un client ou si un lien de réplication a été rompu entre la paire géorépliquée, le serveur secondaire conserve les données en mémoire qu’il a synchronisées à partir du cache principal jusqu’à ce point dans le temps. Il n’y a pas de point de récupération garanti dans ces situations.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Puis-je utiliser PowerShell ou Azure CLI pour gérer la géoréplication ?

Actuellement, vous pouvez gérer la géoréplication uniquement par le biais du portail Azure.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Combien coûte la réplication de mes données entre régions Azure ?

Lorsque vous utilisez la géoréplication, les données du cache lié principal sont répliquées vers le cache lié secondaire. Si les deux caches liés figurent dans la même région Azure, le transfert de données est gratuit. Si les deux caches liés se trouvent dans des régions Azure différentes, les frais de transfert des données de géoréplication correspondent au coût de la bande passante pour la réplication des données vers l’autre région Azure. Pour plus d'informations, consultez [Détails de la tarification de la bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Pourquoi ma tentative de suppression de mon cache lié a-t-elle échoué ?

Lorsque deux caches sont liés, vous ne pouvez pas supprimer les caches ou le groupe de ressources qui les contient aussi longtemps que le lien de géoréplication existe. Si vous tentez de supprimer le groupe de ressources contenant l’un des caches liés ou les deux, les autres ressources du groupe sont supprimées, mais le groupe de ressources reste dans l’état `deleting` et les caches liés dans le groupe de ressources restent dans l’état `running`. Pour achever la suppression du groupe de ressources et des caches liés qu’il contient, vous devez rompre le lien de géoréplication comme décrit dans [Supprimer un lien de géoréplication](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Quelle région dois-je utiliser pour mon cache lié secondaire ?

En règle générale, il est recommandé que votre cache existe dans la même région Azure que l’application qui y accède. Si votre application dispose d’une région primaire et d’une région de secours, vos caches principal et secondaire doivent exister dans ces mêmes régions. Pour plus d’informations sur les régions liées, consultez [Meilleures pratiques : régions Azure liées](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Comment fonctionne le basculement vers le cache lié secondaire ?

Dans la version initiale de la géoréplication, Cache Redis Azure ne prend pas en charge le basculement automatique entre régions Azure. La géoréplication est utilisée principalement dans un scénario de récupération d’urgence. Dans un scénario de récupération d’urgence, les clients doivent activer la pile d’applications entière dans une région de sauvegarde de manière coordonnée, plutôt que de laisser des composants d’application individuels décider du moment auquel basculer vers leurs sauvegardes. Cela est particulièrement pertinent pour Redis. L’un des principaux avantages de Redis est qu’il s’agit d’un magasin dont la latence est très basse. Si un Redis utilisé par une application bascule vers une autre région Azure sans que la couche de calcul suive, la durée ajoutée des boucles a un impact perceptible sur les performances. Nous souhaitons donc éviter que Redis bascule automatiquement en raison des problèmes temporaires de disponibilité que cela engendrerait.

Actuellement, pour lancer le basculement, vous devez supprimer le lien de géoréplication dans le portail Azure, puis modifier le point de terminaison de connexion dans le client Redis du cache lié principal au cache secondaire (précédemment lié). Une fois les deux caches dissociés, le réplica redevient un cache en lecture-écriture normal, et accepte directement les demandes des clients Redis.


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [Niveau Premium de Cache Redis Azure](cache-premium-tier-intro.md).

