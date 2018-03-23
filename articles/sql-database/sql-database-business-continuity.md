---
title: Continuité des activités cloud - récupération de base de données - SQL Database | Microsoft Docs
description: Découvrez comment Azure SQL Database prend en charge la continuité des activités cloud et la récupération de base de données et vous aide à maintenir les applications cloud opérationnelles.
keywords: continuité des activités, continuité des activités cloud, récupération d’urgence de base de données, récupération de base de données
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 08/25/2017
ms.author: sashan
ms.openlocfilehash: 160e65130efc78bc1a98a0feceb1c824cf226156
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="overview-of-business-continuity-with-azure-sql-database"></a>Vue d’ensemble de la continuité de l’activité avec la base de données Azure SQL

Cette vue d’ensemble décrit les fonctionnalités de la base de données SQL Azure en matière de continuité d’activité et de récupération d’urgence. Découvrez les options, les recommandations et les didacticiels pour la récupération à partir d’événements d’interruption qui pourraient entraîner une perte de données ou l’indisponibilité de votre base de données et de votre application. Connaissez la procédure à suivre lorsqu’un utilisateur ou qu’une erreur d’application affecte l’intégrité des données, lorsqu’une région Azure subit une panne ou que votre application nécessite une maintenance.

## <a name="sql-database-features-that-you-can-use-to-provide-business-continuity"></a>Fonctionnalités de la base de données SQL que vous pouvez utiliser pour garantir une continuité d’activité

La base de données SQL fournit plusieurs fonctionnalités de continuité d’activité, notamment des sauvegardes automatisées et une réplication facultative de la base de données. Chacune de ces fonctionnalités possède des caractéristiques spécifiques concernant le temps de récupération estimé (ERT) et le risque de perte de données pour les transactions récentes. Une fois que vous avez compris ces options, vous pouvez choisir celles qui vous conviennent et, dans la plupart des scénarios, les utiliser ensemble dans différents scénarios. Lorsque vous élaborez votre plan de continuité d’activité, vous devez comprendre le délai maximal acceptable nécessaire à la récupération complète de l’application après l’événement d’interruption : votre objectif de délai de récupération (RTO). Vous devez également comprendre la quantité maximale des récentes mises à jour de données (intervalle) que l’application peut accepter de perdre lors de la récupération après l’événement d’interruption, il s’agit de votre objectif de point de récupération (RPO).

Le tableau suivant compare l’ERT et le RPO pour les trois scénarios les plus courants.

| Fonctionnalité | Niveau de base | Niveau standard | Niveau Premium |
| --- | --- | --- | --- |
| Limite de restauration dans le temps à partir de la sauvegarde |Tout point de restauration dans un délai de 7 jours |Tout point de restauration dans un délai de 35 jours |Tout point de restauration dans un délai de 35 jours |
| Géo-restauration à partir de sauvegardes répliquées géographiquement |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h |ERT < 12 h, RPO < 1 h |
| Restauration à partir d’Azure Backup Vault |ERT < 12 h, RPO < 1 sem. |ERT < 12 h, RPO < 1 sem. |ERT < 12 h, RPO < 1 sem. |
| Géo-réplication active |ERT < 30s, RPO < 5s |ERT < 30s, RPO < 5s |ERT < 30s, RPO < 5s |

### <a name="use-database-backups-to-recover-a-database"></a>Utiliser des sauvegardes de base de données pour récupérer une base de données

SQL Database effectue automatiquement une combinaison de sauvegardes de bases de données complètes (toutes les semaines), de sauvegardes de bases de données différentielles (toutes les heures), et de sauvegardes de journaux de transactions (toutes les cinq à dix minutes) pour protéger votre entreprise contre la perte de données. Ces sauvegardes sont stockées dans le stockage géo-redondant pendant 35 jours pour les bases de données associées aux niveaux de service Standard et Premium, et pendant 7 jours pour les bases de données dont le niveau de service est De base. Pour en savoir plus, consultez les [niveaux de service](sql-database-service-tiers.md). Si la période de rétention de votre niveau de service ne répond pas aux besoins de votre entreprise, vous pouvez augmenter la période de rétention en [modifiant le niveau de service](sql-database-service-tiers.md). Les sauvegardes complètes et différentielles de bases de données sont également répliquées vers un [centre de données jumelé](../best-practices-availability-paired-regions.md) pour une protection contre une panne du centre de données . Pour plus d’informations, consultez la rubrique concernant les [sauvegardes de base de données automatiques](sql-database-automated-backups.md).

