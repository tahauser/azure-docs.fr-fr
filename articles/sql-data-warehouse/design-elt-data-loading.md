---
title: Concevoir des processus ELT pour Azure SQL Data Warehouse | Microsoft Docs
description: "Combinez les technologies de déplacement des données vers Azure et de chargement des données dans SQL Data Warehouse pour concevoir un processus d’extraction, de chargement et de transformation (ELT) pour Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 12/12/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: e94dca69c77c46034e318205279be5188e1371f5
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Conception de processus ELT pour Azure SQL Data Warehouse

Combinez les technologies de déplacement des données dans le stockage Azure et de chargement des données dans SQL Data Warehouse pour concevoir un processus d’extraction, de chargement et de transformation (ELT) pour Azure SQL Data Warehouse. Cet article présente les technologies qui prennent en charge le chargement avec PolyBase, puis met l’accent sur la conception d’un processus ELT utilisant PolyBase avec T-SQL pour charger des données dans SQL Data Warehouse à partir du stockage Azure.

## <a name="what-is-elt"></a>ELT, qu’est-ce que ça veut dire ?

ELT (ou extraire, charger et transformer) est un processus qui permet de déplacer des données d’un système source vers un entrepôt de données de destination. Ce processus est exécuté sur une base régulière, par exemple toutes les heures ou tous les jours, pour que les données récemment générées soient toujours transférées dans l’entrepôt de données. La solution idéale pour transférer des données à partir de leur source vers un entrepôt de données consiste à développer un processus ELT qui utilise PolyBase pour charger des données dans SQL Data Warehouse.

Le processus ELT commence par charger les données, puis il les transforme, alors que le processus ETL (extraction, transformation et chargement) transforme les données avant de les charger. Le processus ELT coûte moins cher que le processus ETL, car vous n’avez pas besoin de fournir vos propres ressources pour transformer les données avant leur chargement. Lorsque vous utilisez SQL Data Warehouse, le processus ELT s’appuie sur le système MPP pour effectuer les transformations.

Il existe de nombreuses manières d’implémenter un processus ELT pour SQL Data Warehouse. Voici les étapes de base :  

1. Extrayez les données sources dans des fichiers texte.
2. Placez les données dans le stockage Blob Azure ou Azure Data Lake Store.
3. Préparez les données pour le chargement.
2. Chargez les données dans les tables de mise en lots SQL Data Warehouse à l’aide de PolyBase.
3. Transformez les données.
4. Insérez les données dans des tables de production.


Pour suivre un didacticiel sur le chargement, consultez l’article [Utiliser PolyBase pour charger des données du Stockage Blob Azure dans Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).

Pour en savoir plus, voir l’article de blog [Loading patterns](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/) (Modèles de charge). 

## <a name="options-for-loading-with-polybase"></a>Options de chargement avec PolyBase

PolyBase est une technologie qui accède aux données en dehors de la base de données via le langage T-SQL. C’est l’outil idéal pour charger des données dans SQL Data Warehouse. Avec PolyBase, les données sont chargées en parallèle à partir de la source de données directement sur les nœuds de calcul. 

Pour charger des données avec PolyBase, vous pouvez utiliser l’une des options de chargement suivantes.

- [PolyBase avec T-SQL](load-data-from-azure-blob-storage-using-polybase.md) fonctionne bien lorsque vos données se trouvent dans le stockage Blob Azure ou dans Azure Data Lake Store. Il vous offre un contrôle optimal sur le processus de chargement, mais nécessite également que vous définissiez des objets de données externes. Les autres méthodes définissent ces objets en arrière-plan pendant que vous mappez les tables sources vers les tables de destination.  Pour orchestrer les chargements T-SQL, vous pouvez utiliser Azure Data Factory, SSIS ou les fonctions Azure. 
- [PolyBase avec SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md) fonctionne bien lorsque vos données sources se trouvent dans SQL Server, que ce soit SQL Server sur site ou dans le cloud. SSIS définit le mappage de la table « source vers destination » et orchestre aussi le chargement. Si vous disposez déjà de packages SSIS, vous pouvez modifier les packages pour travailler avec le nouvel entrepôt de données de destination. 
- [PolyBase avec Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) est un autre outil d’orchestration.  Il définit un pipeline et planifie les travaux. 

### <a name="polybase-external-file-formats"></a>Formats des fichiers externes PolyBase

PolyBase charge les données à partir de fichiers texte encodés avec UTF-8 et UTF-16. En plus des fichiers texte délimités, il charge des données à partir des fichiers aux formats Hadoop, RC, ORC et Parquet. PolyBase peut charger des données à partir de fichiers compressés Gzip et Snappy. PolyBase ne prend actuellement pas en charge le codage ASCII étendu, le format de largeur fixe et les formats imbriqués tels que WinZip, JSON et XML.

### <a name="non-polybase-loading-options"></a>Options de chargement non-PolyBase
Si vos données ne sont pas compatibles avec PolyBase, vous pouvez utiliser l’outil [bcp](sql-data-warehouse-load-with-bcp.md) ou l’[API SQLBulkCopy](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). L’outil bcp charge directement dans SQL Data Warehouse sans passer par le stockage Blob Azure et est destiné uniquement aux petits chargements. Notez que les performances de chargement de ces options sont beaucoup plus lentes qu’avec PolyBase. 


## <a name="extract-source-data"></a>Extraire les données sources

L’extraction des données à partir de votre système source dépend de la source.  L’objectif est de déplacer les données vers des fichiers texte délimités. Si vous utilisez SQL Server, vous pouvez utiliser l’[outil en ligne de commande bcp](/sql/tools/bcp-utility) pour exporter les données.  

