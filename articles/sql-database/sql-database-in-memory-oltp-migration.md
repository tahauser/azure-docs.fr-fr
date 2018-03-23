---
title: L’OLTP en mémoire améliore les performances SQL txn | Microsoft Azure
description: Adopter In-Memory OLTP pour améliorer les performances transactionnelles dans une base de données SQL existante.
services: sql-database
author: jodebrui
manager: craigg
ms.reviewer: MightyPen
ms.service: sql-database
ms.custom: develop databases
ms.topic: article
ms.date: 11/22/2016
ms.author: jodebrui
ms.openlocfilehash: 77e73ec1004babb5fce1e293acfade9264cd6945
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-in-memory-oltp-to-improve-your-application-performance-in-sql-database"></a>Utilisation de l’OLTP en mémoire pour améliorer les performances de votre SQL Database
[L’OLTP en mémoire](sql-database-in-memory.md) peut être utilisé pour améliorer les performances de traitement des transactions, l’ingestion des données et des scénarios de données temporaires, dans des bases de données SQL Azure [Premium](sql-database-service-tiers.md) sans augmenter le niveau tarifaire. 

> [!NOTE] 
> Découvrez comment le [Quorum double la charge de travail de la base de données clé tout en réduisant les DTU de 70 % avec la SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)


Suivez ces étapes pour adopter OLTP en mémoire dans votre base de données existante.

## <a name="step-1-ensure-you-are-using-a-premium-database"></a>Étape 1 : vérifiez que vous utilisez une base de données Premium
OLTP en mémoire est pris en charge uniquement dans les bases de données Premium. In-Memory est pris en charge si le résultat retourné est 1 (et nom 0) :

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

L’acronyme *XTP* signifie *Extreme Transaction Processing (Traitement de transactions extrêmes)*



## <a name="step-2-identify-objects-to-migrate-to-in-memory-oltp"></a>Étape 2 : identifier les objets à migrer vers In-Memory OLTP
SSMS inclut un rapport de **présentation d’analyse des performances des transactions** que vous pouvez exécuter sur une base de données avec une charge de travail active. Le rapport identifie les tables et procédures stockées candidates pour la migration vers In-Memory OLTP.

Dans SSMS, pour générer le rapport :

* Dans l’ **Explorateur d’objets**, cliquez avec le bouton droit sur le nœud de votre base de données.
* Cliquez sur **Rapports** > **Rapports Standard** > **Présentation de l’analyse des performances des transactions**.

