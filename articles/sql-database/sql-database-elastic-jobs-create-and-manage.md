---
title: Gérer des groupes de bases de données Azure SQL | Microsoft Docs
description: Passez en revue la création et la gestion d’une tâche élastique.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 10/24/2016
ms.author: sstein
ms.openlocfilehash: 77204e167d298ad77b2786b5d0a3dab1b557ccfc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="create-and-manage-scaled-out-azure-sql-databases-using-elastic-jobs-preview"></a>Créer et gérer des bases de données SQL Azure avec montée en charge à l’aide de tâches élastiques (version préliminaire)


**tâches de bases de données élastiques** simplifient la gestion des groupes de bases de données en exécutant des opérations administratives telles que les modifications de schéma, la gestion des informations d’identification, les mises à jour de données de référence, la collecte des données de performances ou la collecte de données de télémétrie du locataire (client). Tâches de bases de données élastiques est actuellement disponible via le portail Azure et les applets de commande PowerShell. Le Portail Azure propose toutefois une fonctionnalité réduite, qui se limite à une exécution sur toutes les bases de données d’un [pool élastique (version préliminaire)](sql-database-elastic-pool.md). Pour accéder à des fonctionnalités supplémentaires et à l’exécution des scripts sur un groupe de bases de données, notamment une collection personnalisée ou un ensemble de partitions (créé à l’aide de la [bibliothèque cliente de la base de données élastique](sql-database-elastic-scale-introduction.md)), consultez l’article [Créer et gérer des tâches avec PowerShell](sql-database-elastic-jobs-powershell.md). Pour en savoir plus sur les tâches, consultez la rubrique [Vue d'ensemble des tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md). 

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Pour un essai gratuit, consultez [Version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
* Un pool élastique. Voir [À propos des pools élastiques](sql-database-elastic-pool.md).
* Installation des composants du service de tâches de bases de données élastiques. Voir [Installation du service de tâches de bases de données élastiques](sql-database-elastic-jobs-service-installation.md).

## <a name="creating-jobs"></a>Création de travaux
1. À l'aide du [portail Azure](https://portal.azure.com), à partir d'un pool élastique de tâches de bases de données existant, cliquez sur **Créer une tâche**.
2. Tapez le nom d’utilisateur et le mot de passe de l’administrateur (créé à l’installation de Jobs) de la base de données de contrôle des tâches (stockage des métadonnées pour les tâches).
   
    ![Nommez la tâche, saisissez ou collez le code puis cliquez sur Exécuter][1]
3. Dans le volet **Créer une tâche** , nommez la tâche.
4. Tapez le nom d’utilisateur et le mot de passe pour vous connecter aux bases de données cibles avec les autorisations suffisantes pour l’exécution du script.
5. Collez ou saisissez le script T-SQL.
6. Cliquez sur **Enregistrer**, puis sur **Exécuter**.
   
    ![Créez des tâches et exécutez-les.][5]

## <a name="run-idempotent-jobs"></a>Exécution de tâches idempotent
Lorsque vous exécutez un script sur un ensemble de bases de données, vous devez être sûr que le script est idempotent. Autrement dit, le script doit pouvoir être exécuté plusieurs fois, même s'il a échoué avec un état incomplet auparavant. Par exemple, lorsqu'un script échoue, la tâche sera automatiquement relancée jusqu'à ce qu'elle  aboutisse (jusqu'à une certaine limite car la logique de relance finira par s'arrêter). La solution consiste à utiliser la clause « IF EXISTS » et à supprimer toutes les instances trouvées avant de créer un objet. Voici un exemple :

    IF EXISTS (SELECT name FROM sys.indexes
            WHERE name = N'IX_ProductVendor_VendorID')
    DROP INDEX IX_ProductVendor_VendorID ON Purchasing.ProductVendor;
    GO
    CREATE INDEX IX_ProductVendor_VendorID
    ON Purchasing.ProductVendor (VendorID);

Vous pouvez également utiliser une clause « IF NOT EXISTS » avant de créer une instance :

    IF NOT EXISTS (SELECT name FROM sys.tables WHERE name = 'TestTable')
    BEGIN
     CREATE TABLE TestTable(
      TestTableId INT PRIMARY KEY IDENTITY,
      InsertionTime DATETIME2
     );
    END
    GO

    INSERT INTO TestTable(InsertionTime) VALUES (sysutcdatetime());
    GO

ce script met alors à jour la table créée précédemment.

    IF NOT EXISTS (SELECT columns.name FROM sys.columns INNER JOIN sys.tables on columns.object_id = tables.object_id WHERE tables.name = 'TestTable' AND columns.name = 'AdditionalInformation')
    BEGIN

    ALTER TABLE TestTable

    ADD AdditionalInformation NVARCHAR(400);
    END
    GO

    INSERT INTO TestTable(InsertionTime, AdditionalInformation) VALUES (sysutcdatetime(), 'test');
    GO


## <a name="checking-job-status"></a>Vérification du statut de la tâche
Une fois la tâche lancée, vous pouvez vérifier son statut.

1. Sur la page du pool élastique, cliquez sur **Gérer les travaux**.
   
    ![Cliquez sur « Gérer les tâches ».][2]
2. Cliquez sur le nom (a) d'une tâche. Le champ **STATUS** peut afficher la valeur « Completed » (Terminé) ou « Failed » (Échec). Les détails de la tâche (b) incluent la date et l'heure de création et d'exécution. La liste (c) ci-dessous indique la progression du script sur chaque base de données du pool, en précisant la date et l'heure.
   
    ![Vérification d'une tâche terminée][3]

## <a name="checking-failed-jobs"></a>Vérification des tâches ayant échoué
Si une tâche échoue, un journal détaillant son exécution est créé. Cliquez sur le nom de la tâche ayant échoué pour afficher ses détails.

![Vérifier une tâche ayant échoué][4]

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-create-and-manage/screen-1.png
[2]: ./media/sql-database-elastic-jobs-create-and-manage/click-manage-jobs.png
[3]: ./media/sql-database-elastic-jobs-create-and-manage/running-jobs.png
[4]: ./media/sql-database-elastic-jobs-create-and-manage/failed.png
[5]: ./media/sql-database-elastic-jobs-create-and-manage/screen-2.png