Si la période de rétention intégrée n’est pas suffisante pour votre application, vous pouvez la rallonger en configurant une stratégie de rétention à long terme. Pour plus d’informations, consultez [rétention à long terme](sql-database-long-term-retention.md).

Vous pouvez utiliser ces sauvegardes automatiques pour récupérer une base de données après divers événements d’interruption, à la fois dans votre propre centre de données et dans un autre centre de données. Lorsque vous utilisez des sauvegardes automatiques, le délai estimé de récupération dépend de plusieurs facteurs, notamment du nombre total de bases de données à récupérer dans la même région au même moment, de la taille des bases de données, de la taille du journal des transactions et de la bande passante réseau. Le délai de récupération est généralement inférieur à 12 heures. Lorsque vous effectuez une récupération vers une autre région de données, le risque de perte de données est limité à 1 heure par le stockage géo-redondant des sauvegardes de bases de données différentielle (toutes les heures).

> [!IMPORTANT]
> Pour effectuer une récupération à l’aide de sauvegardes automatisées, vous devez être membre du rôle Contributeur de SQL Server ou être le propriétaire de l’abonnement (voir [RBAC : rôles intégrés](../active-directory/role-based-access-built-in-roles.md)). Vous pouvez effectuer une récupération en utilisant le portail Azure, PowerShell ou l’API REST. Vous ne pouvez pas utiliser Transact-SQL.
>

Utilisez des sauvegardes automatisées comme mécanisme de continuité d’activité et de récupération si votre application :

* N'est pas essentielle.
* N’a pas de contrat SLA contraignant, une interruption de service de 24 heures ou plus n’entraîne pas la responsabilité financière.
* Affiche un faible taux de modification des données (peu de transactions par heure) et accepte une perte de données correspondant à une heure de modifications.
* Est sensible aux coûts.

Si vous avez besoin d’une récupération plus rapide, utilisez une [géo-réplication active](sql-database-geo-replication-overview.md) (abordée plus loin). Si vous devez être en mesure de récupérer des données d’une période remontant à plus de 35 jours, utilisez la [rétention des sauvegardes à long terme](sql-database-long-term-retention.md). 

### <a name="use-active-geo-replication-and-auto-failover-groups-in-preview-to-reduce-recovery-time-and-limit-data-loss-associated-with-a-recovery"></a>Utilisez la géo-réplication active et les groupes de basculement automatique (en version préliminaire) pour réduire le délai de récupération et les pertes de données associées à la récupération

Outre l’utilisation de sauvegardes de base de données pour récupérer une base de données lorsqu’une interruption de service se produit, vous pouvez utiliser la [géo-réplication active](sql-database-geo-replication-overview.md) pour configurer une base de données contenant jusqu’à quatre bases de données secondaires accessibles en lecture dans les régions de votre choix. Ces bases de données secondaires sont synchronisées avec la base de données primaire à l’aide d’un mécanisme de réplication asynchrone. Cette fonctionnalité sert à vous protéger contre l’interruption de service en cas de panne du centre de données ou de mise à niveau de l’application. La géo-réplication active peut également fournir de meilleures performances pour les requêtes en lecture seule adressées à des utilisateurs géographiquement dispersés.

Pour activer le basculement automatique et transparent, vous devez organiser vos bases de données géo-répliquées dans des groupes à l’aide de la fonction de [groupe de basculement automatique](sql-database-geo-replication-overview.md) SQL Database (en version préliminaire).

