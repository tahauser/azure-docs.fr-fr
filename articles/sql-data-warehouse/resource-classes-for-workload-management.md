---
title: Classes de ressources pour la gestion des charges de travail - Azure SQL Data Warehouse | Microsoft Docs
description: "Conseils d’utilisation des classes de ressources pour gérer la concurrence et les ressources de calcul en lien avec les requêtes dans Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: c76fb73c9beda93c407d1af29e157682c7fe58c0
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2018
---
# <a name="resource-classes-for-workload-management"></a>Classes de ressources pour la gestion des charges de travail
Conseils d’utilisation des classes de ressources pour gérer le nombre de requêtes concurrentes exécutées simultanément et les ressources de calcul en lien avec les requêtes dans Azure SQL Data Warehouse.
 
## <a name="what-is-workload-management"></a>Qu’est-ce que la gestion des charges de travail ?
La gestion des charges de travail permet d’optimiser les performances globales de toutes les requêtes. Une charge de travail bien paramétrée exécute les requêtes et les opérations de chargement avec efficacité, que celles-ci nécessitent beaucoup de ressources système ou exigent de nombreuses entrées et sorties. 

SQL Data Warehouse fournit des fonctionnalités de gestion des charges de travail pour les environnements multi-utilisateurs. Un entrepôt de données n’est pas destiné à des charges de travail mutualisées.

## <a name="what-are-resource-classes"></a>Que sont les classes de ressources ?
Les classes de ressources sont des limites de ressources prédéfinies qui régissent l’exécution des requêtes. SQL Data Warehouse limite les ressources de calcul pour chaque requête en fonction de la classe de ressources. 

Les classes de ressources vous aident à gérer les performances globales de la charge de travail de votre entrepôt de données. Une utilisation efficace des classes de ressources vous aide à gérer votre charge de travail en définissant des limites applicables au nombre de requêtes qui s’exécutent simultanément et de ressources de calcul attribuées à chaque requête. 

- Des classes de ressources plus petites utilisent moins de ressources de calcul mais permettent une concurrence plus importante de requêtes globales
- Des classes de ressources plus grandes fournissent davantage de ressources de calcul mais restreignent la concurrence des requêtes

Les classes de ressources sont conçues pour des activités de gestion et de manipulation de données. Certaines requêtes très complexes sont également bénéficiaires de jointures et de tris volumineux qui ont pour effet que le système exécute les requêtes en mémoire plutôt qu’en les déversant sur un disque.

Les instructions suivantes sont régies par des classes de ressources :

* INSERT-SELECT, UPDATE, DELETE
* SELECT (lors de l’interrogation des tables d’utilisateur)
* ALTER INDEX - REBUILD ou REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* Chargement de données
* Opérations de déplacement de données effectuées par le Service le déplacement des données (DMS)

> [!NOTE]  
> Les instructions SELECT sur des vues de gestion dynamique (DMV) ou d’autres vues système ne sont régies par aucune limite de concurrence. Les utilisateurs peuvent surveiller le système en toutes circonstances, quel que soit le nombre de requêtes en cours d’exécution dessus.
> 
> 

## <a name="static-and-dynamic-resource-classes"></a>Classes de ressources statiques et dynamiques

Il existe deux types de classes de ressources : statiques et dynamiques.

- Les **classes de ressources statiques** allouent la même quantité de mémoire, quel que soit le niveau de service actuel, qui est mesuré en nombre de [Data Warehouse Unit](what-is-a-data-warehouse-unit-dwu-cdwu.md). Cette allocation statique signifie que, sur des niveaux de service plus élevés, vous pouvez exécuter davantage de requêtes dans chaque classe de ressources.  Les classes de ressources statiques sont nommées staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70 et staticrc80. Ces classes de ressources sont idéalement adaptées aux solutions qui augmentent la classe de ressources pour obtenir des ressources de calcul supplémentaires.

- Les **classes de ressources dynamiques** allouent une quantité de mémoire qui varie en fonction du niveau de service en cours. Lors de l’augmentation de l’échelle du niveau de service, vos requêtes obtiennent automatiquement davantage de mémoire. Les classes de ressources dynamiques sont nommées smallrc, mediumrc, largerc et xlargerc. Ces classes de ressources sont idéalement adaptées aux solutions qui augmentent l’échelle de calcul pour obtenir des ressources supplémentaires. 

Les divers [niveaux de performance](performance-tiers.md) utilisent les mêmes noms de classe de ressources, mais ont des [spécifications de mémoire et de concurrence](performance-tiers.md) différentes. 


## <a name="assigning-resource-classes"></a>Attribution de classes de ressources

Les classes de ressources sont implémentées en assignant des utilisateurs à des rôles de base de données. Quand un utilisateur exécute une requête, celle-ci est exécutée avec la classe de ressources de l’utilisateur. Par exemple, quand un utilisateur est membre du rôle de base de données smallrc ou staticrc10, ses requêtes s’exécutent avec de petites quantités de mémoire. Quand un utilisateur de base de données est membre des rôles de base de données xlargerc ou staticrc80, ses requêtes s’exécutent avec de grandes quantités de mémoire. 

