---
title: "Comparaison des fonctionnalités d’Azure SQL Database | Microsoft Docs"
description: "Cet article compare les fonctionnalités de SQL Server et Azure SQL Database et détaille leurs différences."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 02/08/2018
ms.author: carlrab
ms.openlocfilehash: dc9a7fa0a7fa0e029f71510cc516496ed12a6274
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Comparaison des fonctionnalités d’Azure SQL Database comparées à celles de SQL Server 

Azure SQL Database partage une base de code commune avec SQL Server. Les deux outils offrent quasiment les mêmes fonctionnalités au niveau de la base de données. Les principales différences de fonctionnalités entre Azure SQL Database et SQL Server interviennent au niveau de l’instance. 

Nous continuons d’ajouter des fonctionnalités à Azure SQL Database. Par conséquent, nous vous encourageons à consulter notre page web sur les mises à jour des services pour Azure et à utiliser ses filtres :

* Filtrez sur [Service SQL Database](https://azure.microsoft.com/updates/?service=sql-database).
* Filtrez sur [annonces](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) de disponibilité générale pour les fonctionnalités SQL Database.

## <a name="sql-server-and-sql-database-feature-support"></a>Prise en charge des fonctionnalités SQL Server et SQL Database

Le tableau suivant répertorie les principales fonctionnalités de SQL Server et fournit des informations sur la prise en charge de chaque fonctionnalité, ainsi qu’un lien vers davantage d’informations sur la fonctionnalité. Pour connaître les différences de Transact-SQL à prendre en compte lors de la migration d’une solution de base de données existante, consultez [Résolution des différences de Transact-SQL durant la migration vers SQL Database](sql-database-transact-sql-information.md).


| **Fonctionnalité SQL Server** | **Prise en charge dans Azure SQL Database** | 
| --- | --- |  
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Oui - voir [Magasin de certificats](sql-database-always-encrypted.md) et [Coffre de clés](sql-database-always-encrypted-azure-key-vault.md)|
| [Groupes de disponibilité AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | La haute disponibilité est incluse dans chaque base de données. Consultez [Haute disponibilité et Azure SQL Database](sql-database-high-availability.md). La récupération d’urgence est abordée dans [Vue d’ensemble de la continuité de l’activité avec Azure SQL Database](sql-database-geo-replication-overview.md) |
| [Attacher une base de données](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Non  |
| [Rôles d’application](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | OUI |
|[Audit](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Oui](sql-database-auditing.md)|
| [Réglage automatique](/sql/relational-databases/automatic-tuning/automatic-tuning)| [Oui](sql-database-automatic-tuning.md)|
| [Fichier BACPAC (exporter)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Oui - voir [Exportation de base de données SQL](sql-database-export.md) |
| [Fichier BACPAC (importer)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Oui - voir [Importation de base de données SQL](sql-database-import.md) |
| [Commande de sauvegarde](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Non - voir [Sauvegardes automatisées](sql-database-automated-backups.md) |
| [Fonctions intégrées](https://docs.microsoft.com/sql/t-sql/functions/functions) | La plupart - voir Fonctions individuelles |
| [Modifier la capture de données](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Non  |
| [Suivi des modifications](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | OUI |
| [Instructions de classement](https://docs.microsoft.com/sql/t-sql/statements/collations) | OUI |
| [Index Columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Oui - [Édition Premium uniquement](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [Common Language Runtime (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Non  |
| [Bases de données à relation contenant-contenu](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | OUI |
| [Utilisateurs contenus](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | OUI |
| [Contrôle des mots clés de langage de flux](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | OUI |
| [Requêtes entre plusieurs bases de données](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Partiellement - voir [Requêtes élastiques](sql-database-elastic-query-overview.md) |
| [Curseurs](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | OUI | 
| [Compression des données](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | OUI |
| [Messagerie de base de données](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Non  |
| [Mise en miroir de bases de données](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Non  |
| [Paramètres de configuration de base de données](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | OUI |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Non  |
| [Instantanés de base de données](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Non  |
| [Types de données](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | OUI |  
| [Instructions DBCC](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | La plupart - voir Instructions individuelles |
| [Instructions DDL](https://docs.microsoft.com/sql/t-sql/statements/statements) | OUI |
| [Déclencheurs DDL](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Base de données uniquement |
| [Transactions distribuées - MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Non - voir [Transactions élastiques](sql-database-elastic-transactions-overview.md) |
| [Instructions DML](https://docs.microsoft.com/sql/t-sql/queries/queries) | OUI |
| [Déclencheurs DML](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | La plupart - voir Instructions individuelles | 
| [DMV](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Certains - voir DMV individuels |
|[Masquage des données dynamiques](/sql/relational-databases/security/dynamic-data-masking)|[Oui](sql-database-dynamic-data-masking-get-started.md)|
| [Notifications d’événement](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Non - voir [Alertes](sql-database-insights-alerts-portal.md) |
| [Expressions](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |OUI |
| [Événements étendus](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Certains - voir [Événements étendus dans SQL Database](sql-database-xevent-db-diff-from-svr.md) |
| [Procédures stockées étendues](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Non  |
| [Fichiers et groupes de fichiers](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Groupe de fichiers principal uniquement |
| [FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Non  |
| [Recherche en texte intégral](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | Analyseurs lexicaux tiers non pris en charge |
| [Fonctions](https://docs.microsoft.com/sql/t-sql/functions/functions) | La plupart - voir Fonctions individuelles |
| [Traitement de graphe](/sql/relational-databases/graphs/sql-graph-overview) | OUI |
| [Optimisation en mémoire](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Oui - [Édition Premium uniquement](sql-database-in-memory.md) |
| [Prise en charge des données JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | OUI |
| [Éléments de langage](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | La plupart - voir Éléments individuels |  
| [Serveurs liés](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Non - voir [Requête élastique](sql-database-elastic-query-horizontal-partitioning.md) |
| [Copie des journaux de transaction](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | La haute disponibilité est incluse dans chaque base de données. La récupération d’urgence est abordée dans [Vue d’ensemble de la continuité de l’activité avec Azure SQL Database](sql-database-geo-replication-overview.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Non  |
| [Journalisation minimale dans l’importation en bloc](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Non  |
| [Modification des données système](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Non  |
| [Opérations d’index en ligne](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | OUI |
| [Opérateurs](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | La plupart - voir Opérateurs individuels |
| [Limite de restauration dans le temps d’une base de données](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Oui - voir [Récupération de base de données SQL](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Non  |
| [Gestion basée sur des stratégies](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Non  |
| [Prédicats](https://docs.microsoft.com/sql/t-sql/queries/predicates) | OUI |
| [R Services](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Version préliminaire ; consultez [Nouveautés du Machine Learning](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  |
| [Gouverneur de ressources](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Non  |
| [Instructions RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Non  | 
| [Restauration de la base de données à partir de la sauvegarde](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | À partir des sauvegardes intégrées uniquement - voir [Récupération de base de données SQL](sql-database-recovery-using-backups.md) |
| [Sécurité au niveau des lignes](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | OUI |
| [Recherche sémantique](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Non  |
| [Numéros de séquence](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | OUI |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Non  |
| [Paramètres de configuration du serveur](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Non  |
| [Instructions SET](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | La plupart - voir Instructions individuelles 
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | OUI |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Non. Voir [Tâches élastiques](sql-database-elastic-jobs-getting-started.md). |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Voir [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [Audit SQL Server](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Non - voir [Audit de base de données SQL](sql-database-auditing.md) |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Oui - voir [Effectuer un lift-and-shift des charges de travail SQL Server Integration Services vers le cloud](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | OUI |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Non - voir [Événements étendus](sql-database-xevent-db-diff-from-svr.md) |
| [Réplication SQL Server](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Abonné à la réplication transactionnelle et de capture instantanée uniquement](sql-database-cloud-migrate.md) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Non  |
| [procédures stockées](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | OUI |
| [Fonctions stockées sur système](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | La plupart - voir Fonctions individuelles |
| [Procédures stockées sur système](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Certaines - voir Procédures stockées individuelles |
| [Tables système](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Certaines - voir Tables individuelles |
| [Vues catalogue système](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Certaines - voir Vues individuelles |
| [Partitionnement de table](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Oui - Groupe de fichiers principal uniquement |
| [Tables temporaires](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | Tables temporaires globales niveau base de données ou local uniquement |
| [Tables temporelles](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | OUI |
| [Variables](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | OUI | 
| [Chiffrement transparent des données (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | OUI |
| [Clustering de basculement Windows Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | La haute disponibilité est incluse dans chaque base de données. La récupération d’urgence est abordée dans [Vue d’ensemble de la continuité de l’activité avec Azure SQL Database](sql-database-geo-replication-overview.md) |
| [Index XML](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | OUI |

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur le service Azure SQL Database, consultez [What is SQL Database?](sql-database-technical-overview.md) (Qu’est-ce qu’une base de données SQL ?)
- Pour plus d’informations sur la prise en charge de Transact-SQL et les différences, consultez la page [Résolution des différences de Transact-SQL durant la migration vers SQL Database](sql-database-transact-sql-information.md).