## <a name="land-data-to-azure-storage"></a>Charger des données dans le stockage Azure

Pour charger les données dans le stockage Azure, vous pouvez les déplacer dans le [stockage Blob Azure](../storage/blobs/storage-blobs-introduction.md) ou dans [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). Quel que soit l’emplacement choisi, les données doivent être stockées dans des fichiers texte. PolyBase peut effectuer le chargement à partir des deux emplacements.

Voici des outils et services, que vous pouvez utiliser pour déplacer des données dans le stockage Azure.

- Le service [Azure ExpressRoute](../expressroute/expressroute-introduction.md) améliore le débit, les performances et la prévisibilité du réseau. ExpressRoute est un service qui achemine vos données via une connexion privée dédiée vers Azure. Les connexions ExpressRoute n’acheminent pas vos données via le réseau Internet public. Elles offrent davantage de fiabilité, des vitesses supérieures, des latences inférieures et une sécurité renforcée par rapport aux connexions publiques sur Internet.
- L’[utilitaire AZCopy](../storage/common/storage-use-azcopy.md) déplace les données vers le stockage Azure via l’Internet public. Il fonctionne si la taille de vos données ne dépasse pas les 10 To. Pour effectuer des chargements réguliers avec AZCopy, testez la vitesse du réseau pour voir si elle est acceptable. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) dispose d’une passerelle que vous pouvez installer sur votre serveur local. Ensuite, vous pouvez créer un pipeline pour déplacer des données à partir de votre serveur local vers le stockage Azure.

Pour plus d’informations, consultez l’article [Déplacer des données vers et à partir du stockage Azure](../storage/common/storage-moving-data.md).


## <a name="prepare-data"></a>Préparer les données

Avant de pouvoir charger les données de votre compte de stockage dans SQL Data Warehouse, vous devrez peut-être les préparer et les nettoyer. Vous pouvez préparer vos données pendant qu’elles sont dans la source, pendant que vous exportez les données dans des fichiers texte ou une fois que les données se trouvent dans le stockage Azure.  Plus vous les préparez tôt, plus ce sera facile.  

### <a name="define-external-tables"></a>Définir des tables externes
Avant de pouvoir charger des données, vous devez définir des tables externes dans votre entrepôt de données. PolyBase utilise des tables externes pour définir les données dans le stockage Azure et y accéder. La table externe est similaire à une table normale. La principale différence réside dans le fait que la table externe pointe vers des données stockées en dehors de l’entrepôt de données. 

La définition des tables externes implique de spécifier la source des données, le format des fichiers texte et les définitions de la table. Voici les rubriques sur la syntaxe T-SQL dont vous aurez besoin :
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Pour voir un exemple de création d’objets externes, consultez l’étape [Créer des tables externes](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) du didacticiel sur le chargement.

### <a name="format-text-files"></a>Formater les fichiers texte

Une fois les objets externes définis, vous devez aligner les lignes des fichiers texte avec la table externe et la définition du format de fichier. Les données de chaque ligne du fichier texte doivent être alignées avec la définition de la table.

Pour formater les fichiers texte :

- Si vos données proviennent d’une source non relationnelle, vous devez les transformer en lignes et en colonnes. Que les données proviennent d’une source relationnelle ou non relationnelle, elles doivent être transformées pour être alignées avec les définitions des colonnes pour la table dans laquelle vous souhaitez les charger. 
- Formatez les données dans le fichier texte pour les aligner avec les types de colonnes et de données dans la table de destination SQL Data Warehouse. Un décalage entre les types de données dans les fichiers texte externes et la table de l’entrepôt de données cause le rejet des lignes lors du chargement.
- Séparez les champs dans le fichier texte à l’aide d’une marque de fin.  Assurez-vous d’utiliser un caractère ou une séquence de caractères qui ne se trouve pas dans votre source de données. Utilisez la marque de fin que vous avez spécifiée avec l’instruction [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Charger dans une table de mise en lots
Pour transférer des données dans l’entrepôt de données, il est conseillé de commencer par charger les données dans une table de mise en lots. En utilisant une table de mise en lots, vous pouvez gérer les erreurs sans interférer avec les tables de production. Vous évitez ainsi d’exécuter des opérations de restauration sur la table de production. Une table de mise en lots vous donne également la possibilité d’utiliser SQL Data Warehouse pour exécuter des transformations avant d’insérer les données dans des tables de production.

Pour effectuer des chargements avec T-SQL, exécutez l’instruction T-SQL [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md). Cette commande permet d’insérer les résultats d’une instruction SELECT dans une nouvelle table. Lorsque l’instruction effectue une sélection à partir d’une table externe, elle importe les données externes. 

Dans l’exemple suivant, ext.Date est une table externe. Toutes les lignes sont importées dans une nouvelle table appelée dbo.Date.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>Transformer les données
Pendant que les données se trouvent dans la table de mise en lots, effectuez les transformations requises par votre charge de travail. Déplacez ensuite les données dans une table de production.

## <a name="insert-data-into-production-table"></a>Insérer des données dans une table de production

L’instruction INSERT INTO... SELECT déplace les données depuis la table de mise en lots vers la table permanente. 

Lorsque vous concevez un processus ETL, commencez par exécuter le processus sur un petit échantillon. Essayez d’extraire 1 000 lignes de la table dans un fichier, déplacez-le vers Azure, puis essayez de le charger dans une table de mise en lots. 

## <a name="partner-loading-solutions"></a>Solutions de chargement des partenaires
La plupart de nos partenaires proposent des solutions de chargement. Pour en savoir plus, consultez la liste de nos [partenaires de solutions](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des instructions sur le chargement, consultez [Meilleures pratiques de chargement de données](guidance-for-loading-data.md).