Si la base de données primaire se déconnecte de manière inattendue ou si vous devez la mettre hors ligne pour des activités de maintenance, vous pouvez rapidement promouvoir un serveur secondaire en serveur principal (opération également appelée basculement) et configurer les applications pour une connexion au serveur principal promu. Si votre application se connecte aux bases de données à l’aide de l’écouteur du groupe de basculement, vous n’avez pas besoin de modifier la configuration de chaîne de connexion SQL après le basculement. Le basculement planifié évite toute perte de données. Avec un basculement non planifié, quelques pertes de données peuvent survenir pour les transactions très récentes en raison de la nature de la réplication asynchrone. Avec les groupes de basculement automatique (en version préliminaire), vous pouvez personnaliser la stratégie de basculement de manière à minimiser la perte potentielle de données. Après un basculement, vous pouvez effectuer une restauration automatique ultérieurement, en fonction d’un plan ou lorsque le centre de données redevient disponible. Dans tous les cas, les utilisateurs sont confrontés à quelques interruptions de service minimes et doivent se reconnecter.

> [!IMPORTANT]
> Pour utiliser la géo-réplication active et les groupes de basculement automatique (en version préliminaire), vous devez être le propriétaire ou disposer d’autorisations administratives dans SQL Server. Vous pouvez configurer et effectuer un basculement à l’aide du portail Azure, de PowerShell ou de l’API REST, en utilisant les autorisations d’abonnement Azure ou à l’aide de Transact-SQL en utilisant des autorisations dans SQL Server.
> 

Utilisez la géo-réplication active et les groupes de basculement automatique (en version préliminaire) si votre application répond à l’un des critères suivants :

* Est essentielle.
* A un contrat de niveau de service (SLA) qui n’autorise pas plus de 24 heures d’interruption de service.
* Toute interruption de service peut engager la responsabilité financière.
* Affiche un taux élevé de données modifiées et la perte d’une heure de données n’est pas acceptable.
* Le coût supplémentaire lié à l'utilisation de la géoréplication est plus faible que la responsabilité financière potentielle et la perte d'activité associée.

>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-protecting-important-DBs-from-regional-disasters-is-easy/player]
>

## <a name="recover-a-database-after-a-user-or-application-error"></a>Récupérer une base de données après une erreur d’utilisateur ou d’application
* Personne n’est parfait ! Un utilisateur peut supprimer par inadvertance des données, un tableau important voire une base de données tout entière. Ou bien, une erreur d’application peut accidentellement remplacer des données correctes par des données incorrectes.

Dans ce scénario, voici les options de récupération dont vous disposez.

### <a name="perform-a-point-in-time-restore"></a>Effectuer une limite de restauration dans le temps
Vous pouvez utiliser les sauvegardes automatisées pour récupérer une copie de votre base de données à un point clairement identifié dans le temps, à condition que ce point figure dans la période de rétention de la base de données. Une fois la base de données restaurée, vous pouvez remplacer la base de données d’origine par la base de données restaurée ou copier les informations nécessaires des données restaurées vers la base de données d’origine. Si la base de données utilise la géo-réplication active, nous vous recommandons de copier les données requises de la copie restaurée vers la base de données d’origine. Si vous remplacez la base de données d’origine par la base de données restaurée, vous devez reconfigurer et resynchroniser la géo-réplication active (ce qui peut prendre un certain temps avec des bases de données volumineuses). Bien que cette opération restaure une base de données à la dernière limite de restauration dans le temps disponible, la restauration de la base de données géo-secondaire à n’importe quelle limite de restauration dans le temps n’est pas actuellement prise en charge.

Pour plus d’informations et obtenir la procédure détaillée de restauration d’une base de données à un point dans le temps à l’aide du portail Azure ou de PowerShell, voir [Limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore). Vous ne pouvez pas effectuer une récupération à l’aide de Transact-SQL.

### <a name="restore-a-deleted-database"></a>restauration d’une base de données supprimée.
Si la base de données est supprimée mais pas le serveur logique, vous pouvez restaurer cette base de données au point où sa suppression s’est produite. Cette opération restaure une sauvegarde de la base de données sur le même serveur SQL logique d’où elle a été supprimée. Vous pouvez la restaurer en utilisant son nom d’origine, ou attribuer un nouveau nom à la base de données restaurée.

