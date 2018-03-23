---
title: Prise en main des tables temporelles dans Azure SQL Database | Microsoft Docs
description: Découvrez comment prendre en main les tables temporelles dans Azure SQL Database.
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 01/10/2017
ms.author: bonova
ms.openlocfilehash: 8e76d78e402d2cdc58ca26767c55c413f83226d9
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Prise en main des tables temporelles dans Azure SQL Database
Les tables temporelles sont une nouvelle fonctionnalité de programmabilité d’Azure SQL Database qui vous permet de suivre et d’analyser l’historique complet des modifications apportées à vos données, sans codage personnalisé. Les tables temporelles conservent les données étroitement liées au contexte temporel afin que les faits stockés puissent être interprétés comme étant valides uniquement dans une période spécifique. Cette propriété des tables temporelles permet une analyse efficace basée sur le temps et permet d’obtenir des informations à partir de l’évolution des données.

## <a name="temporal-scenario"></a>Scénario temporel
Cet article illustre les étapes permettant d’utiliser les tables temporelles dans un scénario d’application. Supposons que vous voulez effectuer le suivi de l’activité utilisateur sur un nouveau site web en cours de développement ou sur un site web existant que vous voulez enrichir avec l’analyse de l’activité utilisateur. Dans cet exemple simplifié, nous partons du principe que le nombre de pages web visitées pendant une période de temps est un indicateur qui doit être capturé et analysé dans la base de données du site web hébergée sur Azure SQL Database. L’objectif de l’analyse de l’historique de l’activité utilisateur est d’obtenir des informations pour redessiner le site web et fournir une meilleure expérience pour les visiteurs.

Le modèle de base de données pour ce scénario est très simple : la mesure de l’activité utilisateur est représentée par un champ d’entier unique, **PageVisited**, et est capturée en même temps que des informations de base sur le profil utilisateur. En outre, pour l’analyse temporelle, vous conservez une série de lignes pour chaque utilisateur, où chaque ligne représente le nombre de pages visitées par un utilisateur particulier dans une période de temps spécifique.

