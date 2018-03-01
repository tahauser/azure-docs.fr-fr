---
title: "Scénarios de données impliquant Data Lake Store | Microsoft Docs"
description: "Comprendre les différents scénarios et outils à l’aide desquels les données peuvent reçues, traitées, téléchargées et affichées dans Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 37409a71-a563-4bb7-bc46-2cbd426a2ece
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: bd69d3c353c7f44efb37e2359412aa3a9f3e8f8d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/24/2018
---
# <a name="using-azure-data-lake-store-for-big-data-requirements"></a>Utilisation d’Azure Data Lake Store pour les données volumineuses
Il existe quatre étapes principales dans traitement des données Big Data :

* Réception de grandes quantités de données dans un magasin de données, en temps réel ou par lots
* Traitement des données
* Téléchargement des données
* Visualisation des données

Dans cet article, nous allons examiner ces étapes en ce qui concerne Azure Data Lake Store pour comprendre les options et les outils disponibles pour répondre à vos besoins en termes de Big Data.

## <a name="ingest-data-into-data-lake-store"></a>Réception de données dans Data Lake Store
Cette section présente les différentes sources de données et les différentes façons selon lesquelles ces données peuvent être reçues dans un compte Data Lake Store.

![Réception de données dans Data Lake Store](./media/data-lake-store-data-scenarios/ingest-data.png "Réception de données dans Data Lake Store")

### <a name="ad-hoc-data"></a>Données ad hoc
Ceci représente les petits jeux de données qui sont utilisés pour créer un prototype d’une application de Big Data. Il existe différentes façons de recevoir des données ad hoc en fonction de la source de données.

