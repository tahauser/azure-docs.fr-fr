# [Documentation SQL Data Warehouse](index.md)

# Vue d'ensemble

## [À propos de SQL Data Warehouse](sql-data-warehouse-overview-what-is.md)
## [Aide-mémoire](cheat-sheet.md)

# Démarrages rapides

## [Créer et connecter - portail](create-data-warehouse-portal.md)
## Suspension et reprise du calcul
### [Portail](pause-and-resume-compute-portal.md)
### [PowerShell](pause-and-resume-compute-powershell.md)
## Mise à l’échelle des ressources de calcul
### [Portail](quickstart-scale-compute-portal.md)
### [PowerShell](quickstart-scale-compute-powershell.md)
### [T-SQL](quickstart-scale-compute-tsql.md)


# Didacticiels
## [1 - Charger WideWorldImporters](load-data-wideworldimportersdw.md)

# Concepts
## Fonctionnalités du service
### [Architecture MPP](massively-parallel-processing-mpp-architecture.md)
### [Niveaux de performances](performance-tiers.md)
### [Unités de l’entrepôt de données](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Scale-out, pause, reprise](sql-data-warehouse-manage-compute-overview.md)
### [Sauvegardes d’entrepôts de données](sql-data-warehouse-backups.md)
### [Audit](sql-data-warehouse-auditing-overview.md)
### [Limites de capacité](sql-data-warehouse-service-capacity-limits.md)
### [FORUM AUX QUESTIONS](sql-data-warehouse-overview-faq.md)

## Sécurité
### [Vue d'ensemble](sql-data-warehouse-overview-manage-security.md)
### [Authentification](sql-data-warehouse-authentication.md)


## Migrer vers SQL Data Warehouse
### [Vue d'ensemble](sql-data-warehouse-overview-migrate.md)
### [Migration du schéma](sql-data-warehouse-migrate-schema.md)
### [Migrer du code](sql-data-warehouse-migrate-code.md)
### [Migration des données](sql-data-warehouse-migrate-data.md)

## Chargement et transfert de données
### [Vue d’ensemble](design-elt-data-loading.md)
### [meilleures pratiques](guidance-for-loading-data.md)


## Integrate
### [Vue d'ensemble](sql-data-warehouse-overview-integrate.md)
### [Requête élastique SQL Database](how-to-use-elastic-query-with-sql-data-warehouse.md)


## Performances des requêtes
### [Classes de ressources](resource-classes-for-workload-management.md)
### [Compression columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)

## [Surveiller](sql-data-warehouse-manage-monitor.md)


## Développer des entrepôts de données
### [Vue d'ensemble](sql-data-warehouse-overview-develop.md)
### [Composants d’entrepôts de données](sql-data-warehouse-overview-workload.md)

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

## [Résolution des problèmes](sql-data-warehouse-troubleshoot.md)

# Procédures
## Fonctionnalités du service
### [Restaurer un entrepôt de données - portail](sql-data-warehouse-restore-database-portal.md)
### [Restaurer un entrepôt de données - PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [Restaurer un entrepôt de données - API REST](sql-data-warehouse-restore-database-rest-api.md)

## Sécurité
### [Activer le chiffrement - portail](sql-data-warehouse-encryption-tde.md)
### [Activer le chiffrement - T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Détection de menaces](sql-data-warehouse-security-threat-detection.md)


## Chargement et transfert de données
### [Données sur les taxis de New York](load-data-from-azure-blob-storage-using-polybase.md)
### [Données publiques de Contoso](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [AZCopy](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## Integrate
### [Configurer une requête élastique SQL Database](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [Ajouter une tâche Azure Stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [Utiliser l’apprentissage automatique](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Visualiser des données avec Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## Surveiller et régler
### [Analyser votre charge de travail](analyze-your-workload.md)

## Montée en charge

### [Automatiser les niveaux de calcul](manage-compute-with-azure-functions.md)


# Informations de référence

## T-SQL
### [Référence complète](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Éléments de langage SQL DW](sql-data-warehouse-reference-tsql-language-elements.md)
### [Instructions SQL DW](sql-data-warehouse-reference-tsql-statements.md)
## [Vues système](sql-data-warehouse-reference-tsql-system-views.md)
## [Applets de commande PowerShell](sql-data-warehouse-reference-powershell-cmdlets.md)
## [API REST](sql-data-warehouse-manage-compute-rest-api.md)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/?category=databases)
## [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Tarification](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Demandes de fonctionnalités](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Dépassement de capacité de la pile](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Support](sql-data-warehouse-get-started-create-support-ticket.md)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Partenaires
### [Décisionnel](sql-data-warehouse-partner-business-intelligence.md)
### [Intégration des données](sql-data-warehouse-partner-data-integration.md)
### [Gestion des données](sql-data-warehouse-partner-data-management.md)
