# [Documentation Data Lake Store](index.md)

# Vue d'ensemble
## [Présentation d'Azure Data Lake Store](data-lake-store-overview.md)
## [Comparer Azure Data Lake Store et le stockage Azure](data-lake-store-comparison-with-blob-storage.md)
## [Azure Data Lake Store pour les données Big Data](data-lake-store-data-scenarios.md)
## [Applications open source qui fonctionnent avec Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)
## [Meilleures pratiques relatives à l’utilisation de Data Lake Store](data-lake-store-best-practices.md)

# Prise en main
## [Utilisation du portail](data-lake-store-get-started-portal.md)
## [Utiliser PowerShell](data-lake-store-get-started-powershell.md)
## [Utilisation de l’interface de ligne de commande Azure 2.0](data-lake-store-get-started-cli-2.0.md)


# Procédures
## Charger et déplacer des données
### [Utilisation d’Azure Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Utilisation de l’Explorateur Stockage](data-lake-store-in-storage-explorer.md)
### [Utilisation d’AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [Utilisation de DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [Utilisation de Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [Charger des données à partir de sources hors connexion](data-lake-store-offline-bulk-data-upload.md)
### [Migrer Azure Data Lake Store dans différentes régions](data-lake-store-migration-cross-region.md)

## Sécuriser les données
### [vue d’ensemble de la sécurité](data-lake-store-security-overview.md)
### [Contrôle d’accès dans Azure Data Lake Store](data-lake-store-access-control.md)
### [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
### [Chiffrement](data-lake-store-encryption.md)

## S’authentifier avec Data Lake Store
### [Options d’authentification](data-lakes-store-authentication-using-azure-active-directory.md)
### [Authentification de l’utilisateur final](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [À l’aide de Java](data-lake-store-end-user-authenticate-java-sdk.md)
#### [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-end-user-authenticate-net-sdk.md)
#### [Utilisation de l'API REST](data-lake-store-end-user-authenticate-rest-api.md)
#### [Utilisation de Python](data-lake-store-end-user-authenticate-python.md)
### [Authentification de service à service](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [À l’aide de Java](data-lake-store-service-to-service-authenticate-java.md)
#### [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [Utilisation de l'API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Utilisation de Python](data-lake-store-service-to-service-authenticate-python.md)

## Utiliser Data Lake Store
### Opérations de gestion de compte
#### [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-get-started-net-sdk.md)
#### [Utilisation de l'API REST](data-lake-store-get-started-rest-api.md)
#### [Utilisation de Python](data-lake-store-get-started-python.md)
### Opérations de système de fichiers
#### [Utilisation du kit de développement logiciel (SDK) .NET](data-lake-store-data-operations-net-sdk.md)
#### [Utilisation du kit de développement logiciel (SDK) Java](data-lake-store-get-started-java-sdk.md)
#### [Utilisation de l'API REST](data-lake-store-data-operations-rest-api.md)
#### [Utilisation de Python](data-lake-store-data-operations-python.md)

## Performances
### [Recommandations en matière d’optimisation des performances pour Azure Data Lake Store](data-lake-store-performance-tuning-guidance.md)
### [Recommandations en matière d’optimisation des performances pour l’utilisation de PowerShell avec Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
### [Recommandations en matière d’optimisation des performances pour Spark sur HDInsight et Azure Data Lake Store](data-lake-store-performance-tuning-spark.md)
### [Recommandations en matière d’optimisation des performances pour Hive sur HDInsight et Azure Data Lake Store](data-lake-store-performance-tuning-hive.md)
### [Recommandations en matière d’optimisation des performances pour MapReduce sur HDInsight et Azure Data Lake Store](data-lake-store-performance-tuning-mapreduce.md)
### [Recommandations en matière d’optimisation des performances pour Storm sur HDInsight et Azure Data Lake Store](data-lake-store-performance-tuning-storm.md)

## Intégrer avec les services Azure
### Avec HDInsight
#### [En passant par le portail Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Utilisation d’Azure PowerShell (stockage par défaut)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Utilisation d’Azure PowerShell (stockage supplémentaire)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Utilisation du modèle Azure](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Accéder à partir de machines virtuelles dans un réseau virtuel Azure](data-lake-store-connectivity-from-vnets.md)
### [Utiliser avec Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Utiliser avec Azure Event Hubs](data-lake-store-archive-eventhub-capture.md)
### [Utiliser avec Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Utiliser avec Stream Analytics](data-lake-store-stream-analytics.md)
### [Utiliser avec Power BI](data-lake-store-power-bi.md)
### [Utiliser avec Data Catalog](data-lake-store-with-data-catalog.md)
### [Utiliser avec PolyBase dans SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [Utilisation avec SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [Autres options d’intégration Azure](data-lake-store-integrate-with-other-services.md)

## gérer
### [Accéder aux journaux de diagnostic](data-lake-store-diagnostic-logs.md)
### [Planifier la haute disponibilité](data-lake-store-disaster-recovery-guidance.md)

# Informations de référence
## [Exemples de code](https://azure.microsoft.com/en-us/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.JS](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (gestion des comptes)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
## [Python (gestion du système de fichiers)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/dls)

# Ressources
## [Feuille de route Azure](https://azure.microsoft.com/roadmap/)
## [Blog sur Data Lake Store](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Envoyer des commentaires sur UserVoice](https://feedback.azure.com/forums/327234-data-lake)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Tarification](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [Calculatrice de prix](https://azure.microsoft.com/pricing/calculator/)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Forum Stack Overflow](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
