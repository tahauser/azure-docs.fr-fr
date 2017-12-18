---
title: "Conseils sur le chargement de données - Azure SQL Data Warehouse | Microsoft Docs"
description: "Recommandations pour le chargement des données et l’exécution d’ELT avec Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 7b698cad-b152-4d33-97f5-5155dfa60f79
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 12/13/2017
ms.author: barbkess
ms.openlocfilehash: 8903be1361d1574a5d81b69223f608ccb7a698ea
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="guidance-for-loading-data-into-azure-sql-data-warehouse"></a>Conseils sur le chargement de données dans Azure SQL Data Warehouse
Recommandations et optimisation des performances pour le chargement de données dans Azure SQL Data Warehouse. 

- Pour plus d’informations sur PolyBase et la conception d’un processus d’extraction, de chargement et transformation (ELT), consultez [Designing Extract, Load, and Transform (ELT) for Azure SQL Data Warehouse](design-elt-data-loading.md) (Conception d’un processus d’extraction, de chargement et de transformation (ELT) pour Azure SQL Data Warehouse).
- Pour un didacticiel sur le chargement, consultez [Utiliser PolyBase pour charger des données du Stockage Blob Azure dans Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md).


## <a name="extract-source-data"></a>Extraire les données sources

Lorsque vous exportez des données au format de fichier ORC à partir de SQL Server ou d’Azure SQL Data Warehouse, le nombre de colonnes contenant beaucoup de texte peut être limité à 50, en raison d’erreurs Java de mémoire insuffisante. Pour contourner ce problème, exportez uniquement un sous-ensemble de ces colonnes.


## <a name="land-data-to-azure"></a>Charger des données dans Azure
PolyBase ne peut pas charger de lignes comptant plus d’un million d’octets de données. Lorsque vous placez des données dans des fichiers texte dans le stockage Blob Azure ou Azure Data Lake Store, elles doivent être inférieures à un million d’octets. Ceci est valable, quel que soit le schéma de table défini.

Colocalisez votre couche de stockage et votre entrepôt de données pour réduire la latence.

## <a name="data-preparation"></a>Préparation des données

Tous les formats de fichier présentent des caractéristiques de performances différentes. Pour un chargement plus rapide, utilisez des fichiers texte délimités compressés. La différence entre les performances des formats UTF-8 et UTF-16 est minime.

Fractionnez les fichiers compressés volumineux en plusieurs petits fichiers compressés.

## <a name="create-designated-loading-users"></a>Créer des utilisateurs de chargement désignés
Pour exécuter des charges avec des ressources de calcul appropriées, créez des utilisateurs de chargement désignés pour cette tâche. Attribuez à chaque utilisateur de chargement pour une classe de ressources spécifique. Pour exécuter une charge, connectez-vous en tant qu’utilisateur de chargement, puis exécutez la charge. La charge s’exécute avec la classe de ressources de l’utilisateur.  Cette méthode est plus simple que d’essayer de modifier la classe de ressources d’un utilisateur pour répondre au besoin de la classe de ressources actuelle.

Ce code crée un utilisateur de chargement pour la classe de ressources staticrc20. Il donne l’autorisation d’utiliser le contrôle utilisateur sur une base données, puis ajoute l’utilisateur en tant que membre du rôle de base de données staticrc20. Pour exécuter une charge avec des ressources pour les classes de ressources staticRC20, connectez-vous simplement en tant que LoaderRC20 et exécutez la charge. 

    ```sql
    CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
    CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
    GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
    EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
    ```

