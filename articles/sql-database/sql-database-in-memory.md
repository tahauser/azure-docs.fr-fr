---
title: Technologies Azure SQL Database en mémoire | Microsoft Docs
description: Les technologies Azure SQL Database en mémoire améliorent considérablement les performances des charges de travail transactionnelles et analytiques.
services: sql-database
author: jodebrui
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 11/16/2017
ms.author: jodebrui
ms.openlocfilehash: 107df78f0ec6ce924785f5027958ee66f2a86c7c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="optimize-performance-by-using-in-memory-technologies-in-sql-database"></a>Optimisation des performances à l’aide des technologies en mémoire dans SQL Database

À l’aide des technologies en mémoire dans Azure SQL Database, vous pouvez obtenir des améliorations des performances pour différentes charges de travail : transactionnelles (traitement transactionnel en ligne (OLTP)), analytiques (traitement analytique en ligne (OLAP)) et mixtes (traitement transactionnel/analytique hybride (HTAP)). Les technologies en mémoire vous aident également à réduire les coûts grâce à un traitement plus efficace des requêtes et des transactions. En général, il n’est pas nécessaire de mettre à jour le niveau de tarification de la base de données pour obtenir des gains de performances. Dans certains cas, vous pourriez même être en mesure de réduire le niveau de tarification, tout en bénéficiant d’une amélioration des performances grâce aux technologies en mémoire.

Voici deux exemples illustrant comment la technologie OLTP en mémoire a permis d’améliorer les performances :

- À l’aide de l’OLTP en mémoire, [Quorum Business Solutions a pu doubler leur charge de travail tout en améliorant les DTU de 70 %](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database).
    - DTU signifie *unité de débit de base de données* et inclut une mesure de la consommation des ressources.
- La vidéo suivante montre une amélioration significative de la consommation de ressources avec un exemple de charge de travail : [Vidéo OLTP en mémoire dans Azure SQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB).
    - Pour plus d’informations, voir le billet de blog : [Billet de blog OLTP en mémoire dans Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

Les technologies en mémoire sont disponibles dans toutes les bases de données du niveau Premium, notamment les bases de données dans des pools élastiques Premium.

La vidéo suivante explique les gains de performance potentiels que peuvent apporter les technologies en mémoire dans l’Azure SQL Database. N’oubliez pas que le gain de performance que vous remarquez dépend de nombreux facteurs, notamment de la nature de la charge de travail et des données, du modèle d’accès de la base de données, etc.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-In-Memory-Technologies/player]
>
>

L’Azure SQL Database comprend les technologies en mémoire suivantes :

