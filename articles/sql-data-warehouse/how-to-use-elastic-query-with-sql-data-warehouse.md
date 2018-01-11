---
title: "Concepts de requête élastique avec Azure SQL Data Warehouse | Microsoft Docs"
description: "Concepts de requête élastique avec Azure SQL Data Warehouse"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: e2dc8f3f-10e3-4589-a4e2-50c67dfcf67f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 09/18/2017
ms.author: elbutter
ms.openlocfilehash: 4c351d88b31adfa3443dd2231f67bb442f2b8fe0
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2017
---
# <a name="how-to-use-elastic-query-with-sql-data-warehouse"></a>Guide pratique pour utiliser la fonctionnalité de requête élastique avec SQL Data Warehouse



Vous pouvez utiliser la fonctionnalité de requête élastique avec Azure SQL Data Warehouse pour écrire du code Transact-SQL dans une base de données SQL qui est envoyé à distance à une instance Azure SQL Data Warehouse par le biais de tables externes. Cette fonctionnalité permet de réaliser des économies et d’obtenir des architectures plus performantes en fonction du scénario.

Deux scénarios principaux se prêtent à l’utilisation de cette fonctionnalité :

1. Isolation de domaine
2. Exécution de requête distante

### <a name="domain-isolation"></a>Isolation de domaine

L’isolation de domaine fait référence au scénario DataMart classique. Dans certains scénarios, vous pouvez souhaiter fournir un domaine de données logique à des utilisateurs en aval qui sont isolés du reste de l’entrepôt de données, pour diverses raisons, telles que celles-ci :

1. Isolation de la ressource : la base de données SQL est optimisée pour servir une base volumineuse d’utilisateurs simultanés en prenant en charge des charges de travail légèrement différentes des requêtes analytiques volumineuses pour lesquelles l’entrepôt de données est réservé. L’isolation garantit que les charges de travail adéquates sont prises en charge par les outils appropriés.
2. Isolation de sécurité : pour séparer un sous-ensemble de données autorisé sélectivement par le biais de certains schémas.
3. Utilisation d’un bac à sable : fournir un exemple d’ensemble de données en guise de « laboratoire » pour explorer, entre autres, les requêtes de production.

Avec la fonctionnalité de requête élastique, vous pouvez facilement sélectionner des sous-ensembles de données de l’entrepôt de données SQL et les déplacer vers une instance de la base de données SQL. En outre, cette isolation n’exclut pas la possibilité d’activer l’exécution de requête distante dans le cadre de scénarios de « cache » plus intéressants.

### <a name="remote-query-execution"></a>Exécution de requête distante

La fonctionnalité de requête élastique permet l’exécution de requête distante sur une instance de l’entrepôt de données SQL. Vous pouvez tirer le meilleur parti de la base de données SQL et de l’entrepôt de données SQL en répartissant vos données chaudes et froides entre les deux bases de données. Les utilisateurs peuvent conserver davantage de données récentes dans une base de données SQL, qui peut servir des rapports et un grand nombre d’utilisateurs ordinaires au sein des entreprises. Toutefois, quand davantage de données ou de calcul sont nécessaires, un utilisateur peut décharger une partie de la requête sur une instance de l’entrepôt de données SQL où les agrégats à grande échelle peuvent être traités beaucoup plus rapidement et plus efficacement.



## <a name="elastic-query-overview"></a>Vue d’ensemble des requêtes élastiques

Une requête élastique peut être utilisée pour mettre les données situées dans un entrepôt de données SQL à la disposition d’instances de la base de données SQL. La requête élastique permet à des requêtes d’une base de données SQL de faire référence à des tables dans une instance de l’entrepôt de données SQL distante. 

La première étape consiste à créer une définition de source de données externe faisant référence à l’instance de l’entrepôt de données SQL, qui utilise les informations d’identification utilisateur existantes au sein de l’entrepôt de données SQL. Aucune modification n’est nécessaire sur l’instance de l’entrepôt de données SQL distante. 

> [!IMPORTANT] 
> 
> Vous devez posséder l’autorisation ALTER ANY EXTERNAL DATA SOURCE. Cette autorisation est incluse dans l’autorisation ALTER DATABASE. Les autorisations ALTER ANY EXTERNAL DATA SOURCE sont nécessaires pour faire référence aux sources de données distantes.

