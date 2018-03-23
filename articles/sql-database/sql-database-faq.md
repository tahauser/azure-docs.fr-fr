---
title: Forum Aux Questions sur Azure SQL Database | Microsoft Docs
description: Réponses fréquemment posées sur les bases de données du cloud et Azure SQL Database, le système de gestion de base de données relationnelle Microsoft (SGBDR) et la base de données en tant que service dans cloud.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: reference
ms.topic: article
ms.date: 02/12/2018
ms.author: carlrab
ms.openlocfilehash: 59058b3445fdaf75df331d771afd28599491c52e
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="sql-database-faq"></a>Forum Aux Questions de base de données SQL

## <a name="what-is-the-current-version-of-sql-database"></a>Quelle est la version actuelle de la base de données SQL ?
La version actuelle de la base de données SQL est la version 12. La version 11 a été retirée.

## <a name="what-is-the-sla-for-sql-database"></a>Quel est le contrat de niveau de service (SLA) de la base de données SQL ?
Nous garantissons qu’au moins 99,99 % du temps, les clients disposeront d’une connectivité entre leur base de données Microsoft Azure SQL Database (niveaux De base, Standard ou Premium) unique ou élastique et notre passerelle Internet. Pour plus d’informations, consultez [Contrat de niveau de service](http://azure.microsoft.com/support/legal/sla/).

## <a name="how-do-i-reset-the-password-for-the-server-admin"></a>Comment réinitialiser le mot de passe de l’administrateur du serveur ?
Dans le [portail Azure](https://portal.azure.com), cliquez sur **Serveurs SQL**, sélectionnez le serveur dans la liste, puis cliquez sur **Réinitialiser le mot de passe**.

## <a name="how-do-i-manage-databases-and-logins"></a>Comment gérer les bases de données et les connexions ?
Consultez [Gestion des bases de données et des connexions](sql-database-manage-logins.md).

## <a name="how-do-i-make-sure-only-authorized-ip-addresses-are-allowed-to-access-a-server"></a>Comment s’assurer que seules les adresses IP autorisées peuvent accéder à un serveur ?
Voir [Configuration des paramètres de pare-feu sur une base de données SQL](sql-database-configure-firewall-settings.md).

## <a name="how-does-the-usage-of-sql-database-show-up-on-my-bill"></a>Comment l’utilisation de base de données SQL apparaît-elle sur ma facture ?
La base de données SQL effectue sa facturation en utilisant un taux horaire prédéterminé basé sur le niveau de service ainsi qu’un niveau de performance pour des bases de données uniques ou des eDTU par pool élastique. L’utilisation réelle est calculée au pro-rata horaire, et il se peut que votre facture affiche des fractions d’heure. Par exemple, si une base de données existe pendant 12 heures au cours d’un mois, votre facture affiche une demi-journée d’utilisation. En outre, les niveaux de service et le niveau de performances et le nombre d’eDTU par pool sont séparés dans la facture afin que vous puissiez connaître aisément le nombre de jours pendant lequel vous avez utilisé la base de données chaque mois.

## <a name="what-if-a-single-database-is-active-for-less-than-an-hour-or-uses-a-higher-service-tier-for-less-than-an-hour"></a>Que se passe-t-il si une base de données est active pendant moins d’une heure ou utilise un niveau de service plus élevé pendant moins d’une heure ?
Vous êtes facturé pour chaque heure d’existence de la base de données avec le niveau de service le plus élevé, le niveau de performance appliqué pendant cette heure quel que soit l’usage, ou si la base de données a été active pendant moins d’une heure. Par exemple, si vous avez créé une base de données unique et que vous l’avez supprimée cinq minutes après, votre facture mentionne le coût d’une heure de base de données. 

Exemples :

* Si vous créez une base de données de base, puis que vous la mettez à niveau immédiatement vers le niveau Standard S1, vous serez facturé au tarif Standard S1 pour la première heure.
* Si vous mettez à niveau une base de données De base vers la version Premium à 22 h 00 et que la mise à niveau se termine à 01 h 35 le jour suivant, vous serez facturé au tarif Premium à partir de 01 h 00. 
* Si vous passez d’une base de données Premium à une version De base à 11 h 00 et que le processus se termine à 14 h 15, la base de données sera facturée au tarif Premium jusqu’à 15 h 00, puis elle sera facturée au tarif De base.

## <a name="how-does-elastic-pool-usage-show-up-on-my-bill-and-what-happens-when-i-change-edtus-per-pool"></a>Comment l’utilisation du pool élastique s’affiche-il sur ma facture et que se passe-t-il lorsque je change le nombre d’eDTU par pool ?
Les frais de pool élastique s’affichent sur votre facture en tant que DTU élastiques (eDTU) dans les incréments indiqués sous eDTU par pool sur la [page de facturation](https://azure.microsoft.com/pricing/details/sql-database/). Il n’existe pas de facturation par base de données pour les pools élastiques. Vous êtes facturé pour chaque heure d’existence d’un pool selon l’eDTU la plus élevée indépendamment de l’utilisation ou si le pool a été actif de moins d’une heure. 

Exemples :

* Si vous créez un pool élastique standard avec 200 eDTU à 11 h 18, en ajoutant cinq bases de données au pool, vous êtes facturé pour 200 eDTU pendant une heure complète, à partir de 11 h 00 pour le reste de la journée.
* Le 2e jour, à 5 h 05, la base de données 1 commence à consommer 50 eDTU et reste stable toute la journée. Les bases de données 2-5 fluctuent entre 0 et 80 eDTU. Pendant la journée, vous ajoutez cinq autres bases de données qui utilisent des eDTU pendant la journée. Le jour 2 est un jour complet facturé à 200 eDTU. 
* Le troisième jour, à 5 h 00, vous ajoutez 15 autres bases de données. L’utilisation de la base de données augmente pendant la journée jusqu’au point où vous décidez d’augmenter le nombre d’eDTU du pool de 200 à 400 à 20 h 05. Les frais au niveau de 200 eDTU étaient en vigueur jusqu’à 8 h 00 et augmentent jusqu’à 400 eDTU pour les quatre heures restantes. 

## <a name="elastic-pool-billing-and-pricing-information"></a>Informations sur la tarification et la facturation du pool élastique
Les pools élastiques sont facturés en fonction des caractéristiques suivantes :

* Un pool élastique est facturé lors de sa création, même s’il n’inclut aucune base de données.
* Il est facturé toutes les heures. La fréquence de mesure est identique à celle des niveaux de performances associés aux bases de données uniques.
* Si un pool élastique est redimensionné en fonction d’un nouveau nombre d’eDTU, ce pool n’est pas facturé selon ce nouveau nombre, tant que l’opération de redimensionnement n’est pas terminée. Le processus est le même que lors de la modification du niveau de performances des bases de données uniques.
* Le prix d’un pool élastique est basé sur le nombre d’eDTU de ce pool. Le prix d’un pool élastique est indépendant du nombre et de l’utilisation des bases de données élastiques qu’il contient.
* Le prix est calculé comme suit : le nombre d’eDTUs d’un pool x le prix unitaire par eDTU.

Le prix unitaire des eDTU d’un pool élastique est supérieur au prix unitaire des DTU d’une base de données unique au sein du même niveau de service. Pour en savoir plus, voir [Tarification de la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/). 

Pour comprendre les eDTU et les niveaux de service, consultez [Options et performances de la base de données SQL](sql-database-service-tiers.md).

## <a name="how-does-the-use-of-active-geo-replication-in-an-elastic-pool-show-up-on-my-bill"></a>Comment l’utilisation de la géoréplication active dans un pool élastique apparaît-elle sur ma facture ?
Contrairement aux bases de données uniques, l’utilisation de la [géoréplication active](sql-database-geo-replication-overview.md) avec des bases de données élastiques n’a pas d’incidence directe sur la facturation.  Vous êtes facturé pour les eDTU configurées pour chaque pool (pool principal et pool secondaire)

## <a name="how-does-the-use-of-the-auditing-feature-impact-my-bill"></a>Quel sera l’impact de l’utilisation de la fonctionnalité d’audit sur ma facture ?
La fonctionnalité d’audit est intégrée au service Base de données SQL sans frais supplémentaires et est disponible pour les bases de données De base, Standard et Premium. Toutefois, pour stocker les journaux d’audit, la fonction d’audit utilise un compte Azure Storage et les tarifs des tables et files d’attente dans Azure Storage s’appliquent en fonction de la taille de votre journal d’audit.

## <a name="how-do-i-find-the-right-service-tier-and-performance-level-for-single-databases-and-elastic-pools"></a>Comment trouver le niveau de service adéquat et le niveau de performance adapté pour les pools élastiques et de bases de données uniques ?
Il existe quelques outils à votre disposition : 

* Pour les bases de données locales, utilisez le [conseil de dimensionnement DTU](http://dtucalculator.azurewebsites.net/), qui recommande les bases de données et les DTU requis, et évalue plusieurs bases de données pour les pools élastiques.
* Si une base de données unique tire avantage d’être au sein d’un pool, le moteur intelligent d’Azure recommande un pool élastique s’il détecte un schéma d’utilisation historique qui le justifie. Consultez [Surveiller et gérer un pool de bases de données élastique avec le portail Azure](sql-database-elastic-pool-manage-portal.md). Pour plus d’informations sur la façon d’effectuer des calculs vous-même, consultez [Considérations relatives au prix et aux performances d’un pool élastique](sql-database-elastic-pool.md)
* Pour voir si vous devez activer/désactiver une base de données unique, consultez [Guide des performances pour les bases de données uniques](sql-database-performance-guidance.md).

## <a name="how-often-can-i-change-the-service-tier-or-performance-level-of-a-single-database"></a>À quelle fréquence puis-je changer le niveau de service ou le niveau de performances d’une base de données unique ?
Vous pouvez modifier le niveau de service (entre De base, Standard et Premium) ou le niveau de performance au sein d’un niveau de service (par exemple, S1 à S2) aussi souvent que vous le souhaitez. Pour les versions de bases de données plus anciennes, vous pouvez modifier le niveau de service ou de performance jusqu’à quatre fois pendant une période de 24 heures.

## <a name="how-often-can-i-adjust-the-edtus-per-pool"></a>À quelle fréquence puis-je ajuster le nombre d’eDTU par pool ?
Aussi souvent que vous le souhaitez.

## <a name="how-long-does-it-take-to-change-the-service-tier-or-performance-level-of-a-single-database-or-move-a-database-in-and-out-of-an-elastic-pool"></a>Combien de temps faut-il pour modifier le niveau de service ou le niveau de performances d’une base de données ou pour déplacer une base de données dans ou hors du pool élastique ?
Le changement de niveau de service d’une base de données et le déplacement dans ou vers un pool exige que la base de données soit copiée sur la plateforme en tant qu’opération d’arrière-plan. Le changement de niveau de service peut prendre de quelques minutes à plusieurs heures en fonction de la taille de la base de données. Dans les deux cas, les bases de données restent en ligne et disponibles pendant le déplacement. Pour plus d’informations sur la modification de bases de données uniques, consultez [Modifier le niveau de service d’une base de données](sql-database-service-tiers.md). 

## <a name="when-should-i-use-a-single-database-vs-elastic-databases"></a>Quand dois-je choisir une base de données unique plutôt que des bases de données élastiques ?
En général, les pools élastiques sont conçus pour un [modèle d’application logiciel en tant que service (SaaS)](sql-database-design-patterns-multi-tenancy-saas-applications.md) standard, où il existe une base de données par client ou par locataire. L’achat de bases de données individuelles et le sur-approvisionnement destiné à répondre aux pics de demandes variables de chaque base de données ne sont généralement pas rentables. Avec les pools, vous gérez les performances collectives du pool, et les bases de données se mettent à l’échelle automatiquement. Le moteur intelligent d’Azure recommande un pool pour les bases de données quand un modèle d’utilisation l’exige. Pour plus d’informations, consultez [Conseils pour les pools élastiques](sql-database-elastic-pool.md).

## <a name="what-does-it-mean-to-have-up-to-200-of-your-maximum-provisioned-database-storage-for-backup-storage"></a>Qu’est-ce que cela signifie si je possède jusqu’à 200 % du stockage de base de données configuré maximal pour le stockage de sauvegarde ?
Le stockage de sauvegarde est le stockage associé à vos sauvegardes de bases de données automatisées qui sont utilisées pour la [restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) et la [géorestauration](sql-database-recovery-using-backups.md#geo-restore). Microsoft Azure SQL Database fournit jusqu’à 200 % du stockage de base de données maximal configuré pour le stockage de sauvegarde sans coût supplémentaire. Par exemple, si vous avez une instance de base de données Standard configurée à une taille de 250 Go, vous bénéficiez de 500 Go d’espace de stockage de sauvegarde sans coût supplémentaire. Si votre base de données dépasse l’espace de stockage de sauvegarde fourni, vous pouvez choisir de réduire la période de rétention en contactant le Support technique Azure ou en achetant l’espace de stockage supplémentaire facturé au tarif RA-GRS (Read-Access Geo Redundant Microsoft Azure) standard. Pour plus d’informations sur la facturation RA-GRS, consultez la page Détails de tarification de stockage.

## <a name="im-moving-from-webbusiness-to-the-new-service-tiers-what-do-i-need-to-know"></a>Je passe de services Web/Business à de nouveaux niveaux de service, que dois-je savoir ?
Les bases de données Web et Business SQL Azure sont désormais supprimées. Les niveaux De base, Standard, Premium et Élastique remplacent les bases de données Web et Business. 

## <a name="what-is-an-expected-replication-lag-when-geo-replicating-a-database-between-two-regions-within-the-same-azure-geography"></a>Quel décalage de réplication est attendu lors de la réplication géographique d’une base de données entre deux régions au sein de la même zone géographique Azure ?
Nous prenons actuellement en charge un RPO de cinq secondes et le décalage de réplication est inférieur à cette mesure quand la zone géographique secondaire est hébergée dans la région Azure associée recommandée et au même niveau de service.

## <a name="what-is-an-expected-replication-lag-when-geo-secondary-is-created-in-the-same-region-as-the-primary-database"></a>Quel est le décalage réplication attendu lorsque la zone géographique secondaire est créée dans la même région que la base de données primaire ?
En se basant sur des données empiriques, il y a peu de différence entre la réplication intra-région et inter-région si la région associée recommandée par Azure est utilisée. 

## <a name="if-there-is-a-network-failure-between-two-regions-how-does-the-retry-logic-work-when-geo-replication-is-set-up"></a>En cas de défaillance du réseau entre deux régions, comment la logique de nouvelle tentative fonctionne-t-elle lorsque la géoréplication est configurée ?
En cas de déconnexion, une nouvelle tentative de rétablissement des connexions est effectuée toutes les 10 secondes.

## <a name="what-can-i-do-to-guarantee-that-a-critical-change-on-the-primary-database-is-replicated"></a>Que puis-je faire pour garantir qu’une modification critique de la base de données primaire est répliquée ?
La zone géographique secondaire est un réplica asynchrone et nous n’essayons pas de conserver une synchronisation complète avec la zone principale. Mais nous fournissons une méthode pour forcer la synchronisation afin de garantir la réplication des modifications critiques (par exemple, les mises à jour du mot de passe). La synchronisation forcée a un impact sur les performances, car elle bloque le thread appelant jusqu’à ce que toutes les transactions validées soient répliquées. Pour plus d’informations, consultez [sp_wait_for_database_copy_sync](https://msdn.microsoft.com/library/dn467644.aspx). 

## <a name="what-tools-are-available-to-monitor-the-replication-lag-between-the-primary-database-and-geo-secondary"></a>Quels outils sont disponibles pour surveiller le décalage de réplication entre la base de données primaire et la zone géographique secondaire ?
Nous exposons le décalage de réplication en temps réel entre la base de données primaire et la zone géographique secondaire par le biais d’une vue de gestion dynamique (DMV). Pour plus d’informations, consultez [sys.dm_geo_replication_link_status](https://msdn.microsoft.com/library/mt575504.aspx).

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Pour déplacer une base de données sur un autre serveur dans le même abonnement
Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez une base de données dans la liste, puis cliquez sur **Copier**. Pour plus d’informations, consultez [Copier une base de données SQL Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Pour déplacer une base de données entre des abonnements
Dans le [portail Azure](https://portal.azure.com), cliquez sur **Serveurs SQL**, puis sélectionnez le serveur qui héberge votre base de données dans la liste. Cliquez sur **Déplacer**, puis sélectionnez les ressources à déplacer et l’abonnement cible.
