# Vue d'ensemble

## [En quoi consiste la solution SQL Data Warehouse ?](sql-data-warehouse-overview-what-is.md)
## [Charge de travail de l’entrepôt de données](sql-data-warehouse-overview-workload.md)

# Prise en main

## [Didacticiel de chargement des données](load-data-from-azure-blob-storage-using-polybase.md)
## [Didacticiel de requête élastique](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
## [meilleures pratiques](sql-data-warehouse-best-practices.md)
## [Gérer](sql-data-warehouse-overview-manage.md)

# Procédure

## Fonctionnalités du service
### [Architecture MPP](massively-parallel-processing-mpp-architecture.md)
### [Niveaux de performances](performance-tiers.md)
### [Unités de l’entrepôt de données](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Limites de capacité](sql-data-warehouse-service-capacity-limits.md)
### [Forum Aux Questions](sql-data-warehouse-overview-faq.md)

## Sauvegarde et restauration

### [Présentation de la sauvegarde](sql-data-warehouse-backups.md)
### [Présentation de la restauration](sql-data-warehouse-restore-database-overview.md)
#### [Portail Azure](sql-data-warehouse-restore-database-portal.md)
#### [Azure PowerShell](sql-data-warehouse-restore-database-powershell.md)
#### [REST](sql-data-warehouse-restore-database-rest-api.md)

## Connecter

### [Vue d'ensemble](sql-data-warehouse-connect-overview.md)
### [SSMS](sql-data-warehouse-query-ssms.md)
### [Visual Studio](sql-data-warehouse-query-visual-studio.md)
### [Installation de Visual Studio](sql-data-warehouse-install-visual-studio.md)
### [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
### [Chaînes de connexion](sql-data-warehouse-connection-strings.md)

## Créer
### [Portail Azure](sql-data-warehouse-get-started-provision.md)
### [Azure PowerShell](sql-data-warehouse-get-started-provision-powershell.md)
### [T-SQL](sql-data-warehouse-get-started-create-database-tsql.md)

## Développement

### [Vue d'ensemble](sql-data-warehouse-overview-develop.md)

### Tables

#### [Vue d'ensemble](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Types de données](sql-data-warehouse-tables-data-types.md)
#### [Tables distribuées](sql-data-warehouse-tables-distribute.md)
#### [Index](sql-data-warehouse-tables-index.md)
#### [Identité](sql-data-warehouse-tables-identity.md)
#### [Partitions](sql-data-warehouse-tables-partition.md)
#### [Tables répliquées](design-guidance-for-replicated-tables.md)
#### [Statistiques](sql-data-warehouse-tables-statistics.md)
#### [Temporaire](sql-data-warehouse-tables-temporary.md)

### Requêtes

#### [SQL dynamique](sql-data-warehouse-develop-dynamic-sql.md)
#### [Options de regroupement](sql-data-warehouse-develop-group-by-options.md)
#### [Étiquettes](sql-data-warehouse-develop-label.md)

### Éléments de langage T-SQL

#### [Boucles](sql-data-warehouse-develop-loops.md)
#### [Procédures stockées](sql-data-warehouse-develop-stored-procedures.md)
#### [Transactions](sql-data-warehouse-develop-transactions.md)
#### [Bonnes pratiques relatives aux transactions](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Schémas définis par l’utilisateur](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Attribution de variables](sql-data-warehouse-develop-variable-assignment.md)
#### [Views](sql-data-warehouse-develop-views.md)

## Integrate

### [Vue d'ensemble](sql-data-warehouse-overview-integrate.md)
### [Data Factory](sql-data-warehouse-integrate-azure-data-factory.md)
### [Machine Learning](sql-data-warehouse-integrate-azure-machine-learning.md)
### [Didacticiel sur Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Power BI](sql-data-warehouse-integrate-power-bi.md)
### [Visualisation Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
### [Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)

### [Requête élastique avec SQL Database et SQL Data Warehouse](how-to-use-elastic-query-with-sql-data-warehouse.md)

## charger

### Concepts
#### [Vue d'ensemble](sql-data-warehouse-overview-load.md)
#### [Guide PolyBase](sql-data-warehouse-load-polybase-guide.md)

### Didacticiels
#### [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)

### Procédures
#### [Exemples de données](sql-data-warehouse-load-sample-databases.md)
#### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
#### [BCP](sql-data-warehouse-load-with-bcp.md)
#### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
#### [Charger - PolyBase depuis le stockage blob](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
#### [Charger - PolyBase depuis SQL Server](sql-data-warehouse-load-from-sql-server-with-polybase.md)
#### [RedGate](sql-data-warehouse-load-with-redgate.md)
#### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)

## Migrer

### [Vue d'ensemble](sql-data-warehouse-overview-migrate.md)
### [Utilitaire de migration](sql-data-warehouse-migrate-migration-utility.md)
### [Migration du schéma](sql-data-warehouse-migrate-schema.md)
### [Migrer du code](sql-data-warehouse-migrate-code.md)
### [Migration des données](sql-data-warehouse-migrate-data.md)
### [Migrer vers le stockage Premium](sql-data-warehouse-migrate-to-premium-storage.md)

## Gérer le calcul

### [Vue d'ensemble](sql-data-warehouse-manage-compute-overview.md)
### [Portail Azure](sql-data-warehouse-manage-compute-portal.md)
### [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [API REST](sql-data-warehouse-manage-compute-rest-api.md)
### [T-SQL](sql-data-warehouse-manage-compute-tsql.md)
### [Automatiser les niveaux de calcul](manage-compute-with-azure-functions.md)

### Classes de ressources
#### [Instructions](resource-classes-for-workload-management.md)
#### [Analyser votre charge de travail](analyze-your-workload.md)

## Performances

### [Vue d'ensemble](sql-data-warehouse-overview-manage-user-queries.md)
### [Compression columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Surveiller](sql-data-warehouse-manage-monitor.md)

## Sécurité

### [Vue d'ensemble](sql-data-warehouse-overview-manage-security.md)
### [Audit](sql-data-warehouse-auditing-overview.md)
### [Audit pour les clients de niveau inférieur](sql-data-warehouse-auditing-downlevel-clients.md)
### [Authentification](sql-data-warehouse-authentication.md)
### [Chiffrement](sql-data-warehouse-encryption-tde.md)
### [Chiffrement avec T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Détection de menaces](sql-data-warehouse-security-threat-detection.md)

## Résolution des problèmes
### [Résolution des problèmes](sql-data-warehouse-troubleshoot.md)

# Référence


## T-SQL
### [Référence complète](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Éléments de langage SQL DW](sql-data-warehouse-reference-tsql-language-elements.md)
### [Instructions SQL DW](sql-data-warehouse-reference-tsql-statements.md)
## [Vues système](sql-data-warehouse-reference-tsql-system-views.md)
## [Applets de commande PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=databases)
## [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Demandes de fonctionnalités](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Support](sql-data-warehouse-get-started-create-support-ticket.md)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Partenaires
### [Décisionnel](sql-data-warehouse-partner-business-intelligence.md)
### [Intégration des données](sql-data-warehouse-partner-data-integration.md)
### [Gestion des données](sql-data-warehouse-partner-data-management.md)
