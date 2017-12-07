---
title: "Créer des clés de substitution à l’aide d’IDENTITY | Microsoft Docs"
description: "Découvrez comment utiliser IDENTITY pour créer des clés de substitution sur vos tables."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: e10b58743fad5f7c2c4f00b51f06d4ec9bcb6768
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2017
---
# <a name="create-surrogate-keys-by-using-identity"></a>Créer des clés de substitution à l’aide d’IDENTITY
> [!div class="op_single_selector"]
> * [Vue d’ensemble][Overview]
> * [Types de données][Data Types]
> * [Distribuer][Distribute]
> * [Index][Index]
> * [Partition][Partition]
> * [Statistiques][Statistics]
> * [Temporaire][Temporary]
> * [Identité][Identity]
> 
> 

Nombreux sont les modélisateurs de données à aimer créer des clés de substitution sur leurs tables lorsqu’ils conçoivent des modèles d’entrepôt de données. Vous pouvez utiliser la propriété IDENTITY pour atteindre cet objectif de manière simple et efficace, sans affecter les performances de chargement. 

## <a name="get-started-with-identity"></a>Prise en main d’IDENTITY
Vous pouvez définir une table ayant la propriété IDENTITY lorsque vous créez la table à l’aide d’une syntaxe similaire à l’instruction suivante :

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1) NOT NULL
,   C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

Vous pouvez ensuite utiliser `INSERT..SELECT` pour remplir la table.

## <a name="behavior"></a>Comportement
La propriété IDENTITY est conçue pour augmenter la taille des instances sur toutes les distributions de l’entrepôt de données sans affecter les performances de chargement. Par conséquent, l’implémentation d’IDENTITY est adaptée pour atteindre ces objectifs. Cette section met en évidence les nuances de l’implémentation pour vous aider à mieux les comprendre.  

### <a name="allocation-of-values"></a>Allocation de valeurs
La propriété IDENTITY ne garantit pas l’ordre dans lequel les valeurs de substitution sont alloués, ce qui reflète le comportement de SQL Server et d’Azure SQL Database. Toutefois, dans Azure SQL Data Warehouse, l’absence de garantie est plus marquée. 

L’exemple suivant en est une illustration :

```sql
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)    NOT NULL
,   C2 VARCHAR(30)              NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

Dans l’exemple précédent, les deux lignes sont arrivées dans la distribution 1. La première ligne a la valeur de substitution 1 dans la colonne `C1`, et la deuxième la valeur de substitution 61. Ces deux valeurs ont été générées par la propriété IDENTITY. Toutefois, l’allocation des valeurs n’est pas contiguë. Ce comportement est normal.

### <a name="skewed-data"></a>Données décalées 
La plage de valeurs pour le type de données est répartie uniformément entre les distributions. Si une table distribuée subit un décalage de données, la plage de valeurs disponibles pour le type de données peut être épuisée prématurément. Par exemple, si toutes les données se terminent dans une seule distribution, la table a accès à seulement un sixième des valeurs du type de données. Pour cette raison, la propriété IDENTITY se limite aux types de données `INT` et `BIGINT` uniquement.

### <a name="selectinto"></a>SELECT .. INTO
Lorsqu’une colonne IDENTITY existante est sélectionnée dans une nouvelle table, la nouvelle colonne hérite de la propriété IDENTITY, sauf si une des conditions suivantes est remplie :
- L’instruction SELECT contient une jointure.
- Plusieurs instructions SELECT sont jointes à l’aide d’UNION.
- La colonne IDENTITY est répertoriée plusieurs fois dans la liste SELECT.
- La colonne SELECT fait partie d’une expression.
    
Si l’une de ces conditions est vraie, la colonne est créée comme étant NOT NULL au lieu d’hériter de la propriété IDENTITY.

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT
CREATE TABLE AS SELECT (CTAS) suit le même comportement SQL Server que celui documenté pour SELECT..INTO. Toutefois, vous ne pouvez pas spécifier de propriété IDENTITY dans la définition de la colonne de la partie `CREATE TABLE` de l’instruction. Vous ne pouvez pas non plus utiliser la fonction IDENTITY dans la partie `SELECT` de l’instruction CTAS. Pour remplir une table, vous devez utiliser `CREATE TABLE` pour définir la table suivie de l’instruction `INSERT..SELECT`.

## <a name="explicitly-insert-values-into-an-identity-column"></a>Insérer explicitement des valeurs dans une colonne IDENTITY 
SQL Data Warehouse prend en charge la syntaxe `SET IDENTITY_INSERT <your table> ON|OFF`. Vous pouvez utiliser cette syntaxe pour insérer explicitement des valeurs dans la colonne IDENTITY.

Nombreux sont les modélisateurs de données à aimer utiliser des valeurs négatives prédéfinies pour certaines lignes dans leurs dimensions. Un exemple est la ligne -1 ou « membre inconnu ». 

Le script suivant montre comment ajouter explicitement cette ligne à l’aide de SET IDENTITY_INSERT :

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT  *
FROM    dbo.T1
;
```    

## <a name="load-data-into-a-table-with-identity"></a>Charger des données dans une table avec IDENTITY

La présence de la propriété IDENTITY a des conséquences sur votre code de chargement des données. Cette section met en évidence certains modèles de base pour charger des données dans les tables à l’aide d’IDENTITY. 