Ensuite, dans une instance de la base de données SQL, vous créez une définition de table externe distante qui pointe vers une table distante dans l’entrepôt de données SQL. Quand vous utilisez une requête qui utilise une table externe, la partie de la requête qui fait référence à la table externe est envoyée à l’instance de l’entrepôt de données SQL à traiter. Une fois la requête terminée, le jeu de résultats est renvoyé à l’instance de base de données SQL appelante. Pour obtenir un didacticiel sommaire de la configuration d’une requête élastique entre la base de données SQL et l’entrepôt de données SQL, consultez [Configurer la fonctionnalité de requête élastique SQL Data Warehouse][Configure Elastic Query with SQL Data Warehouse].

Pour plus d’informations sur la fonctionnalité de requête élastique avec la base de données SQL, consultez [Vue d’ensemble de la fonctionnalité de requête élastique Azure SQL Database (préversion)][Azure SQL Database elastic query overview].



## <a name="best-practices"></a>Bonnes pratiques

### <a name="general"></a>Généralités

- Quand vous utilisez l’exécution de requête distante, veillez à ne sélectionner que les colonnes nécessaires et à n’appliquer que les filtres appropriés. Cette méthode augmente à la fois le calcul nécessaire et la taille du jeu de résultats, et, par là même, la quantité de données à déplacer entre les deux instances.
- Pour optimiser le niveau de performance des analyses, conservez les données de l’entrepôt de données SQL et de la base de données SQL dans un columnstore en cluster.
- Vérifiez que les tables sources sont partitionnées pour la requête et le déplacement des données.
- Vérifiez que les instances de la base de données SQL utilisées en tant que cache sont partitionnées pour autoriser des mises à jour plus précises et faciliter la gestion. 
- Dans l’idéal, utilisez des bases de données PremiumRS, qui offrent les avantages analytiques de l’indexation columnstore en cluster en mettant l’accent sur les charges de travail gourmandes en opérations d’E/S et reviennent moins cher que les bases de données Premium.
- Après les chargements, utilisez des colonnes d’identification de date d’effet ou de chargement pour les upserts dans les instances SQL Database afin de conserver l’intégrité de la source du cache. 
- Créez une connexion et un utilisateur distincts dans votre instance de l’entrepôt de données SQL pour vos informations d’identification d’ouverture de session à distance sur la base de données SQL définies dans la source de données externe. 

### <a name="elastic-querying"></a>Interrogation élastique

- Dans de nombreux cas, il peut être souhaitable de gérer un type de table étendue. Une partie de votre table se trouve au sein de la base de données SQL Database sous la forme de données mises en cache pour améliorer les performances, tandis que les données restantes sont stockées dans SQL Data Warehouse. Vous devez disposer de deux objets dans SQL Database : une table externe au sein de SQL Database qui fait référence à la table de base dans SQL Data Warehouse et la partie « mise en cache » de la table dans la base de données SQL Database. Au-dessus de la partie mise en cache de la table et de la table externe, envisagez de créer une vue qui joint les deux tables et applique des filtres qui séparent les données matérialisées au sein de SQL Database et les données SQL Data Warehouse exposées par le biais de tables externes.

  Imaginez que nous souhaitions conserver l’année de données la plus récente dans une instance de la base de données SQL. Nous avons deux tables : **ext.Orders**, qui fait référence aux tables de commandes dans l’entrepôt de données, et **dbo.Orders**, qui représente les données des années les plus récentes au sein de l’instance de la base de données SQL. Au lieu de demander aux utilisateurs de décider s’il faut interroger une table ou une autre, nous créons une vue au-dessus des deux tables sur le point de la partition de l’année la plus récente.

  ```sql
  CREATE VIEW dbo.Orders_Elastic AS
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]
  FROM
    [dbo].[Orders]
  WHERE 
    YEAR([o_orderdate]) >= '<Most Recent Year>'
  UNION
  SELECT 
    [col_a]     
  , [col_b]         
  , ...
  , [col_n]         
  FROM
    [ext].[Orders]
  WHERE
    YEAR([o_orderdate]) < '<Most Recent Year>'
  ```

  Une vue produite de cette manière permet au compilateur de requête de déterminer s’il doit utiliser l’instance de l’entrepôt de données pour répondre à la requête de vos utilisateurs. 

  La soumission, la compilation, l’exécution et le déplacement des données associées à chaque requête élastique sur l’instance de l’entrepôt de données entraînent une surcharge. Gardez à l’esprit que chaque requête élastique consomme des ressources et une partie de vos emplacements de concurrence.  