Pour plus d’informations, voir [Déterminer si une table ou une procédure stockée doit être déplacée vers In-Memory OLTP](http://msdn.microsoft.com/library/dn205133.aspx).

## <a name="step-3-create-a-comparable-test-database"></a>Étape 3 : Créer une base de données de test comparable
Supposons que le rapport indique que votre base de données est équipée d’une table qui gagnerait à être convertie en table à mémoire optimisée. Nous vous recommandons de commencer par effectuer un test pour confirmer l’indication.

Vous avez besoin d’une copie de test de votre base de données de production. La base de données de test doit être au même niveau de service que votre base de données de production.

Pour faciliter le test, modifiez votre base de données comme suit :

1. Connectez-vous à la base de données de test à l’aide de SSMS.
2. Pour éviter d’avoir recours à l’option WITH (SNAPSHOT) dans les requêtes, définissez l’option de base de données, comme indiqué dans l’instruction T-SQL suivante :
   
   ```
   ALTER DATABASE CURRENT
    SET
        MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
   ```

## <a name="step-4-migrate-tables"></a>Étape 4 : Migrer les tables
Vous devez créer et remplir une copie de mémoire optimisée de la table que vous souhaitez tester. Vous pouvez le créer en utilisant soit :

* L’Assistant Optimisation de mémoire pratique en SSMS.
* T-SQL manuel.

#### <a name="memory-optimization-wizard-in-ssms"></a>Assistant Optimisation de mémoire en SSMS
Pour utiliser cette option de migration :

1. Se connecter à la base de données de test avec SSMS.
2. Dans **l’Explorateur d’objets**, cliquez avec le bouton droit sur la table, puis cliquez sur **Conseiller d’optimisation de la mémoire**.
   
   * L’assistant **Conseiller d’optimiseur de mémoire de table** s’affiche.
3. Dans l’assistant, cliquez sur **Validation de migration** (ou sur le bouton **Suivant**) pour voir si la table possède des fonctionnalités non prises en charge dans les tables optimisées par mémoire. Pour plus d'informations, consultez les pages suivantes :
   
   * La *liste de vérification d’optimisation de mémoire* dans [Memory Optimization Advisor](http://msdn.microsoft.com/library/dn284308.aspx).
   * [Constructions Transact-SQL non prises en charge par In-Memory OLTP](http://msdn.microsoft.com/library/dn246937.aspx).
   * [Migration vers In-Memory OLTP](http://msdn.microsoft.com/library/dn247639.aspx).
4. Si la table ne possède pas de fonctions non prises en charge, l’Assistant peut exécuter le schéma et la migration des données pour vous.

#### <a name="manual-t-sql"></a>T-SQL manuel
Pour utiliser cette option de migration :

1. Connectez-vous à votre base de données de test à l’aide de SSMS (ou utilitaire similaire).
2. Obtenir le script T-SQL complet pour votre table et ses index.
   
   * Dans SSMS, cliquez avec le bouton droit sur le nœud de votre table.
   * Cliquez sur **Générer un script de la table en tant que** > **CREATE To** > **Fenêtre de nouvelle requête**.
3. Dans la fenêtre de script, ajoutez WITH (MEMORY_OPTIMIZED = ON) à l’instruction CREATE TABLE.
4. S’il existe un index CLUSTERED (ORDONNÉ) en clusters, le transformer en NONCLUSTERED (NON ORDONNÉ).
5. Renommez la table existante à l’aide de SP_RENAME.
6. Créez la nouvelle copie de la table de mémoire optimisée si la table en exécutant le script CREATE TABLE modifié.
7. Copiez les données dans votre table mémoire optimisée à l’aide d’INSERT... SELECT * INTO :

```
INSERT INTO <new_memory_optimized_table>
        SELECT * FROM <old_disk_based_table>;
```


## <a name="step-5-optional-migrate-stored-procedures"></a>Étape 5 (facultative) : migrer les procédures stockées
La fonctionnalité In-Memory peut également modifier une procédure stockée pour améliorer les performances.

### <a name="considerations-with-natively-compiled-stored-procedures"></a>Considérations relatives à des procédures stockées compilées en mode natif
Une procédure stockée compilée en mode natif doit avoir les options suivantes dans sa clause WITH T-SQL :

* NATIVE_COMPILATION
* SCHEMABINDING : les tables significatives figurant dans la procédure stockée ne peuvent voir leurs définitions modifiées de quelque manière que ce soit susceptible d’affecter la procédure stockée, à moins que vous ne glissiez la procédure stockée.

Un module natif doit utiliser un grand [bloc ATOMIC](http://msdn.microsoft.com/library/dn452281.aspx) pour la gestion de transaction. Il n’existe aucun rôle pour une instruction BEGIN TRANSACTION ou ROLLBACK TRANSACTION explicite. Si votre code détecte une violation de règle métier, il peut terminer le bloc atomique avec une instruction [THROW](http://msdn.microsoft.com/library/ee677615.aspx) .

### <a name="typical-create-procedure-for-natively-compiled"></a>En général, CREATE PROCEDURE compilée en mode natif
En général, le code T-SQL pour créer une procédure stockée compilée en mode natif est similaire au modèle suivant :

```
CREATE PROCEDURE schemaname.procedurename
    @param1 type1, …
    WITH NATIVE_COMPILATION, SCHEMABINDING
    AS
        BEGIN ATOMIC WITH
            (TRANSACTION ISOLATION LEVEL = SNAPSHOT,
            LANGUAGE = N'your_language__see_sys.languages'
            )
        …
        END;
```

* Pour le niveau TRANSACTION_ISOLATION_LEVEL, SNAPSHOT est la valeur la plus commune pour la procédure stockée compilée de façon native. Cependant, un sous-ensemble d’autres valeurs est également pris en charge :
  
  * REPEATABLE READ
  * SERIALIZABLE
* La valeur LANGUAGE doit être présente dans la vue sys.languages.

### <a name="how-to-migrate-a-stored-procedure"></a>Comment migrer une procédure stockée
Les étapes de la migration sont les suivantes :

1. Obtenir le script CREATE PROCEDURE pour la procédure stockée régulière interprétée.
2. Réécrire son en-tête pour qu’il corresponde au modèle précédent.
3. Déterminer si le code TSQL de procédure stocké utilise les fonctions non prises en charge pour les procédures stockées compilées en mode natif. Mettre en œuvre des solutions de contournement si nécessaire.
   
   * Pour plus d’informations, voir [Problèmes de migration pour les procédures stockées compilées natives](http://msdn.microsoft.com/library/dn296678.aspx).
4. Renommez l’ancienne procédure stockée en utilisant SP_RENAME. Ou FAITES-LE SIMPLEMENT GLISSER (DROP).
5. Exécutez le script CREATE PROCEDURE T-SQL modifié.

## <a name="step-6-run-your-workload-in-test"></a>Étape 6 : exécuter votre charge de travail dans un test
L’exécution d’une charge de travail dans votre base de données de test est similaire à la charge de travail qui s’exécute dans votre base de données de production. Cela doit révéler les gains de performances générés par votre utilisation de la fonction In-Memory pour les tables et les procédures stockées.

Les principaux attributs de la charge de travail sont :

* Nombre de connexions simultanées.
* Rapport Lecture/Écriture

Pour adapter et exécuter la charge de travail de test, envisagez d’utiliser l’outil pratique ostress.exe, figurant [ici](sql-database-in-memory.md).

Pour réduire la latence du réseau, exécutez le test dans la région géographique Azure dans laquelle la base de données existe.

## <a name="step-7-post-implementation-monitoring"></a>Étape 7 : surveillance post exécution
Veillez à surveiller les effets des performances de vos implémentations In-Memory en production :

* [Surveiller le stockage en mémoire](sql-database-in-memory-oltp-monitoring.md).
* [Analyse d’une base de données SQL Azure à l’aide de vues de gestion dynamique](sql-database-monitoring-with-dmvs.md)

## <a name="related-links"></a>Liens connexes
* [In-Memory OLTP (optimisation en mémoire)](http://msdn.microsoft.com/library/dn133186.aspx)
* [Présentation des procédures stockées compilées en mode natif](http://msdn.microsoft.com/library/dn133184.aspx)
* [Conseil d’optimisation par mémoire](http://msdn.microsoft.com/library/dn284308.aspx)