Pour plus d’informations et obtenir la procédure détaillée de restauration d’une base de données supprimée à l’aide du portail Azure ou de PowerShell, voir [Restauration d’une base de données supprimée](sql-database-recovery-using-backups.md#deleted-database-restore). Vous ne pouvez pas restaurer à l’aide de Transact-SQL.

> [!IMPORTANT]
> Si le serveur logique est supprimé, vous ne pouvez pas récupérer une base de données supprimée.
>
>

### <a name="restore-from-azure-backup-vault"></a>Restauration à partir d’Azure Backup Vault
Si la perte de données s’est produite en dehors de la période de rétention actuelle pour des sauvegardes automatisées, et si votre base de données est configurée pour une rétention à long terme, vous pouvez effectuer une restauration à partir d’une sauvegarde hebdomadaire dans Azure Backup Vault vers une nouvelle base de données. À ce stade, vous pouvez remplacer la base de données d’origine par la base de données restaurée ou copier les données nécessaires à partir de la base de données restaurée vers la base de données d’origine. Si vous avez besoin de récupérer une version de votre base de données qui soit antérieure à une importante mise à niveau d’application, ou de répondre à une demande dans le cadre d’un audit ou d’un ordre légal, vous pouvez créer une base de données à l’aide d’une sauvegarde complète enregistrée dans Azure Backup Vault.  Pour plus d’informations, consultez [Rétention à long terme](sql-database-long-term-retention.md).

## <a name="recover-a-database-to-another-region-from-an-azure-regional-data-center-outage"></a>Récupérer une base de données vers une autre région suite à une panne du centre de données régional Azure
<!-- Explain this scenario -->

Bien que le fait soit rare, un centre de données Azure peut subir une panne. En cas de panne, il entraîne une interruption de service qui peut durer de quelques minutes à plusieurs heures.

* Vous pouvez attendre que votre base de données redevienne disponible une fois la panne réparée au niveau du centre de données. Cette méthode fonctionne pour les applications qui peuvent se permettre d’avoir la base de données déconnectée. C’est le cas, par exemple, d’un projet de développement ou d’une version d’évaluation gratuite sur lesquels vous n’avez pas à travailler en permanence. Lorsqu’un centre de données subit une panne, vous ne savez pas combien de temps cette panne peut durer. Cette méthode fonctionne donc uniquement si vous n’avez pas à travailler sur votre base de données pendant un certain temps.
* Une autre solution consiste à basculer vers une autre région de données si vous utilisez la géo-réplication active ou de restaurer une base de données à l’aide de sauvegardes de bases de données géo-redondantes (restauration géographique). Un basculement ne prend que quelques secondes, tandis qu’une récupération de base de données à partir de sauvegardes nécessite des heures.

Lorsque vous prenez une décision, le délai de la récupération et la quantité de données perdues dépendent de la façon dont vous décidez d’utiliser ces fonctionnalités de continuité d’activité dans votre application. En effet, vous pouvez choisir d’utiliser une combinaison de sauvegardes de bases de données et de géo-réplication active en fonction des besoins de votre application. Pour plus d’informations sur la conception d’applications pour des bases de données autonomes et des pools élastiques à l’aide de ces fonctionnalités de continuité d’activité, consultez [Conception d’applications pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

Les sections suivantes fournissent une vue d’ensemble des étapes de la récupération à l’aide de sauvegardes de bases de données ou de la géo-réplication active. Pour des instructions détaillées, y compris les exigences de planification, les étapes de post-récupération et des informations sur la simulation d’une panne pour effectuer un exercice de récupération d’urgence, voir la section [Récupérer une base de données SQL Azure en cas de défaillance](sql-database-disaster-recovery.md).

### <a name="prepare-for-an-outage"></a>Se préparer à une panne
Quelle que soit la fonctionnalité de continuité d’activité que vous utilisez, vous devez :

* Identifier et préparer le serveur cible, y compris les règles de pare-feu au niveau du serveur, les connexions et les autorisations au niveau de la base de données MASTER
* Déterminer comment rediriger les clients et les applications clientes vers le nouveau serveur
* Documenter les autres dépendances, notamment les paramètres d’audit et les alertes

Si la préparation n’est pas effectuée correctement, la mise en ligne de vos applications après un basculement ou une récupération de base de données prend plus de temps et nécessite probablement de résoudre certains problèmes dans une situation de stress, ce qui constitue une combinaison très risquée.

### <a name="fail-over-to-a-geo-replicated-secondary-database"></a>Basculement vers une base de données secondaire géo-répliquée
Si vous utilisez la géo-réplication active et les groupes de basculement automatique (en version préliminaire) comme mécanisme de récupération, vous pouvez configurer une stratégie de basculement automatique ou utiliser le [basculement manuel](sql-database-disaster-recovery.md#fail-over-to-geo-replicated-secondary-server-in-the-failover-group). Une fois le basculement lancé, la base de données secondaire est promue comme nouvelle base principale et peut alors enregistrer de nouvelles transactions et répondre aux requêtes, avec des pertes de données minimes pour les données qui n’ont pas encore été répliquées. Pour plus d’informations sur la création du processus de basculement, reportez-vous à la section [Conception d’une application pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [!NOTE]
> Lorsque le centre de données revient en ligne, les anciennes bases primaires se reconnectent automatiquement à la nouvelle base primaire et deviennent les bases de données secondaires. Si vous devez replacer la base de données primaire dans sa région d’origine, vous pouvez initier un basculement programmé de manière manuelle (restauration automatique). 
> 

### <a name="perform-a-geo-restore"></a>Effectuer une restauration géographique
Si vous utilisez des sauvegardes automatisées avec une réplication de stockage géo-redondant comme mécanisme de récupération, [lancez une récupération de base de données à l’aide de la géo-restauration](sql-database-disaster-recovery.md#recover-using-geo-restore). La récupération intervient sous 12 heures dans la plupart des cas, avec une perte de données de 1 heure maximum, selon le moment où la dernière sauvegarde différentielle horaire a été effectuée et répliquée. Tant que la récupération n’est pas terminée, la base de données ne peut pas enregistrer de transactions ou répondre à des requêtes. Bien que cette opération restaure une base de données à la dernière limite de restauration dans le temps disponible, la restauration de la base de données géo-secondaire à n’importe quelle limite de restauration dans le temps n’est pas actuellement prise en charge.

> [!NOTE]
> Si le centre de données redevient disponible avant que vous ne transfériez votre application vers la base de données récupérée, vous pouvez annuler la récupération.  
>
>

### <a name="perform-post-failover--recovery-tasks"></a>Exécution de tâches de post-basculement/récupération
Après la récupération à l’aide d’un de ces mécanismes de récupération, vous devez effectuer les tâches supplémentaires suivantes afin que les utilisateurs et les applications soient de nouveau opérationnels :

* Rediriger les clients et les applications clientes vers le nouveau serveur et la base de données restaurée
* Vérifier que les règles de pare-feu appropriées au niveau du serveur sont en place pour permettre aux utilisateurs de se connecter (ou utiliser le [pare-feu au niveau de la base de données](sql-database-firewall-configure.md#creating-and-managing-firewall-rules))
* Vérifier que les connexions et les autorisations appropriées au niveau de la base de données MASTER sont en place (ou utiliser des [utilisateurs contenus](https://msdn.microsoft.com/library/ff929188.aspx))
* Configurer l’audit, selon les besoins
* Configurer les alertes, selon les besoins

## <a name="upgrade-an-application-with-minimal-downtime"></a>Mettre à niveau une application avec un temps d’arrêt minimal
Parfois, une application doit être déconnectée en raison d’une maintenance planifiée, par exemple une mise à niveau. [Gestion des mises à niveau des applications](sql-database-manage-application-rolling-upgrade.md) explique comment utiliser la géo-réplication active pour activer les mises à niveau propagées de votre application cloud afin de réduire le temps d’arrêt pendant les mises à niveau et de fournir un chemin de récupération en cas de problème. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la conception d’applications pour des bases de données autonomes et des pools élastiques, consultez [Conception d’applications pour la récupération d’urgence cloud](sql-database-designing-cloud-solutions-for-disaster-recovery.md) et [Stratégies de récupération d’urgence de pool élastique](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).