Pour augmenter la classe de ressources d’un utilisateur, utilisez la procédure stockée [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). 

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Pour réduire la classe de ressources, utilisez [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

La classe de ressources de l’administrateur de service est fixe et ne peut pas être modifiée.  L’administrateur de service est l’utilisateur créé pendant le processus d’approvisionnement.

> [!NOTE]
> Les utilisateurs ou groupes définis en tant qu’administrateur Active Directory sont également administrateurs de service.
>
>

### <a name="default-resource-class"></a>Classe de ressources par défaut
Par défaut, chaque utilisateur appartient à la petite classe de ressources, **smallrc**. 

### <a name="resource-class-precedence"></a>Précédence de classe de ressources
Les utilisateurs peuvent être membres de plusieurs classes de ressources. Quand un utilisateur appartient à plus d’une classe de ressources :

- les classes de ressources dynamiques ont la précédence sur les classes de ressources statiques. Par exemple, si un utilisateur est membre des classes de ressources mediumrc (dynamique) et staticrc80 (statique), les requêtes s’exécutent avec la classe de ressources mediumrc.
- Les classes de ressources plus grandes ont la précédence sur les classes de ressources plus petites. Par exemple, si un utilisateur est membre des classes de ressources mediumrc et largerc, les requêtes s’exécutent avec la classe de ressources largerc. De même, si un utilisateur est membre des classes de ressources staticrc20 et statirc80, les requêtes s’exécutent avec les allocations de ressources de staticrc80.

### <a name="queries-exempt-from-resource-classes"></a>Requêtes exemptes de classes de ressources
Certaines requêtes s’exécutent toujours dans la classe de ressources smallrc, même si l’utilisateur est membre d’une classe de ressources plus grande. Ces requêtes exemptes ne sont pas prises en compte pour la limite de concurrence. Par exemple, si la limite de concurrence est définie sur 16, de nombreux utilisateurs peuvent sélectionner des vues système sans que cela ait d’incidence sur les emplacements de concurrence disponibles.

Les instructions suivantes sont exemptes de classes de ressources et s’exécutent toujours dans smallrc :

* CREATE ou DROP TABLE
* ALTER TABLE ... SWITCH, SPLIT ou MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE ou DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER ou DROP USER
* CREATE, ALTER ou DROP PROCEDURE
* CREATE ou DROP VIEW
* INSERT VALUES
* SELECT à partir des affichages système et des DMV
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="recommendations"></a>Recommandations
Nous recommandons de créer un utilisateur dédié à l’exécution d’un type spécifique d’opérations de requête ou de chargement. Accordez ensuite à cet utilisateur une classe de ressources permanente au lieu de modifier fréquemment la classe de ressources. Étant donné que les classes de ressources statiques offrent un plus grand contrôle global de la charge de travail, nous suggérons également de les utiliser en priorité avant d’envisager l’utilisation de classes de ressources dynamiques.

### <a name="resource-classes-for-load-users"></a>Classes de ressources pour les utilisateurs de chargement
L’instruction `CREATE TABLE` utilise des index cluster columnstore par défaut. La compression de données dans un index columnstore est une opération nécessitant beaucoup de mémoire, et la sollicitation de la mémoire peut réduire la qualité de l’index. Par conséquent, il est très probable que vous deviez demander une classe de ressources supérieure lors du chargement des données. Pour garantir que les chargements disposent de suffisamment de mémoire, vous pouvez créer un utilisateur désigné pour exécuter les chargements, et assigner cet utilisateur à une classe de ressources supérieure.

La quantité de mémoire nécessaire au traitement des chargements dépend de la nature de la table chargée et de la taille des données. Pour plus d’informations sur la mémoire requise, voir [Optimiser la qualité du rowgroup pour columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Après avoir déterminé la mémoire requise, choisissez d’affecter l’utilisateur de chargement à une classe de ressources statique ou dynamique.

- Utilisez une classe de ressources statique lorsque la mémoire requise pour la table s’inscrit dans une plage spécifique. Les chargements s’exécutent avec une mémoire appropriée. Lorsque vous modifiez l’échelle de l’entrepôt de données, les charges n’ont pas besoin de davantage de mémoire. En utilisant une classe de ressources statique, les allocations de mémoire restent constantes. Cette cohérence conserve la mémoire et permet l’exécution concurrente de davantage de requêtes. Nous recommandons que les nouvelles solutions utilisent prioritairement les classes de ressources statiques, car celles-ci offrent davantage de contrôle.
- Utilisez une classe de ressources dynamique lorsque la mémoire requise pour la table varie considérablement. Des chargements peuvent nécessiter plus de mémoire que ce qu’offre le niveau actuel de DWU ou de cDWU. Par conséquent, la mise à l’échelle de l’entrepôt de données ajoute de la mémoire aux opérations de chargement, ce qui permet d’accélérer les chargements.

### <a name="resource-classes-for-queries"></a>Classes de ressources pour les requêtes

Certaines requêtes nécessitent beaucoup de ressources système, et d’autres pas.  

- Choisissez une classe de ressources dynamique lorsque les requêtes sont complexes, mais n’impliquent pas de concurrence élevée.  Par exemple, la génération de rapports quotidiens ou hebdomadaires entraîne un besoin occasionnel de ressources. Si les rapports résultent du traitement de grandes quantités de données, la mise à l’échelle de l’entrepôt de données fournit davantage de mémoire à la classe de ressources existante de l’utilisateur.
- Choisissez une classe de ressources statique lorsque les attentes de ressources varient pendant la journée. Par exemple, une classe de ressources statique fonctionne bien lorsque l’entrepôt de données est interrogé par de nombreuses personnes. Lors de la mise à l’échelle de l’entrepôt de données, la quantité de mémoire allouée à l’utilisateur ne change pas. Par conséquent, davantage de requêtes peuvent être exécutées en parallèle sur le système.

Le choix de l’allocation de mémoire appropriée dépend de nombreux facteurs tels que la quantité de données interrogées, la nature des schémas de table et les divers prédicats join, select et group. En règle général, si l’allocation de davantage de mémoire permet d’accélérer l’exécution des requêtes, cela limite la concurrence globale. Si la concurrence n’est pas un problème, une allocation excessive de mémoire n’affecte pas le débit. 

Pour ajuster les performances, utilisez des classes de ressources différentes. La section suivante fournit une procédure stockée permettant de déterminer la classe de ressources optimale.

## <a name="example-code-for-finding-the-best-resource-class"></a>Exemple de code pour rechercher la classe de ressources optimale
 
Vous pouvez utiliser la procédure stockée suivante pour déterminer la concurrence et l’allocation de mémoire par classe de ressources pour un SLO donné et la classe de ressources optimale pour les opérations ICC sur une table ICC non partitionnée pour une classe de ressources donnée :

Cette procédure stockée vise à :  
1. Déterminer la concurrence et l’allocation de mémoire par classe de ressources pour un SLO donné. L’utilisateur doit fournir la valeur NULL pour le schéma et le nom de table, comme indiqué dans cet exemple.  
2. Déterminer la classe de ressources optimale la plus proche pour les opérations ICC utilisant beaucoup de mémoire (chargement, copie de table, régénération d’index, etc.) sur une table ICC non partitionnée à une classe de ressources donnée. La procédure stockée utilise un schéma de table pour déterminer l’allocation de mémoire requise.

### <a name="dependencies--restrictions"></a>Dépendances et restrictions :
- Cette procédure stockée n’est pas conçue pour calculer la mémoire requise pour une table ICC partitionnée.    
- Cette procédure stockée ne prend pas en compte la mémoire requise pour la partie SELECT d’une instruction CTAS/INSERT-SELECT et part du principe qu’il s’agit d’une instruction SELECT.
- Cette procédure stockée utilise une table temporaire disponible dans la session où la procédure stockée a été créée.    
- Cette procédure stockée dépend des offres du moment (par exemple, configuration matérielle, configuration DMS). Si l’une d’elles change, cette procédure stockée ne fonctionne plus correctement.  
- Cette procédure stockée dépend de la limite de concurrence offerte existante. Si celle-ci change, cette procédure stockée ne fonctionne plus correctement.  
- Cette procédure stockée dépend des offres de classes de ressources existantes. Si celles-ci changent, cette procédure stockée ne fonctionne plus correctement.  

>  [!NOTE]  
>  Si vous n’obtenez pas de sortie après exécution de la procédure stockée avec les paramètres fournis, il se peut que vous soyez confronté à l’un des deux cas suivants : <br />1. Un paramètre DW contient une valeur SLO non valide. <br />2. Il n’existe aucune classe de ressources correspondant à l’opération ICC sur la table. <br />Par exemple, pour DW100, l’allocation de mémoire la plus élevée disponible est de 400 Mo si le schéma de table est suffisamment volumineux pour dépasser l’exigence de 400 Mo.
      
### <a name="usage-example"></a>Exemple d’utilisation :
Syntaxe :  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`  
1. @DWU: fournissez un paramètre NULL pour extraire la DWU active de la base de données DW ou fournissez une DWU prise en charge dans le format « DW100 »
2. @SCHEMA_NAME: fournissez le nom de schéma de la table
3. @TABLE_NAME: fournissez le nom de la table qui vous intéresse

Exemples d’exécution de cette procédure stockée :  
```sql  
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

L’instruction suivante crée la Table1 utilisée dans les exemples précédents.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Définition de la procédure stockée

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')
       
-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS 
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
     SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
   SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239) 
                                AND  co.max_length <= 32 
                                THEN COUNT(co.column_id) 
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239) 
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id) 
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as 
(
SELECT  CASE 
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
) 
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```




## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la gestion de la sécurité et des utilisateurs de base de données, consultez [Sécuriser une base de données dans SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Pour plus d’informations sur la façon dont des classes de ressources plus élevées peuvent améliorer la qualité des index cluster columnstore, voir [Optimiser la qualité du rowgroup pour columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