| source de données | Réception avec |
| --- | --- |
| Ordinateur local |<ul> <li>[portail Azure](/data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Interface CLI 2.0 inter-plateforme Azure](data-lake-store-get-started-cli-2.0.md)</li> <li>[Utilisation de Data Lake Tools pour Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[l’outil AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[DistCp en cours d’exécution sur un cluster HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Flux de données
Ceci représente les données qui peuvent être générées par diverses sources, telles que des applications, des appareils, des capteurs, etc. Ces données peuvent être reçues dans un Data Lake Store par des outils divers. En général, ces outils capturent et traitent les données sur la base de l’événement en temps réel, puis ils écrivent les événements par lots dans Data Lake Store afin qu’ils puissent être traités.

Voici les outils que vous pouvez utiliser :

* [Azure Stream Analytics](../stream-analytics/stream-analytics-data-lake-output.md) : les événements reçus dans Event Hubs peuvent être écrits dans Azure Data Lake à l’aide d’une sortie Azure Data Lake Store.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) : vous pouvez écrire des données directement dans Data Lake Store à partir du cluster Storm.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md) : vous pouvez recevoir des événements à partir d’Event Hubs, puis les écrire dans Data Lake Store à l’aide du [Kit de développement logiciel (SDK) .NET Data Lake Store](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Données relationnelles
Les bases de données relationnelles peuvent également être utilisées comme sources des données. Sur une période donnée, les bases de données relationnelles collectent de grandes quantités de données qui peuvent fournir des informations clés si elles sont traitées via un pipeline de Big Data. Vous pouvez utiliser les outils suivants pour déplacer ces données vers Data Lake Store.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Données de journal de serveur web (téléchargement à l’aide d’applications personnalisées)
Ce type de jeu de données est spécifiquement indiqué, car l’analyse des données de journal de serveur web constitue un cas d’usage courant pour les applications de Big Data et nécessite le téléchargement d’importants volumes de fichiers journaux dans Data Lake Store. Vous pouvez utiliser les outils suivants pour écrire vos propres scripts ou applications pour télécharger ces données.

* [Interface CLI 2.0 inter-plateforme Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Kit de développement logiciel (SDK) .NET Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Pour télécharger des données de journal de serveur web, et également pour télécharger d’autres types de données (par exemple, les données relatives aux sentiments sociaux), il est préférable d’écrire vos propres scripts/applications personnalisés, car cela vous donne la possibilité d’inclure votre composant de téléchargement de données dans le cadre de votre application de Big Data plus étendue. Dans certains cas, ce code peut prendre la forme d’un script ou d’un utilitaire de ligne de commande simple. Dans d’autres cas, le code peut être utilisé pour intégrer le traitement de Big Data dans une solution ou une application métier.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Données associées aux clusters Azure HDInsight
La plupart des types de clusters HDInsight (Hadoop, HBase, Storm) prend en charge Data Lake Store comme référentiel de stockage des données. Les clusters HDInsight accèdent aux données à partir des objets blob d’Azure Storage (WASB). Pour optimiser les performances, vous pouvez copier les données à partir de WASB sur un compte Data Lake Store associé au cluster. Vous pouvez utiliser les outils suivants pour copier les données.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy Service](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Données stockées localement ou dans des clusters IaaS Hadoop
De grandes quantités de données peuvent être stockées dans des clusters Hadoop existants, localement sur les ordinateurs à l’aide de HDFS. Les clusters Hadoop peuvent être inclus dans un déploiement local ou au sein d’un cluster IaaS sur Azure. Il peut être nécessaire de copier ces données dans Azure Data Lake Store afin de bénéficier d’un accès unique ou périodique. Différentes options vous permettent d’y parvenir. Voici une liste de ces options et des compromis correspondants.

| Approche | Détails | Avantages | Considérations |
| --- | --- | --- | --- |
| Utiliser Azure Data Factory (ADF) pour copier des données directement à partir de clusters Hadoop dans Azure Data Lake Store |[ADF prend en charge HDFS comme source de données](../data-factory/connector-hdfs.md) |ADF offre une prise en charge immédiate de HDFS ainsi qu’une gestion et une surveillance de bout en bout de premier ordre |Nécessite que la passerelle de gestion des données soit déployée localement ou dans le cluster IaaS |
| Exportez les données depuis Hadoop sous forme de fichiers. Copiez ensuite les fichiers dans Azure Data Lake Store à l’aide du mécanisme approprié. |Vous pouvez copier des fichiers dans Azure Data Lake Store en utilisant :  <ul><li>[Azure PowerShell pour système d'exploitation Windows](data-lake-store-get-started-powershell.md)</li><li>[Interface CLI 2.0 inter-plateforme Azure pour système d’exploitation non Windows](data-lake-store-get-started-cli-2.0.md)</li><li>Application personnalisée utilisant n’importe quel SDK Data Lake Store</li></ul> |Pour commencer. Permet des téléchargements personnalisés |Processus en plusieurs étapes qui implique différentes technologies. La gestion et la surveillance deviendront de plus en plus exigeantes au fil du temps, étant donné le caractère personnalisé des outils |
| Utilisez Distcp pour copier les données de Hadoop dans Azure Storage. Copiez ensuite les fichiers d’Azure Storage dans Data Lake Store à l’aide du mécanisme approprié. |Vous pouvez copier les données d’Azure Storage dans Data Lake Store en utilisant :  <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[l’outil AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[pache DistCp en cours d’exécution sur des clusters HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Vous pouvez utiliser des outils open source. |Processus en plusieurs étapes qui implique différentes technologies |

### <a name="really-large-datasets"></a>Jeux de données très volumineux
Pour télécharger des jeux de données qui comptent plusieurs téraoctets, l’utilisation des méthodes décrites ci-dessus peut parfois être lente et coûteuse. Dans ce cas, vous pouvez utiliser les options ci-dessous.

* **Utilisation d’Azure ExpressRoute**. Azure ExpressRoute vous permet de créer des connexions privées entre les infrastructures et les centres de données Azure dans votre environnement local. Ceci constitue une option fiable pour le transfert de grandes quantités de données. Pour plus d’informations, consultez la [Documentation Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **Téléchargement « hors connexion » des données**. Si vous ne pouvez pas utiliser Azure ExpressRoute pour une raison ou une autre, vous pouvez utiliser le [service Azure Import/Export](../storage/common/storage-import-export-service.md) pour expédier des disques durs contenant vos données à un centre de données Azure. Vos données sont alors téléchargées vers des objets blob Azure Storage. Vous pouvez ensuite utiliser [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) ou [l’outil AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) pour copier des données des objets blob Azure Storage vers Data Lake Store.

  > [!NOTE]
  > Si vous utilisez le service Import/Export, la taille des fichiers sur les disques durs que vous envoyez au centre de données Azure ne doit pas être supérieure à 195 Go.
  >
  >

## <a name="process-data-stored-in-data-lake-store"></a>Traitement des données stockées dans Data Lake Store
Une fois que les données sont disponibles dans Data Lake Store, vous pouvez exécuter une analyse sur ces données à l’aide des applications de Big Data prises en charge. Actuellement, vous pouvez utiliser Azure HDInsight et Azure Data Lake Analytics pour exécuter des tâches d’analyse des données sur les données stockées dans Data Lake Store.

![Analyse des données dans Data Lake Store](./media/data-lake-store-data-scenarios/analyze-data.png "Analyse des données dans Data Lake Store")

Vous pouvez consulter les exemples suivants.

* [Créer un cluster HDInsight avec Data Lake Store comme stockage.](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-store"></a>Téléchargement de données à partir de Data Lake Store
Vous pouvez également être amené à télécharger ou à déplacer les données à partir d’Azure Data Lake Store dans certains cas, tels que :

* Déplacer des données vers d’autres référentiels pour créer une interface avec vos pipelines de traitement des données existantes. Par exemple, vous pouvez vouloir déplacer des données à partir de Data Lake Store vers Azure SQL Database ou vers SQL Server sur site.
* Télécharger des données sur votre ordinateur local pour le traitement dans des environnements IDE lors de la création de prototypes d’applications.

![Sortie des données à partir de Data Lake Store](./media/data-lake-store-data-scenarios/egress-data.png "Sortie des données à partir de Data Lake Store")

Dans ce cas, vous pouvez utiliser l’une des options suivantes :

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Vous pouvez également utiliser les méthodes suivantes pour écrire votre propre application/script pour télécharger les données à partir de Data Lake Store.

* [Interface CLI 2.0 inter-plateforme Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Kit de développement logiciel (SDK) .NET Azure Data Lake Store](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-store"></a>Visualisation des données dans Data Lake Store
Vous pouvez utiliser une combinaison de services pour créer des représentations visuelles des données stockées dans Data Lake Store.

![Visualisation des données dans Data Lake Store](./media/data-lake-store-data-scenarios/visualize-data.png "Visualisation des données dans Data Lake Store")

* Vous pouvez commencer par utiliser [Azure Data Factory pour déplacer les données de Data Lake Store vers Azure SQL Data Warehouse](../data-factory/copy-activity-overview.md)
* Ensuite, vous pouvez [intégrer Power BI à Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-integrate-power-bi.md) pour créer une représentation visuelle des données.