### <a name="load-data-with-polybase"></a>Charger des données avec PolyBase
Pour charger des données dans une table et générer une clé de substitution à l’aide d’IDENTITY, créez la table et utilisez l’instruction INSERT... SELECT ou INSERT... VALEURS pour effectuer le chargement.

L’exemple suivant met en évidence le modèle de base :
 
```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(   C1 INT IDENTITY(1,1)
,   C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT  C2
FROM    ext.T1
;

SELECT  *
FROM    dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE] 
> Il n’est pas possible d’utiliser `CREATE TABLE AS SELECT` actuellement lors du chargement des données dans une table comportant une colonne IDENTITY.
> 

Pour plus d’informations sur le chargement de données à l’aide de l’outil Programme de copie en bloc (BCP), consultez les articles suivants :

- [Charger avec PolyBase][]
- [Meilleures pratiques pour PolyBase][]

### <a name="load-data-with-bcp"></a>Chargement des données avec BCP
BCP est un outil de ligne de commande que vous pouvez utiliser pour charger des données dans SQL Data Warehouse. Un de ses paramètres (-E) contrôle le comportement de l’outil BCP lors du chargement des données dans une table avec une colonne IDENTITY. 

Lorsque le paramètre -E est spécifié, les valeurs contenues dans le fichier d’entrée pour la colonne comportant IDENTITY sont conservées. Si -E n’est *pas* spécifié, les valeurs de cette colonne sont ignorées. Si la colonne IDENTITY n’est pas incluse, les données sont chargées normalement. Les valeurs sont générées en fonction de la stratégie d’incrémentation et de valeur initiale de la propriété.

Pour plus d’informations sur le chargement de données à l’aide de l’outil BCP, consultez les articles suivants :

- [Charger avec BCP][]
- [BCP dans MSDN][]

## <a name="catalog-views"></a>Affichages catalogue
SQL Data Warehouse prend en charge l’affichage catalogue `sys.identity_columns`. Cet affichage peut être utilisé pour identifier une colonne comportant la propriété IDENTITY.

Pour vous aider à mieux comprendre la structure de la base de données, cet exemple montre comment intégrer `sys.identity_columns` à d’autres affichages catalogue du système :

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>Limitations
La propriété IDENTITY ne peut pas être utilisée dans les scénarios suivants :
- Où le type de données de colonne n’est pas INT ou BIGINT
- Où la colonne est également la clé de distribution
- Où la table est une table externe 

Les fonctions associées suivantes ne sont pas prises en charge dans SQL Data Warehouse :

- [IDENTITY()][]
- [@@IDENTITY][]
- [SCOPE_IDENTITY][]
- [IDENT_CURRENT][]
- [IDENT_INCR][]
- [IDENT_SEED][]
- [DBCC CHECK_IDENT()][]

## <a name="tasks"></a>Tâches

Cette section fournit un exemple de code que vous pouvez utiliser pour effectuer des tâches courantes, lorsque vous travaillez avec des colonnes IDENTITY.

> [!NOTE] 
> La colonne C1 est la colonne IDENTITY dans toutes les tâches suivantes.
> 
 
### <a name="find-the-highest-allocated-value-for-a-table"></a>Rechercher la valeur allouée la plus élevée d’une table
Utilisez la fonction `MAX()` pour déterminer la valeur maximale allouée d’une table distribuée :

```sql
SELECT  MAX(C1)
FROM    dbo.T1
``` 

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>Rechercher la valeur initiale et l’incrément de la propriété IDENTITY
Vous pouvez utiliser les affichages catalogue pour découvrir les valeurs de configuration de la valeur initiale et de l’incrément d’identité d’une table à l’aide de la requête suivante : 

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value 
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur le développement de tables, consultez les articles [Vue d’ensemble des tables][Overview], [Types de données de table][Data Types], [Distribution d’une table][Distribute], [Indexation d’une table][Index], [Partitionnement d’une table][Partition] et [Tables temporaires][Temporary]. 
* Pour plus d’informations sur les bonnes pratiques, consultez [Bonnes pratiques pour SQL Data Warehouse][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Identity]: ./sql-data-warehouse-tables-identity.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

[Charger avec BCP]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-with-bcp/
[Charger avec PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase/
[Meilleures pratiques pour PolyBase]: https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide/


<!--MSDN references-->
[Identity property]: https://msdn.microsoft.com/library/ms186775.aspx
[sys.identity_columns]: https://msdn.microsoft.com/library/ms187334.aspx
[IDENTITY()]: https://msdn.microsoft.com/library/ms189838.aspx
[@@IDENTITY]: https://msdn.microsoft.com/library/ms187342.aspx
[SCOPE_IDENTITY]: https://msdn.microsoft.com/library/ms190315.aspx
[IDENT_CURRENT]: https://msdn.microsoft.com/library/ms175098.aspx
[IDENT_INCR]: https://msdn.microsoft.com/library/ms189795.aspx
[IDENT_SEED]: https://msdn.microsoft.com/library/ms189834.aspx
[DBCC CHECK_IDENT()]: https://msdn.microsoft.com/library/ms176057.aspx

[BCP dans MSDN]: https://msdn.microsoft.com/library/ms162802.aspx
  

<!--Other Web references-->  