- *L’OLTP en mémoire* augmente le débit et réduit la latence de traitement des transactions. Les scénarios qui bénéficient de l’OLTP en mémoire sont : le traitement de transactions haut débit, notamment les données commerciales et de jeux, l’ingestion de données d’événements ou d’appareils IoT, la mise en cache, le chargement de données, les tables temporaires et les scénarios de variables de table.
- Les *index columnstore en cluster* réduisent l’encombrement de stockage (jusqu'à 10 fois) et améliorent les performances des requêtes d’analyse et de création de rapports. Vous pouvez les utiliser avec des tables de faits dans vos mini-Data Warehouses pour faire tenir plus de données dans votre base de données et optimiser les performances. Vous pouvez également les utiliser avec des données historiques dans votre base de données opérationnelles pour archiver et être en mesure d’interroger jusqu’à 10 fois plus de données.
- Les *index columnstore sans cluster* pour HTAP vous aident à obtenir un aperçu en temps réel de votre activité en interrogeant la base de données opérationnelle directement, sans avoir à exécuter de processus d’extraction, de transformation et de chargement (ETL) coûteux et à attendre que l’entrepôt de données se remplisse. Les index columnstore sans cluster permettent une exécution très rapide des requêtes d’analyse sur la base de données OLTP, tout en réduisant l’impact sur la charge de travail opérationnelle.
- Vous pouvez également disposer de la combinaison d’une table à mémoire optimisée et d’un index columnstore. Cette combinaison permet d’effectuer un traitement transactionnel très rapide, et permet d’exécuter *simultanément* et très rapidement des requêtes Analytics sur les mêmes données.

Les index columnstore et la technologie OLTP en mémoire sont inclus dans le produit SQL Server respectivement depuis 2012 et 2014. Azure SQL Database et SQL Server partagent la même implémentation des technologies en mémoire. À l’avenir, les nouvelles fonctionnalités pour ces technologies seront publiées dans Azure SQL Database avant d’être publiées dans SQL Server.

Cette rubrique décrit des aspects de l’OLTP en mémoire et des index columnstore qui sont spécifiques à Azure SQL Database, et inclut également des exemples :
- Vous verrez l’impact de ces technologies sur le stockage et les limites de taille des données.
- Vous verrez ensuite comment gérer le déplacement de bases de données qui exploitent ces technologies entre les différents niveaux tarifaires.
- Vous verrez deux exemples qui illustrent l’utilisation d’OLTP en mémoire, ainsi que les index columnstore dans la base de données SQL Azure.

Pour plus d’informations, consultez les ressources suivantes.

Informations approfondies sur ces technologies :

- [Présentation de l’OLTP en mémoire et scénarios d’utilisation](https://msdn.microsoft.com/library/mt774593.aspx) (avec notamment des références à des études de cas client et des informations de prise en main)
- [Documentation pour l’OLTP In-Memory](http://msdn.microsoft.com/library/dn133186.aspx)
- [Description des index columnstore](https://msdn.microsoft.com/library/gg492088.aspx)
- Traitement hybride transactionnel et analytique (HTAP), également appelé [analytique opérationnelle en temps réel](https://msdn.microsoft.com/library/dn817827.aspx)

Introduction rapide sur l’OLTP en mémoire : [Démarrage rapide 1 : Technologies OLTP en mémoire pour des performances T-SQL plus rapides](http://msdn.microsoft.com/library/mt694156.aspx) (un autre article pour vous aider à commencer)

Vidéos détaillées sur les technologies :

- [OLTP en mémoire dans Azure DQL Database](https://channel9.msdn.com/Shows/Data-Exposed/In-Memory-OTLP-in-Azure-SQL-DB) (avec notamment une démonstration des avantages de performances et les étapes à suivre pour reproduire ces résultats vous-même)
- [Vidéos sur l’OLTP en mémoire : qu’est-ce que c’est, et comment l’utiliser](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/03/in-memory-oltp-video-what-it-is-and-whenhow-to-use-it/)
- [Index Columnstore : vidéos d’analyse en mémoire d’Ignite 2016](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/10/04/columnstore-index-in-memory-analytics-i-e-columnstore-index-videos-from-ignite-2016/)

## <a name="storage-and-data-size"></a>Taille des données et du stockage

### <a name="data-size-and-storage-cap-for-in-memory-oltp"></a>Seuil de la taille des données et du stockage pour l’OLTP en mémoire

l’OLTP en mémoire inclut des tables optimisées en mémoire, qui sont utilisées pour stocker des données de l’utilisateur. Le volume de ces tables doit tenir dans la mémoire. Étant donné que vous gérez la mémoire directement dans le service SQL Database, nous disposons du concept de quota pour les données utilisateur. Ce concept est appelé *stockage OLTP en mémoire*.

Chaque niveau tarifaire de base de données autonome pris en charge et chaque niveau tarifaire de pool élastique inclut une certaine quantité de stockage OLTP en mémoire. Au moment de l’écriture de cet article, vous obtenez un gigaoctet de stockage pour 125 unités de transaction de base de données (DTU) ou unités de transaction de base de données élastique (eDTU). Pour plus d’informations, consultez [Limites des ressources](sql-database-resource-limits.md).

Les éléments suivants sont pris en compte dans votre plafond de stockage OLTP en mémoire :

- Lignes de données utilisateur actives dans des tables optimisées en mémoire et variables de table. Notez que les anciennes versions des lignes ne comptent pas dans le seuil.
- Index de tables optimisées en mémoire.
- Coûts de fonctionnement des opérations ALTER TABLE.

Si vous atteignez le seuil, vous recevrez une erreur de quota et vous ne serez plus en mesure d’insérer ou de mettre à jour des données. Pour atténuer cette erreur, supprimez des données ou augmentez le niveau tarifaire de la base de données ou du pool.

Pour plus d’informations sur la surveillance de l’utilisation du stockage OLTP en mémoire et la configuration des alertes lorsque le seuil est presque atteint, consultez [Surveiller le stockage en mémoire](sql-database-in-memory-oltp-monitoring.md).

#### <a name="about-elastic-pools"></a>À propos des pools élastiques

Avec les pools élastiques, le stockage OLTP en mémoire est partagé entre toutes les bases de données dans le pool. Par conséquent, l’utilisation dans une base de données peut potentiellement affecter les autres bases de données. Voici deux solutions :

- Configurez un nombre d’eDTU maximal pour les bases de données inférieur au nombre d’eDTU du pool dans son ensemble. Cette valeur maximale limite l’utilisation du stockage OLTP en mémoire, dans n’importe quelle base de données du pool, à la taille qui correspond au nombre d’eDTU.
- Configurez un nombre d’eDTU minimal supérieur à 0. Ce minimum garantit que chaque base de données dans le pool possède la quantité de stockage OLTP en mémoire disponible qui correspond à l’eDTU-Min configuré.

### <a name="data-size-and-storage-for-columnstore-indexes"></a>Taille des données et stockage pour les index columnstore

Le volume des index columntore ne doit pas forcément tenir dans la mémoire. Par conséquent, le seul seuil de taille des index est la taille de base de données globale maximale décrite dans l’article [Niveaux de service SQL Database](sql-database-service-tiers.md).

Lors de l’utilisation d’index columnstore en cluster, la compression en colonnes est utilisée pour le stockage de table de base. Cette compression peut réduire considérablement l’encombrement de stockage des données utilisateur, ce qui signifie que vous pouvez entrer davantage de données dans la base de données. De plus, la compression peut être accrue d’avantage avec [la compression d’archivage en colonnes](https://msdn.microsoft.com/library/cc280449.aspx#Using Columnstore and Columnstore Archive Compression). Le taux de compression que vous pouvez obtenir dépend de la nature des données, mais une compression égale à 10 fois n’est pas rare.

Par exemple, si vous disposez d’une base de données avec une taille maximale de 1 téraoctet (To), et que vous atteignez une compression de 10 fois à l’aide d’index columntore, vous pouvez afficher un total de 10 To de données utilisateur dans la base de données.

Lors de l’utilisation d’index columnstore sans cluster, la table de base est toujours stockée au format rowstore traditionnel. Par conséquent, les économies en stockage ne sont pas aussi importantes qu’avec des index columnstore en cluster. Toutefois, si vous remplacez un nombre d’index sans cluster traditionnels par un index columntore unique, vous pouvez toujours voir une économie globale d’espace de stockage pour la table.

## <a name="moving-databases-that-use-in-memory-technologies-between-pricing-tiers"></a>Déplacement de bases de données utilisant des technologies en mémoire entre différents niveaux tarifaires

Vous ne rencontrerez aucune incompatibilité ou aucun autre problème lorsque vous effectuerez une mise à niveau vers un niveau tarifaire supérieur (ex : Édition Standard vers Édition Premium). Les ressources et les fonctionnalités disponibles ne font qu’augmenter.

Cependant la rétrogradation du niveau de tarification peut affecter négativement votre base de données. L’impact est particulièrement évident lorsque vous rétrogradez à partir de Premium pour Base ou Standard alors que votre base de données contient des objets de l’OLTP en mémoire. Les tables à mémoire optimisée et les index columnstore sont indisponibles après la rétrogradation (même si elles restent visibles). Les mêmes considérations s’appliquent lors de la réduction du niveau tarifaire d’un pool élastique ou du déplacement d’une base de données avec des technologies en mémoire vers un pool élastique Standard ou de base.

### <a name="in-memory-oltp"></a>OLTP In-Memory.

*Rétrogradation vers le niveau De base/Standard* : l’OLTP en mémoire n’est pas pris en charge dans des bases de données de niveau standard ou de base. En outre, il n’est pas possible de déplacer une base de données qui comporte des objets OLTP en mémoire vers un niveau standard ou de base.

Vous pouvez comprendre par programmation si une base de données spécifique prend en charge l’OLTP en mémoire. Vous pouvez exécuter la requête Transact-SQL suivante :

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```

Si la requête renvoie **1**, l’OLTP en mémoire est pris en charge dans cette base de données.

Avant de rétrograder la base de données vers un niveau standard/de base, supprimez toutes les tables à mémoire optimisée et tous les types de table, ainsi que tous les modules T-SQL compilés en mode natif. Les requêtes suivantes identifient tous les objets devant être supprimés avant qu’une base de données puisse être rétrogradée vers Standard/de Base :

```
SELECT * FROM sys.tables WHERE is_memory_optimized=1
SELECT * FROM sys.table_types WHERE is_memory_optimized=1
SELECT * FROM sys.sql_modules WHERE uses_native_compilation=1
```

*Rétrogradation vers un niveau Premium inférieur* : les données dans des tables à mémoire optimisée doivent tenir dans le stockage OLTP en mémoire qui est associé au niveau tarifaire de la base de données ou disponible dans le pool élastique. L’opération échoue si vous tentez de réduire le niveau tarifaire ou de déplacer la base de données dans un pool qui ne dispose pas d’un stockage OLTP en mémoire suffisant.

### <a name="columnstore-indexes"></a>Index Columnstore

*Rétrogradation vers Base ou Standard* : les index columnstore ne sont pris en charge que sur le niveau tarifaire Premium et non sur les niveaux Standard ou Basic. Lors de la rétrogradation de votre base de données vers Standard ou Base, l’index columnstore devient indisponible. Le système maintient l’index columnstore, mais il ne tire jamais profit de l’index. Si vous mettez à niveau ultérieurement vers Premium, l’index columnstore est immédiatement prêt à être exploité à nouveau.

Si vous disposez d’un index columnstore **mis en cluster**, la totalité de la table devient indisponible après la rétrogradation de la couche. Par conséquent, nous vous recommandons d’annuler tous les index columnstore *mis en index* avant de rétrograder votre base de données sous le niveau Premium.

*Rétrogradation vers un niveau Premium inférieur* : cette opération réussit tant que la taille de base de données dans son ensemble est inférieure à la taille de base de données maximale correspondant au niveau tarifaire cible ou au stockage disponible dans le pool élastique. Les index columnstore n’ont aucune incidence spécifique.


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Installer l’exemple In-Memory OLTP

Vous pouvez créer l’exemple de base de données AdventureWorksLT en quelques clics dans le [portail Azure](https://portal.azure.com/). Les étapes de cette section expliquent comment enrichir votre base de données AdventureWorksLT d’objets OLTP en mémoire et démontrent les avantages au niveau des performances.

Pour une démonstration plus simple mais intéressante des performances de l’OLTP en mémoire, consultez :

- Version : [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Code source : [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Procédure d’installation :

1. Dans le [portail Azure](https://portal.azure.com/), créez une base de données Premium sur un serveur. Définissez comme valeur **Source** l’exemple de base de données AdventureWorksLT. Pour obtenir des instructions détaillées, consultez [Créer votre première base de données SQL Azure](sql-database-get-started-portal.md).

2. Connectez-vous à la base de données avec [SQL Server Management Studio (SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copiez le [script In-Memory OLTP Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) dans le Presse-papiers. Le script T-SQL crée les objets en mémoire nécessaires dans l’exemple de base de données AdventureWorksLT créé à l’étape 1.

4. Collez le script T-SQL dans SSMS, puis exécutez-le. Les instructions CREATE TABLE de la clause `MEMORY_OPTIMIZED = ON` sont essentielles. Par exemple : 


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erreur 40536


Si vous obtenez une erreur 40536 lorsque vous exécutez le script T-SQL, exécutez le script T-SQL suivant pour vérifier que la base de données prend en charge In-Memory :


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Un résultat de **0** signifie que la technologie en mémoire n’est pas prise en charge, et un résultat de **1** signifie qu’elle l’est. Pour diagnostiquer le problème, vérifiez que la base de données se trouve sur le niveau de service Premium.


#### <a name="about-the-created-memory-optimized-items"></a>À propos des éléments créés à mémoire optimisée.

**Tables**: l’exemple contient les tables à mémoire optimisée suivantes :

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Vous pouvez inspecter les tables à mémoire optimisée via **l’Explorateur d’objets** dans SSMS. Cliquez avec le bouton droit sur **Tables** > **Filtre** > **Paramètres de filtre** > **Est à mémoire optimisée**. La valeur est égale à 1.


Vous pouvez aussi interroger les vues de catalogue, telles que :


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procédure stockée compilée en mode natif** : vous pouvez inspecter SalesLT.usp_InsertSalesOrder_inmem via une requête de vue de catalogue :


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>Exécuter l’exemple de charge de travail OLTP

La seule différence entre les deux *procédures stockées* est que la première utilise les versions à mémoire optimisée des tables, tandis que la deuxième utilise les tables sur disque régulières :

- SalesLT**.**usp_InsertSalesOrder**_inmem**
- SalesLT**.**usp_InsertSalesOrder**_ondisk**


Dans cette section, vous apprendrez à utiliser l’utilitaire **ostress.exe** , pratique pour exécuter les deux procédures stockées à des niveaux de contrainte élevés. Vous pouvez comparer le temps d’exécution des deux contraintes.


Lorsque vous exécutez ostress.exe, nous vous recommandons de transmettre des valeurs de paramètre conçues pour :

- Exécuter un grand nombre de connexions simultanées, en utilisant -n100.
- Répéter chaque boucle de connexion une centaine de fois, en utilisant -r500.


Toutefois, vous pouvez commencer avec des valeurs plus petites, telles que -n10 et -r50 pour vous assurer que tout fonctionne.


### <a name="script-for-ostressexe"></a>Script pour ostress.exe


Cette section affiche le script T-SQL incorporé à la ligne de commande ostress.exe. Le script utilise des éléments créés par le script T-SQL installé précédemment.


Le script suivant insère un exemple de commande client avec cinq lignes dans les *tables*à mémoire optimisée suivantes :

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Pour créer la version *_ondisk* du script T-SQL précédent pour ostress.exe, il suffit de remplacer les deux occurrences de la sous-chaîne *_inmem* par *_ondisk*. Ces remplacements affectent les noms des tables et des procédures stockées.


### <a name="install-rml-utilities-and-ostress"></a>Installer les utilitaires RML et ostress


Dans l’idéal, vous devez prévoir d’exécuter ostress.exe sur une machine virtuelle Azure. Vous devez créer une [machine virtuelle Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) dans la même région géographique Azure que celle où se trouve votre base de données AdventureWorksLT. Vous pouvez aussi exécuter ostress.exe sur votre ordinateur portable.


Sur la machine virtuelle (ou sur l’hôte que vous avez choisi d’utiliser), installez les utilitaires RML. Ceux-ci incluent ostress.exe.

Pour plus d'informations, consultez les pages suivantes :
- La discussion sur ostress.exe dans [Exemple de base de données pour OLTP en mémoire](http://msdn.microsoft.com/library/mt465764.aspx).
- [Exemple de base de données pour OLTP en mémoire](http://msdn.microsoft.com/library/mt465764.aspx).
- Le [blog pour l’installation de ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Commencer par exécuter la charge de travail de contrainte *_inmem*


Vous pouvez utiliser une fenêtre d’ *invite de commande RML* pour exécuter la ligne de commande ostress.exe. Les paramètres de ligne de commande indiquent à ostress d’effectuer les tâches suivantes :

- Exécuter 100 connexions simultanément (-n100).
- Chaque connexion doit exécuter le script T-SQL 50 fois (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Pour exécuter la ligne de commande ostress.exe précédente :


1. Réinitialisez le contenu de la base de données en exécutant la commande suivante dans SSMS, pour supprimer toutes les données insérées lors des exécutions précédentes :

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Copiez le texte de la ligne de commande ostress.exe qui précède dans le presse-papiers.

3. Remplacez le `<placeholders>` des paramètres -S - U -P -d par les valeurs réelles correctes.

4. Exécutez la ligne de commande que vous avez modifiée dans la fenêtre de commande RML.


#### <a name="result-is-a-duration"></a>Il en résulte une durée


Lorsque ostress.exe est terminé, la durée d’exécution est indiquée sur la dernière ligne de sortie dans la fenêtre de commande RML. Par exemple, une série de tests plus courte a duré environ 1,5 minute :

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Réinitialisez, paramétrez *_ondisk*, puis procédez à une nouvelle exécution.


Une fois le résultat de l’exécution de *_inmem* obtenu, effectuez les opérations suivantes pour l’exécution de *_ondisk* :


1. Réinitialisez la base de données en exécutant la commande suivante dans SSMS pour supprimer toutes les données insérées lors de l’exécution précédente :
```
EXECUTE Demo.usp_DemoReset;
```

2. Modifiez la ligne de commande ostress.exe pour remplacer toutes les occurrences de *_inmem* par *_ondisk*.

3. Réexécutez ostress.exe une deuxième fois, puis enregistrez le résultat de durée.

4. Réinitialisez à nouveau la base de données (pour supprimer une quantité de données de test qui peut s’avérer conséquente).


#### <a name="expected-comparison-results"></a>Résultats de la comparaison attendus

Nos tests en mémoire montrent une multiplication par **9** de l’amélioration des performances pour cette charge de travail simple, avec ostress s’exécutant sur une machine virtuelle Azure dans la même région Azure que la base de données.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Installer l’exemple In-Memory Analytics


Dans cette section, vous comparez les résultats des statistiques et les résultats d’E/S lors de l’utilisation d’un index columnstore par rapport à un index d’arborescence B traditionnel.


Pour l’analyse en temps réel sur une charge de travail OLTP, il est souvent préférable d’utiliser un index columnstore sans cluster. Pour plus d’informations, consultez [Index columnstore décrits](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Préparer le test d’analyse columnstore


1. Utilisez le portail Azure pour créer une base de données AdventureWorksLT à partir de l’exemple.
 - Utilisez ce nom exact.
 - Choisissez un niveau de service Premium.

2. Copiez [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) dans le Presse-papiers.
 - Le script T-SQL crée les objets en mémoire nécessaires dans l’exemple de base de données AdventureWorksLT créé à l’étape 1.
 - Le script crée la table Dimension et deux tables de faits. Les tables de faits comprennent 3,5 millions de lignes chacune.
 - Le script peut prendre 15 minutes pour s’exécuter.

3. Collez le script T-SQL dans SSMS, puis exécutez-le. Le mot clé **COLUMNSTORE** est essentiel dans l’instruction **CREATE INDEX**, comme dans l’exemple ci-dessous :<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Définissez AdventureWorksLT sur le niveau de compatibilité 130 :<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Le niveau 130 n’est pas directement lié aux fonctionnalités In-Memory. Mais le niveau 130 offre généralement des performances de requêtes supérieures au niveau 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Tables et index columnstore essentiels


- dbo.FactResellerSalesXL_CCI est une table contenant un index columnstore en cluster, ce qui permet la compression avancée au niveau des *données*.

- dbo.FactResellerSalesXL_PageCompressed est une table qui possède un index en cluster régulier équivalent, compressé uniquement au niveau de la *page*.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Requêtes essentielles pour comparer l’index columnstore


Il existe [plusieurs types de requête T-SQL que vous pouvez exécuter](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) pour mettre en évidence les améliorations des performances. À l’étape 2 dans le script T-SQL, soyez attentif à ces deux requêtes. Elles diffèrent uniquement d’une ligne :


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Un index columnstore en cluster se trouve dans la table FactResellerSalesXL\_CCI.

L’extrait de script T-SQL suivant imprime des statistiques d’E/S et d’heure pour la requête de chaque table.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

Dans une base de données ayant le niveau tarifaire P2, vous pouvez attendre une multiplication par 9 des performances de cette requête avec l’index columnstore en cluster par rapport à l’index traditionnel. Avec P15, vous pouvez vous attendre à une multiplication des performances par 57 à l’aide de l’index columnstore.



## <a name="next-steps"></a>Étapes suivantes

- [Démarrage rapide 1 : Technologies OLTP en mémoire pour des performances T-SQL plus rapides](http://msdn.microsoft.com/library/mt694156.aspx)

- [Utiliser OLTP en mémoire dans une application SQL Azure existante](sql-database-in-memory-oltp-migration.md)

- [Surveiller le stockage OLTP en mémoire](sql-database-in-memory-oltp-monitoring.md) pour l’OLTP en mémoire


## <a name="additional-resources"></a>Ressources supplémentaires

#### <a name="deeper-information"></a>Informations supplémentaires

- [Découvrez comment le Quorum double la charge de travail de la base de données clé tout en réduisant les DTU de 70 %, grâce à l’OLTP en mémoire dans la SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Billet de blog OLTP en mémoire dans Azure SQL Database](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [En savoir plus sur In-Memory OLTP](http://msdn.microsoft.com/library/dn133186.aspx)

- [En savoir plus sur les index columnstore](https://msdn.microsoft.com/library/gg492088.aspx)

- [En savoir plus sur l’analytique opérationnelle en temps réel](http://msdn.microsoft.com/library/dn817827.aspx)

- Consultez [Modèles de charge de travail courants et considérations relatives à la migration](http://msdn.microsoft.com/library/dn673538.aspx) (qui décrit des modèles de charge de travail où l’OLTP en mémoire apporte généralement des gains de performances significatifs)

#### <a name="application-design"></a>Conception des applications

- [In-Memory OLTP (optimisation en mémoire)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Utiliser OLTP en mémoire dans une application SQL Azure existante](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Outils

- [Portail Azure](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [Outils SQL Server Data Tools (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx)