![Schéma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Heureusement, vous n’avez pas besoin d’intervenir sur votre application pour gérer les informations de cette activité. Avec les tables temporelles, ce processus est automatisé : ce qui vous donne une grande souplesse pour concevoir le site web et davantage de temps pour vous concentrer sur l’analyse des données. La seule chose que vous avez à faire est de garantir que la table **WebSiteInfo** est configurée en tant que table [temporelle avec versions gérées par le système](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Les étapes à suivre pour utiliser les tables temporelles dans ce scénario sont décrites ci-dessous.

## <a name="step-1-configure-tables-as-temporal"></a>Étape 1: Configurer les tables comme tables temporelles
Selon que vous commencez le développement d’une nouvelle application ou que vous mettez à niveau une application existante, vous créez des tables temporelles ou vous modifiez des tables existantes en ajoutant des attributs temporels. En général, votre scénario peut être une combinaison de ces deux options. Exécutez ces opérations à l’aide de [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) ou tout autre outil de développement Transact-SQL.

> [!IMPORTANT]
> Nous vous recommandons d’utiliser systématiquement la dernière version de Management Studio afin de rester en cohérence avec les mises à jour de Microsoft Azure et Base de données SQL. [Mettre à jour SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Créer une table
Utilisez l’élément de menu contextuel Nouvelle table avec version système dans l’Explorateur d’objets SSMS pour ouvrir l’éditeur de requête avec un script de modèle de table temporelle, puis utilisez « Spécifier les valeurs des paramètres du modèle » (Ctrl+Maj+M) pour remplir le modèle :

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

Dans SSDT, choisissez le modèle « Table temporelle (Système par version) » quand vous ajoutez de nouveaux éléments au projet de base de données. Cette opération ouvre le concepteur de tables et vous permet de spécifier facilement la disposition de la table :

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Vous pouvez également créer une table temporelle en spécifiant les instructions Transact-SQL directement, comme indiqué dans l’exemple ci-dessous. Notez que les éléments obligatoires de chaque table temporelle sont la définition de PERIOD et la clause SYSTEM_VERSIONING avec une référence à une autre table utilisateur qui stocke les versions de ligne historiques :

````
CREATE TABLE WebsiteUserInfo 
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED 
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL 
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
````

Quand vous créez une table temporelle avec versions gérées par le système, la table d’historique associée avec la configuration par défaut est automatiquement créée. La table d’historique par défaut contient un index cluster arbre B (B-tree) sur les colonnes de période (début, fin) pour lequel la compression de page est activée. Cette configuration est optimale pour la plupart des scénarios dans lesquels les tables temporelles sont utilisées, en particulier pour l’ [audit des données](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

Dans ce cas particulier, nous voulons effectuer une analyse temporelle des tendances sur un historique de données plus long et avec des jeux de données plus volumineux, le choix du stockage pour la table d’historique est donc un index cluster columnstore. L’index cluster columnstore fournit une très bonne compression et des performances optimales pour les requêtes analytiques. Les tables temporelles vous donnent la possibilité de configurer complètement indépendamment des index sur les tables actuelles et temporelles. 

> [!NOTE]
> Les index columnstore sont uniquement disponibles au niveau de service Premium.
>

Le script suivant montre comment l’index par défaut sur la table d’historique peut être modifié en index cluster columnstore :

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Les tables temporelles sont représentées dans l’Explorateur d’objets avec une icône spécifique pour faciliter l’identification, tandis que la table d’historique s’affiche comme un nœud enfant.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Transformer une table existante en table temporelle
Examinons l’autre scénario dans lequel la table WebsiteUserInfo existe déjà, mais n’a pas été conçue pour conserver un historique des modifications. Dans ce cas, vous pouvez simplement étendre la table existante pour qu’elle devienne temporelle, comme indiqué dans l’exemple suivant :

````
ALTER TABLE WebsiteUserInfo 
ADD 
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN   
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo); 

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

## <a name="step-2-run-your-workload-regularly"></a>Étape 2 : Exécuter votre charge de travail de façon régulière
Le principal avantage des tables temporelles est que vous n’avez pas besoin de modifier ou d’ajuster votre site web de quelque façon que ce soit pour effectuer le suivi des modifications. Une fois créées, les tables temporelles conservent en toute transparence les versions précédentes des lignes chaque fois que vous effectuez des modifications sur vos données. 

Pour tirer parti du suivi automatique des modifications dans ce scénario particulier, nous allons simplement mettre à jour la colonne **PagesVisited** chaque fois qu’un utilisateur met fin à sa session sur le site web :

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Notez que la requête de mise à jour n’a pas besoin de connaître l’heure exacte à laquelle s’est produit l’opération réelle ni comment les données historiques sont conservées pour une future analyse. Ces deux aspects sont gérés automatiquement par Azure SQL Database. Le diagramme suivant illustre la façon dont les données d’historique sont générées à chaque mise à jour.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Étape 3: Analyser les données historiques
Une fois que la gestion de version des tables temporelles par le système est activée, il vous suffit d’une seule requête pour analyser les données historiques. Dans cet article, nous fournissons des exemples de scénarios d’analyse courants. Pour connaître tous les détails, explorez les diverses options introduites avec la clause [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3).

Pour afficher les 10 principaux utilisateurs classés par nombre de pages web visitées durant la dernière heure, exécutez cette requête :

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Vous pouvez facilement modifier cette requête pour analyser les visites du site au cours du dernier jour, du dernier mois ou à n’importe quel moment dans le passé.

Pour effectuer l’analyse statistique de base du jour précédent, utilisez l’exemple suivant :

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

Pour rechercher les activités d’un utilisateur spécifique dans une période de temps, utilisez la clause CONTAINED IN :

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

La visualisation graphique est particulièrement pratique pour les requêtes temporelles, car vous pouvez afficher les tendances et les modèles d’utilisation de façon intuitive très facilement :

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Évolution du schéma de table
En règle générale, vous devez modifier le schéma de table temporelle pendant le développement d’applications. Pour ce faire, exécutez simplement des instructions ALTER TABLE standard et Azure SQL Database propage les modifications de manière appropriée dans la table d’historique. Le script suivant montre comment ajouter un attribut supplémentaire pour le suivi :

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

De même, vous pouvez modifier la définition de colonne pendant que votre charge de travail est active :

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Enfin, vous pouvez supprimer une colonne dont vous n’avez plus besoin.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````

Vous pouvez également utiliser la dernière version de [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) pour modifier un schéma de table temporelle quand vous êtes connecté à la base de données (mode en ligne) ou dans le cadre du projet de base de données (mode hors connexion).

## <a name="controlling-retention-of-historical-data"></a>Contrôle de la rétention des données historiques
Avec les tables temporelles avec versions gérées par le système, la table d’historique peut augmenter la taille de la base de données davantage que les tables normales. Une table d’historique volumineuse qui continue à croître peut devenir un problème non seulement en raison des coûts de stockage, mais aussi parce qu’elle a un impact négatif sur les performances de l’interrogation temporelle. Par conséquent, le développement d’une stratégie de rétention des données pour gérer les données dans la table d’historique est un aspect important de la planification et de la gestion du cycle de vie de chaque table temporelle. Avec Azure SQL Database, vous bénéficiez des approches suivantes pour gérer les données historiques dans la table temporelle :

* [Partitionnement de tables](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Script de nettoyage personnalisé](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les tables temporelles, consultez la [documentation MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Visitez Channel 9 pour écouter le [témoignage d’un client sur l’implémentation temporelle](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) et regardez une [démonstration de table temporelle en direct](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

