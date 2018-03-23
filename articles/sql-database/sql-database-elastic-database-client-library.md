---
title: Conception de bases de données cloud évolutives | Microsoft Docs
description: Créez des applications de base de données .NET évolutives avec la bibliothèque cliente de bases de données élastiques
services: sql-database
manager: jhubbard
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/12/2017
ms.author: sstein
ms.openlocfilehash: e15254fd529d98f2b92acea8dd1ed908a82841ea
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="building-scalable-cloud-databases"></a>Conception de bases de données cloud évolutives
La montée en charge des bases de données peut être aisément accomplie à l’aide de fonctionnalités et d’outils évolutifs de bases de données SQL Azure. En particulier, vous pouvez utiliser la **Bibliothèque cliente de bases de données élastiques** pour créer et gérer des bases de données avec montée en charge. Cette fonctionnalité vous permet de développer facilement des applications partitionnées à l'aide de centaines, voire de milliers, de bases de données SQL Azure. [tâches élastiques](sql-database-elastic-jobs-powershell.md) pour faciliter la gestion de ces bases de données.

Pour télécharger :
* la version Java de la bibliothèque, consultez le dépôt [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* la version .NET de la bibliothèque, consultez [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="documentation"></a>Documentation
1. [Prise en main des outils de base de données élastiques](sql-database-elastic-scale-get-started.md)
2. [Fonctionnalités des bases de données élastiques](sql-database-elastic-scale-introduction.md)
3. [Gestion des cartes de partitions](sql-database-elastic-scale-shard-map-management.md)
4. [Migration de bases de données existantes pour une mise à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md)
5. [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md)
6. [Requêtes sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md)
7. [Ajout d’une partition à l’aide des outils de base de données élastique](sql-database-elastic-scale-add-a-shard.md)
8. [Applications multi-locataires avec des outils de base de données élastique et la sécurité au niveau des lignes](sql-database-elastic-tools-multi-tenant-row-level-security.md)
9. [Mettre à niveau les applications de la bibliothèque cliente](sql-database-elastic-scale-upgrade-client-library.md) 
10. [Vue d'ensemble des requêtes élastiques](sql-database-elastic-query-overview.md)
11. [Glossaire des outils de base de données élastique](sql-database-elastic-scale-glossary.md)
12. [Bibliothèque cliente de la base de données élastique avec Entity Framework](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
13. [Bibliothèque cliente de bases de données élastiques avec Dapper](sql-database-elastic-scale-working-with-dapper.md)
14. [Outil de fractionnement et de fusion](sql-database-elastic-scale-overview-split-and-merge.md)
15. [Compteurs de performances pour le Gestionnaire de cartes de partitions](sql-database-elastic-database-client-library.md) 
16. [FAQ des outils de bases de données élastiques](sql-database-elastic-scale-faq.md)

## <a name="client-capabilities"></a>Fonctionnalités du client
La montée en charge des applications à l’aide du *partitionnement* présente des défis pour le développeur, ainsi que pour l’administrateur. La bibliothèque cliente simplifie les tâches de gestion en fournissant des outils qui permettent à la fois aux développeurs et administrateurs de gérer des bases de données avec montée en charge. Dans un exemple classique, il existe plusieurs bases de données, nommées « partitions », à gérer. Les clients sont colocalisés dans la même base de données et il y a une base de données par client (schéma à locataire unique). La bibliothèque cliente comprend ces fonctionnalités :

- **Gestion des cartes de partitions**: une base de données spéciale appelée « gestionnaire des cartes de partitions » est créée. La gestion des cartes de partitions est la capacité d’une application à gérer les métadonnées sur ses partitions. Les développeurs peuvent utiliser cette fonctionnalité pour enregistrer des bases de données, décrire les mappages de clés de partitionnement individuelles ou de plages de clés pour ces bases de données et gérer ces métadonnées en même temps que le nombre et la composition des bases de données évoluent en fonction des changements de capacités. Sans la bibliothèque cliente de base de données élastique, vous passeriez beaucoup de temps à écrire le code de gestion lors de l'implémentation du partitionnement. Pour plus d'informations, consultez [Gestion des cartes de partitions](sql-database-elastic-scale-shard-map-management.md).

- **Routage dépendant des données**: imaginez une requête arrivant dans l'application. Selon la valeur de la clé de partitionnement de la demande, l'application doit déterminer la base de données correcte en fonction de la valeur de clé. Elle ouvre ensuite une connexion à la base de données pour traiter la demande. Le routage dépendant des données fournit la possibilité d'ouvrir des connexions avec un seul appel simple de la carte de partitions de l'application. Le routage dépendant des données est un autre aspect du code d’infrastructure qui est maintenant couvert par les fonctionnalités de la bibliothèque cliente de base de données élastique. Pour plus d'informations, consultez [Routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md).
- **Requêtes sur plusieurs partitions**: l'interrogation de plusieurs partitions fonctionne lorsqu'une demande implique plusieurs (ou toutes les) partitions. Une requête sur plusieurs partitions exécute le même code T-SQL sur toutes les partitions ou un ensemble de partitions. Les résultats provenant des partitions participantes sont fusionnés en un résultat global défini à l'aide de la sémantique UNION ALL. La fonctionnalité, telle qu’exposée via la bibliothèque cliente, assure de nombreuses tâches, notamment : la gestion des connexions, la gestion des threads, la gestion des erreurs et le traitement des résultats intermédiaires. Les requêtes sur plusieurs partitions peuvent interroger des centaines de partitions. Pour plus d'informations, consultez [Requête sur plusieurs partitions](sql-database-elastic-scale-multishard-querying.md).

De manière générale, les clients utilisant les outils de base de données élastique peuvent s’attendre à obtenir toutes les fonctionnalités T-SQL lors de l’envoi d’opérations de partitions locales, à la différence des opérations entre plusieurs partitions qui ont leur propre sémantique.



## <a name="next-steps"></a>Étapes suivantes

- Bibliothèque cliente de bases de données élastiques ([Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22), [.NET](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)) : pour **télécharger** la bibliothèque.

- [Bien démarrer avec les outils de bases de données élastiques](sql-database-elastic-scale-get-started.md) : Pour obtenir un **exemple d’application** illustrant les fonctions des clients.

- GitHub ([Java](https://github.com/Microsoft/elastic-db-tools-for-java/blob/master/README.md), [.NET](https://github.com/Azure/elastic-db-tools)) : pour contribuer au code.
- [Vue d’ensemble des requêtes élastiques dans Azure SQL Database](sql-database-elastic-query-overview.md) pour utiliser des requêtes élastiques.

- [Déplacement de données entre bases de données cloud mises à l’échelle](sql-database-elastic-scale-overview-split-and-merge.md) pour obtenir des instructions sur l’utilisation de **l’outil de fractionnement et de fusion**.



<!-- Additional resources H2 -->

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]


<!--Anchors-->
<!--Image references-->

[1]: ./media/sql-database-elastic-database-client-library/glossary.png

