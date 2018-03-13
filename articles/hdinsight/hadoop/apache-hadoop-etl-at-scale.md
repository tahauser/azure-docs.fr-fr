---
title: "Extraire, transformer et charger (ETL) des données à l’échelle - Azure HDInsight | Microsoft Docs"
description: "Découvrez comment ETL est utilisé dans HDInsight avec Hadoop."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 8b55bafee83dd43d535f9ebb0488134b5c7b3446
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Extraire, transformer et charger (ETL) à l’échelle

L’extraction, la transformation et le chargement (ETL) sont le processus par lequel les données sont acquises à partir de diverses sources, collectées dans un emplacement standard, nettoyées et traitées et finalement chargées dans une banque de données à partir de laquelle elles peuvent être interrogées. Les processus ETL hérités importent des données, les nettoient sur place, puis les stockent dans un moteur de données relationnelles. Avec HDInsight, une grande variété de composants d’écosystème Hadoop prend en charge l’exécution du processus ETL à grande échelle. 

L’utilisation de HDInsight dans le processus ETL peut être résumée par ce pipeline :

![Vue d’ensemble du processus ETL HDInsight](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

Les sections qui suivent décrivent chacune des phases ETL et les composants associés.

## <a name="orchestration"></a>Orchestration

L’orchestration s’étend sur toutes les phases du pipeline ETL. Les travaux ETL dans HDInsight impliquent souvent plusieurs produits travaillant les uns avec les autres.  Vous pouvez utiliser Hive pour nettoyer une partie des données, tandis que Pig en nettoie une autre.  Vous pouvez utiliser Azure Data Factory pour charger des données dans Azure SQL Database à partir d’Azure Data Lake Store.

L’orchestration est nécessaire pour exécuter le travail approprié au moment opportun.

### <a name="oozie"></a>Oozie

Apache Oozie est un système de coordination de flux de travail qui gère les tâches Hadoop. Oozie s’exécute dans un cluster HDInsight et est intégré à la pile Hadoop. Oozie prend en charge les tâches Hadoop pour Apache MapReduce, Apache Pig, Apache Hive et Apache Sqoop. Oozie peut également être utilisé pour planifier des tâches propres à un système comme des programmes Java ou des scripts shell.

Pour plus d’informations, consultez la section [Utiliser Oozie avec Hadoop pour définir et exécuter un workflow sur HDInsight](../hdinsight-use-oozie-linux-mac.md). Pour savoir en détail comment utiliser Oozie pour piloter un pipeline de bout en bout, consultez la page [Opérationnaliser le pipeline de données](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory fournit des capacités d’orchestration sous la forme d’une plateforme en tant que service. Il s’agit d’un service d’intégration de données basé sur le cloud qui vous permet de créer des flux de travail orientés données dans le cloud pour orchestrer et automatiser le déplacement et la transformation des données. 

À l’aide d’Azure Data Factory, vous pouvez :

1. Créer et planifier des flux de travail pilotés par les données (appelés pipelines) qui ingèrent des données provenant de différents magasins de données.
2. Traiter et transformer les données à l’aide de services de calcul tels que Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch et Azure Machine Learning.
3. Publier des données de sortie vers des magasins de données tels que Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser.

Pour plus d’informations sur Azure Data Factory, consultez la [documentation](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Ingérer le stockage de fichiers et le stockage des résultats

Les fichiers de données sources sont généralement chargés dans un emplacement dans Stockage Azure ou Azure Data Lake Store. Les fichiers peuvent être dans n’importe quel format, mais en général, il s’agit de fichiers plats tels que des fichiers CSV. 

### <a name="azure-storage"></a>Stockage Azure 

[Stockage Azure](https://azure.microsoft.com/services/storage/blobs/) a des [objectifs de scalabilité spécifiques](../../storage/common/storage-scalability-targets.md).  Pour la plupart des nœuds d’analytique, Stockage Azure propose une meilleure mise à l’échelle avec de nombreux petits fichiers.  Stockage Azure garantit des performances identiques, quel que soit le nombre de fichiers ou la taille des fichiers (tant que vous êtes dans les limites que vous avez définies).  Cela signifie que vous pouvez stocker des téraoctets de données et que vous obtenez toujours des performances cohérentes, que vous utilisiez un sous-ensemble des données ou toutes les données.

Stockage Azure a plusieurs types d’objets blob.  Un *blob d’ajout* est idéal pour le stockage de fichiers journaux web ou de données de capteur.  

Plusieurs blobs peuvent être répartis sur plusieurs serveurs afin d’offrir un accès horizontal. Toutefois, un blob donné ne peut être servi que par un seul serveur. Si les blobs peuvent être regroupés de manière logique dans des conteneurs, ce regroupement n’a aucune incidence sur le partitionnement.

Stockage Azure a également une couche API WebHDFS pour le stockage d’objets blob.  Tous les services dans HDInsight peuvent accéder aux fichiers dans Stockage Blob Azure pour le nettoyage et le traitement des données, de la même façon que ces services utilisent le système HDFS (Hadoop Distributed Files System).

Les données sont généralement ingérées dans Stockage Azure à l’aide de PowerShell, du SDK Stockage Azure ou de AZCopy.

### <a name="azure-data-lake-store"></a>Azure Data Lake Store

Azure Data Lake Store (ADLS) est un dépôt hyperscale managé pour les données d’analytique qui sont compatibles avec HDFS.  ADLS utilise un modèle de conception similaire à HDFS et offre une scalabilité illimitée en termes de capacité totale et de taille de fichier individuel. ADLS est très bon avec les fichiers volumineux, car un fichier volumineux peut être stocké sur plusieurs nœuds.  Le partitionnement des données dans ADLS s’effectue en arrière-plan.  Vous bénéficiez d’un débit suffisamment important pour exécuter des travaux d’analyse avec des milliers d’exécuteurs simultanés lisant et écrivant de façon efficace des centaines de téraoctets de données.

Les données sont généralement ingérées dans ADLS à l’aide d’Azure Data Factory, des SDK ADLS, AdlCopy Service, Apache DistCp ou Apache Sqoop.  Le choix du service à utiliser dépend en grande partie de l’emplacement des données.  Si les données sont situées dans un cluster Hadoop existant, vous pouvez utiliser Apache DistCp, AdlCopy Service ou Azure Data Factory.  Si elles sont stockées dans Stockage Blob Azure, vous pouvez utiliser le SDK .NET Azure Data Lake Store, Azure PowerShell ou Azure Data Factory.

ADLS est également optimisé pour l’ingestion d’événements à l’aide d’Azure Event Hubs ou d’Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Considérations relatives aux deux options de stockage

Pour le chargement de jeux de données représentant plusieurs téraoctets, la latence du réseau peut être un problème majeur, particulièrement si les données proviennent d’un emplacement local.  Dans ce cas, vous pouvez utiliser les options ci-dessous :

* Azure ExpressRoute vous permet de créer des connexions privées entre les centres de données Azure et votre infrastructure locale. Ces connexions constituent une option fiable pour le transfert de grandes quantités de données. Pour plus d’informations, consultez la [Documentation Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* Chargement « hors connexion » des données. Vous pouvez utiliser le [service Azure Import/Export](../../storage/common/storage-import-export-service.md) pour expédier des disques durs contenant vos données à un centre de données Azure. Vos données sont alors téléchargées vers des objets blob Azure Storage. Vous pouvez ensuite utiliser [Azure Data Factory](../../data-factory/v1/data-factory-azure-datalake-connector.md) ou [l’outil AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) pour copier des données des objets blob Azure Storage vers Data Lake Store.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW est un choix idéal pour stocker les résultats nettoyés et préparés pour de futures analytiques.  Azure HDInsight peut servir à exécuter ces services pour Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) est un magasin de base de données relationnelle optimisé pour les charges de travail d’analytique.  Azure SQL DW se met à l’échelle en fonction de tables partitionnées.  Les tables peuvent être partitionnées entre plusieurs nœuds.  Les nœuds Azure SQL DW sont sélectionnés au moment de la création.  Ils peuvent être mis à l’échelle après coup, mais il s’agit alors d’un processus actif qui peut nécessiter le déplacement de données. Pour plus d’informations, consultez [SQL Data Warehouse - Gérer le calcul](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="hbase"></a>hbase

Apache HBase est un magasin clé-valeur disponible dans Azure HDInsight.  Apache HBase est une base de données NoSQL open source, basée sur Hadoop et modélisée d'après Google BigTable. HBase fournit un accès aléatoire performant et une forte cohérence pour de vastes quantités de données non structurées et semi-structurées, dans une base de données sans schéma, organisée par familles de colonnes.

Les données sont stockées dans les lignes d'une table et les données au sein d'une ligne sont regroupées par familles de colonnes. HBase est une base de données sans schéma dans le sens où ni les colonnes ni le type de données qui y sont stockées ne doivent être définis avant de pouvoir les utiliser. Le code open source peut être mis à l'échelle de façon linéaire pour gérer des pétaoctets de données dans des milliers de nœuds. HBase peut reposer sur la redondance des données, le traitement par lots et d’autres fonctionnalités qui sont fournies par des applications distribuées dans l’écosystème Hadoop.   

HBase est une excellente destination pour les données de capteur et de journal pour des analytiques futures.

La scalabilité de HBase est déterminée par le nombre de nœuds contenus dans le cluster HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database et Azure Database

Azure propose trois bases de données relationnelles PaaS (platforme-as-a-service).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) est une implémentation de Microsoft SQL Server. Pour en savoir plus sur les performances, consultez [Paramétrage des performances dans Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database pour MySQL](../../mysql/overview.md) est une implémentation d’Oracle MySQL.
* [Azure Database pour PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) est une implémentation de PostgreSQL.

Ces produits ont une scalabilité verticale, ce qui signifie qu’ils peuvent avoir une échelle plus grande en ajoutant plus d’UC et de mémoire.  Vous pouvez également choisir d’utiliser des disques Premium avec les produits pour bénéficier de meilleures performances d’E/S.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) est un moteur de données analytiques utilisé dans l’aide à la décision et l’analytique métier. Il fournit des données analytiques pour les rapports d’entreprise et les applications clientes, telles que Power BI, Excel, les rapports Reporting Services et d’autres outils de visualisation des données.

Les cubes d’analyse peuvent être mis à l’échelle en modifiant les niveaux pour chaque cube individuel.  Pour en savoir plus, consultez [Tarification d’Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extraire et charger

Une fois que les données existent dans Azure, vous pouvez utiliser de nombreux services pour les extraire et les charger dans d’autres produits.  HDInsight prend en charge Sqoop et Flume. 

### <a name="sqoop"></a>Sqoop

Apache Sqoop est un outil conçu pour transférer efficacement des données entre des sources de données structurées, semi-structurées et non structurées. 

Sqoop utilise MapReduce pour importer et exporter les données, fournir une tolérance de panne et un fonctionnement parallèle.

### <a name="flume"></a>Flume

Apache Flume est un service distribué, fiable et disponible pour la collecte, l’agrégation et le déplacement efficaces de grandes quantités de données de journal. Flume possède une architecture simple et flexible basée sur des flux de données de streaming. Flume est un service fiable et à tolérance de pannes avec des mécanismes de fiabilité paramétrables et de nombreux mécanismes de basculement et de récupération. Flume utilise un modèle de données extensible simple qui autorise l’application analytique en ligne.

Apache Flume ne peut pas être utilisé avec Azure HDInsight.  Une installation Hadoop locale permet d’utiliser Flume pour envoyer des données à Azure Storage Blob ou Azure Data Lake Store.  Pour plus d’informations, consultez [Utilisation d’Apache Flume avec HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformer

Une fois qu’il existe des données dans l’emplacement choisi, vous devez les nettoyer, combiner ou préparer pour un modèle d’utilisation spécifique.  Hive, Pig et Spark SQL sont des choix adéquats pour ce type de travail.  Ils sont tous pris en charge sur HDInsight. 

## <a name="next-steps"></a>Étapes suivantes

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utiliser Apache Hive comme outil ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