Exécutez des charges sous des classes de ressources statiques plutôt que dynamiques. L’utilisation des classes de ressources statiques garantit les mêmes ressources, quel que soit votre [niveau de service](performance-tiers.md#service-levels). Si vous utilisez une classe de ressources dynamique, les ressources varient en fonction de votre niveau de service. Pour les classes dynamiques, un niveau de service inférieur signifie que vous devrez probablement utiliser une classe de ressources supérieure pour votre utilisateur de chargement.

### <a name="example-for-isolating-loading-users"></a>Exemple d’isolation des utilisateurs de chargement

Il est souvent nécessaire d’avoir plusieurs utilisateurs qui peuvent charger des données dans un entrepôt de données SQL. Étant donné que [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] nécessite des autorisations CONTROL de la base de données, vous obtenez plusieurs utilisateurs avec un accès au contrôle sur tous les schémas. Pour limiter ce problème, vous pouvez utiliser l’instruction DENY CONTROL.

Par exemple, les schémas de base de données schema_A pour le service A et schema_B pour le service B laissent les utilisateurs de base de données user_A et user_B être utilisateurs du chargement PolyBase dans les services A et B, respectivement. Ils ont tous deux reçu des autorisations de base de données CONTROL. Les créateurs des schémas A et B verrouillent maintenant leurs schémas à l’aide de DENY :

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   

user_A et user_B ne doivent maintenant plus avoir accès au schéma de l’autre service.


## <a name="load-to-a-staging-table"></a>Charger dans une table de mise en lots

Pour un chargement plus rapide, chargez les fichiers dans une table de mise en lots de segments de mémoire avec tourniquet (round robin). Il s’agit du moyen le plus efficace pour déplacer des données de la couche de stockage Azure vers SQL Data Warehouse.

Procédez à une montée en puissance de votre entrepôt de données si vous devez charger un fichier volumineux.

Exécuter un seul travail de chargement à la fois pour des performances de charge optimales

### <a name="optimize-columnstore-index-loads"></a>Optimiser des charges d’index columnstore

Les index columnstore ont besoin de beaucoup de mémoire pour compresser des données dans des rowgroups de haute qualité. Pour une meilleure compression et un index plus efficace, l’index columnstore doit compresser 1 048 576 lignes dans chaque rowgroup. Il s’agit du nombre maximal de lignes par rowgroup. En cas de sollicitation de la mémoire, l’index columnstore peut ne pas être en mesure d’atteindre les taux de compression maximum. Cela affecte les performances de requêtes. Pour une présentation approfondie, consultez [Columnstore memory optimizations](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md) (Optimisation de mémoire Columstore).

- Pour garantir que l’utilisateur de chargement dispose de suffisamment de mémoire pour atteindre des taux de compression maximum, utilisez des utilisateurs de chargement qui sont membres d’une classe de ressources moyenne ou grande. 
- Chargez suffisamment de lignes pour remplir complètement de nouveaux rowgroups. Lors d’un chargement en masse, chacune des 1 048 576 lignes passe directement dans le columnstore. Des charges avec moins de 102 400 lignes envoient les lignes au deltastore, qui retient les lignes dans un index cluster jusqu’à ce qu’il y en ait suffisamment pour la compression. Si vous chargez trop peu de lignes, elles risquent de toutes rejoindre le deltastore et de ne pas être compressées immédiatement au format columnstore.


### <a name="handling-loading-failures"></a>Gestion des échecs de chargement

Une charge qui utilise une table externe peut échouer avec l’erreur suivante : *« Requête abandonnée : le seuil de rejet maximal a été atteint durant la lecture d’une source externe »*. Cela indique que vos données externes contiennent des enregistrements *à l’intégrité compromise* . Un enregistrement de données est considéré comme « compromis » si les types de données / le nombre de colonnes réels ne correspondent pas aux définitions de colonne de la table externe ou si les données ne sont pas conformes au format de fichier externe spécifié. 

Pour résoudre ce problème, assurez-vous que les définitions de format de votre table externe et de votre fichier externe sont correctes et que vos données externes sont conformes à ces définitions. Dans le cas où un sous-ensemble d'enregistrements de données externes serait compromis, vous pouvez choisir de rejeter ces enregistrements pour vos requêtes en utilisant les options de rejet dans le DDL CREATE EXTERNAL TABLE.



## <a name="insert-data-into-production-table"></a>Insérer des données dans une table de production
Ces recommandations concernent l’insertion de lignes dans les tables de production.


### <a name="batch-insert-statements"></a>Instructions INSERT groupées
Une charge unique dans une petite table à l’aide d’une instruction [INSERT](/sql/t-sql/statements/insert-transact-sql.md) ou même un rechargement périodique d’une recherche peut répondre parfaitement à vos besoins grâce à une instruction comme `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Des insertions uniques ne sont pas aussi efficaces qu’un chargement en masse. 

Si vous avez au minimum plusieurs milliers d’insertions uniques pendant la journée, regroupez les insertions pour les charger en masse.  Développez votre processus pour ajouter les insertions uniques à un fichier, puis créez un autre processus qui charge régulièrement le fichier.

### <a name="create-statistics-after-the-load"></a>Créer des statistiques après le chargement

Pour améliorer les performances de vos requêtes, il est important de créer les statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou après toute modification substantielle dans les données.  Pour plus d’informations sur les statistiques, consultez [Statistiques][Statistiques]. L’exemple suivant crée des statistiques sur cinq colonnes de la table Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Passer d’une clé de stockage à une autre
En matière de sécurité, il est recommandé de modifier régulièrement la clé d’accès à votre stockage d’objets blob. Vous disposez de deux clés de stockage pour votre compte de stockage d’objets blob. Cela permet d’effectuer de passer d’une clé à une autre.

Pour passer d’une clé de compte de stockage Azure à une autre :

1. Créez un deuxième fichier d’informations d’identification de niveau base de données en fonction de la clé d’accès de stockage secondaire.
2. Créez une deuxième source de données externe en fonction de ces nouvelles informations d’identification.
3. Supprimez et créez les tables externes de façon qu’elles pointent vers les nouvelles sources de données externes. 

Après la migration de vos tables externes vers la nouvelle source de données, effectuez ces tâches de nettoyage :

1. Suppression de la première source de données externe.
2. Suppression du premier fichier d’informations d’identification de niveau base de données en fonction de la clé d’accès de stockage primaire.
3. Connexion à Azure et régénération de la clé d’accès primaire, afin qu’elle soit prête pour votre prochaine rotation.


## <a name="next-steps"></a>Étapes suivantes
Pour surveiller le processus de chargement, consultez [Surveiller votre charge de travail à l’aide de vues de gestion dynamique](sql-data-warehouse-manage-monitor.md).