- Si vous envisagez d’explorer plus en détail le jeu de résultats issu de l’instance de l’entrepôt de données, pensez à le matérialiser dans une table temporaire au sein de la base de données SQL pour optimiser les performances et empêcher l’utilisation de ressources superflue.

### <a name="moving-data"></a>Déplacement des données 

- Dans la mesure du possible, simplifiez la gestion des données avec des tables sources d’ajout uniquement afin que les mises à jour soient facilement gérables entre les instances de la base de données et de l’entrepôt de données.
- Déplacez les données au niveau de la partition avec la sémantique de vidage et de remplissage pour réduire le coût de la requête au niveau de l’entrepôt de données et la quantité de données déplacées afin que l’instance de la base de données demeure à jour. 

### <a name="when-to-choose-azure-analysis-services-vs-sql-database"></a>Choisir Azure Analysis Services ou SQL Database

#### <a name="azure-analysis-services"></a>Azure Analysis Services

- Vous prévoyez d’utiliser votre cache avec un outil décisionnel qui soumet de grandes quantités de petites requêtes.
- Vous avez besoin d’une latence de requête inférieure à la seconde.
- Vous êtes expérimenté dans la gestion et le développement de modèles pour Analysis Services. 

#### <a name="sql-database"></a>Base de données SQL

- Vous souhaitez interroger vos données de cache avec SQL.
- Vous avez besoin de l’exécution distante pour certaines requêtes.
- Vous avez des exigences de cache plus grand.



## <a name="faq"></a>Forum Aux Questions

Q : Puis-je utiliser des bases de données au sein d’un pool élastique avec une requête élastique ?

R. : Oui. Les bases de données SQL au sein d’un pool élastique peuvent utiliser la fonctionnalité de requête élastique. 

Q : Le nombre de bases de données utilisables avec la fonctionnalité de requête élastique est-il limité ?

R : Le nombre de bases de données utilisables avec la fonctionnalité de requête élastique n’est pas strictement limité. Toutefois, chaque requête élastique (requête accédant à SQL Data Warehouse) est comptabilisée dans les limites de concurrence normales.

Q : La fonctionnalité de requête élastique implique-t-elle des limites DTU ?

R : Les limites DTU ne sont pas imposées différemment avec la fonctionnalité de requête élastique. Selon la stratégie standard, les serveurs logiques sont soumis à des limites DTU afin que les clients évitent les dépenses excessives accidentelles. Si vous permettez à plusieurs bases de données d’exploiter la fonctionnalité de requête élastique parallèlement à une instance de l’entrepôt de données SQL, vous pouvez atteindre la limite brusquement. Si cela se produit, soumettez une demande pour augmenter la limite DTU sur votre serveur logique. Vous pouvez augmenter votre quota en [créant un ticket de support](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) et en sélectionnant *Quota* comme type de demande.

Q : Puis-je utiliser la sécurité au niveau des lignes/Dynamic Data Masking avec la fonctionnalité de requête élastique ?

R : Si vous souhaitez utiliser des fonctionnalités de sécurité plus avancées avec SQL Database, vous devez au préalable déplacer les données et les stocker dans la base de données SQL. Vous ne pouvez pas appliquer la sécurité au niveau des lignes ou DDM sur les données interrogées au moyen de tables externes. 

Q : Puis-je écrire depuis mon instance de base de données SQL vers l’instance de l’entrepôt de données ?

R : cette fonctionnalité n’est pas prise en charge pour l’instant. Visitez notre [page de commentaires][Feedback page] pour proposer cette fonctionnalité ou voter en sa faveur si vous aimeriez la voir à l’avenir. 

Q : Puis-je utiliser des types spatiaux comme géométrie/géographie ?

R : Vous pouvez stocker des types spatiaux dans SQL Data Warehouse en tant que valeurs varbinary(max). Quand vous interrogez ces colonnes à l’aide de la fonctionnalité de requête élastique, vous pouvez les convertir en types appropriés au moment de l’exécution.

![Types spatiaux](./media/sql-data-warehouse-elastic-query-with-sql-database/geometry-types.png)





<!--Image references-->

<!--Article references-->

[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[Configure Elastic Query with SQL Data Warehouse]: ./tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md
[Feedback Page]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Database elastic query overview]: ../sql-database/sql-database-elastic-query-overview.md

<!--MSDN references-->

<!--Other Web references-->
