---
title: "Résolution des différences de T-SQL durant la migration vers Azure SQL Database | Microsoft Docs"
description: "Instructions Transact-SQL qui ne sont pas entièrement prises en charge dans Azure SQL Database"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: c05abd9e-28a7-4c97-9bdf-bc60d08fc92e
ms.service: sql-database
ms.custom: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 10/23/2017
ms.author: carlrab
ms.openlocfilehash: f311c0d139d5ec35cbd85a34bd5a5e991bccba3a
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="resolving-transact-sql-differences-during-migration-to-sql-database"></a>Résolution des différences de Transact-SQL durant la migration vers SQL Database   
Au moment de [migrer votre base de données](sql-database-cloud-migrate.md) SQL Server vers Azure SQL Server, vous découvrirez peut-être que sa conception est à revoir avant de pouvoir effectuer la migration. Cet article comprend des informations vous permettant d’apporter les révisions nécessaires et de comprendre les raisons sous-jacentes à ces changements. Pour détecter les incompatibilités, utilisez [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595).

## <a name="overview"></a>Vue d'ensemble
La plupart des fonctionnalités Transact-SQL utilisées par les applications sont entièrement prises en charge dans Microsoft SQL Server et Azure SQL Database. Par exemple, les principaux composants SQL tels que les types de données, les opérateurs ainsi que les fonctions de chaîne, arithmétiques, logiques et de curseur, fonctionnent de la même façon dans SQL Server et SQL Database. Il existe toutefois quelques différences au niveau des éléments du langage de définition de données (DDL) et des éléments du langage de manipulation de données (DML). Ceux-ci génèrent en effet des instructions et des requêtes T-SQL qui ne sont que partiellement prises en charge (nous y reviendrons plus loin dans cet article).

Par ailleurs, Azure SQL Database étant conçu pour isoler les fonctionnalités des dépendances sur la base de données MASTER et le système d’exploitation, certaines fonctionnalités et syntaxes ne sont pas du tout prises en charge. Ainsi, de nombreuses activités de niveau serveur sont inappropriées pour SQL Database. Les instructions et les options T-SQL ne sont pas disponibles si elles configurent des options de niveau serveur et des composants du système d’exploitation, ou spécifient la configuration du système de fichiers. Si de telles fonctionnalités sont nécessaires, vous trouverez la plupart du temps une alternative appropriée en procédant différemment à partir de SQL Database ou d’un autre service/d’une autre fonctionnalité Azure. 

Par exemple, la haute disponibilité étant intégrée à Azure, il est inutile de configurer Always On (bien que vous puissiez configurer la géoréplication active pour accélérer la récupération d’urgence). Les instructions T-SQL relatives aux groupes de disponibilité ne sont donc pas prises en charge par SQL Database, de même que les vues de gestion dynamique associées à Always On.

Pour obtenir la liste des fonctionnalités prises en charge et non prises en charge par SQL Database, consultez [Comparaison des fonctionnalités d’Azure SQL Database](sql-database-features.md). Cette liste, qui porte sur les instructions Transact-SQL, vient compléter l’article traitant des instructions et des fonctionnalités.

## <a name="transact-sql-syntax-statements-with-partial-differences"></a>Instructions Transact-SQL avec des différences partielles
Les principales instructions DDL sont disponibles, mais certaines présentent des extensions liées à l’emplacement du disque et des fonctionnalités non prises en charge. 

- Les instructions CREATE et ALTER DATABASE ont plus d’une trentaine d’options. Les instructions incluent des options liées à l’emplacement des fichiers, à FILESTREAM et à Service Broker qui s’appliquent uniquement à SQL Server. Ceci peut être sans importance si vous créez des bases de données avant la migration. Mais si vous migrez du code T-SQL qui crée des bases de données, vous devez comparer [CREATE DATABASE (Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) à la syntaxe SQL Server décrite dans [CREATE DATABASE (SQL Server Transact-SQL)](https://msdn.microsoft.com/library/ms176061.aspx) pour vérifier que toutes les options que vous utilisez sont prises en charge. CREATE DATABASE pour Azure SQL Database compte également des options d’objectif de service et de mise à l'échelle élastique qui s’appliquent uniquement à SQL Database.
- Les instructions CREATE et ALTER TABLE offrent des options FileTable que vous ne pouvez pas utiliser sur SQL Database dans la mesure où FILESTREAM n’est pas pris en charge.
- Les instructions CREATE et ALTER LOGIN sont prises en charge, mais SQL Database n’offre pas toutes les options. Pour accroître la portabilité de votre base de données, SQL Database recommande, dans la mesure du possible, de remplacer les connexions par des utilisateurs de base de données à relation contenant-contenu. Pour plus d’informations, consultez [CREATE/ALTER LOGIN](https://msdn.microsoft.com/library/ms189828.aspx) et [Contrôle et octroi de l’accès à la base de données](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins).

## <a name="transact-sql-syntax-not-supported-in-azure-sql-database"></a>Syntaxe Transact-SQL non prise en charge dans Azure SQL Database   
Outre les instructions Transact-SQL liées aux fonctionnalités non prises en charge décrites dans [Comparaison des fonctionnalités d’Azure SQL Database](sql-database-features.md), les instructions et groupes d’instructions suivants ne sont pas pris en charge. Si la base de données à migrer utilise les fonctionnalités et instructions T-SQL suivantes, vous devez donc changer votre code T-SQL pour les éliminer.

- Classement des objets système
- Connexions associées : instructions de point de terminaison. SQL Database ne prend pas en charge l’authentification Windows, mais prend en charge l’authentification Azure Active Directory similaire. Certains types d’authentification nécessitent la version la plus récente de SSMS. Pour plus d’informations, voir [Connexion à SQL Database ou SQL Data Warehouse avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).
- Requêtes dans plusieurs bases de données à l’aide de noms à trois ou quatre parties. (Les requêtes sur plusieurs bases de données en lecture seule sont prises en charge à l’aide d’une [requête de base de données élastique](sql-database-elastic-query-overview.md).)
- Chaînage d’appartenance entre plusieurs bases de données, paramètre `TRUSTWORTHY`
- `EXECUTE AS LOGIN` Utilisez « EXECUTE AS USER » à la place.
- Le chiffrement est pris en charge, à l’exception de la gestion de clés extensible.
- Gestion des événements : événements, notifications d’événements, notifications de requête.
- Emplacement des fichiers : syntaxe liée à l’emplacement du fichier de la base de données, à la taille et aux fichiers de la base de données qui sont gérés automatiquement par Microsoft Azure.
- Haute disponibilité : syntaxe liée à haute disponibilité, qui est gérée par le biais de votre compte Microsoft Azure. Cela inclut la syntaxe pour la sauvegarde, la restauration, la fonctionnalité AlwaysOn, la mise en miroir de bases de données, la copie des journaux de transaction et les modes de récupération.
- Lecture du journal : syntaxe reposant sur le lecteur de journal, qui n’est pas disponible dans SQL Database (réplication par émission, modification de la capture de données). SQL Database peut être un abonné d’un article de réplication par émission.
- Fonctions : `fn_get_sql`, `fn_virtualfilestats`,`fn_virtualservernodes`
- Matériel : syntaxe pour les paramètres du serveur relatifs au matériel (mémoire, threads de travail, affinité du processeur, indicateurs de trace, etc.). Utilisez plutôt les niveaux de service.
- `KILL STATS JOB`
- `OPENQUERY`, `OPENROWSET`, `OPENDATASOURCE` et noms en quatre parties
- .NET Framework : intégration du CLR à SQL Server
- Recherche sémantique
- Informations d’identification du serveur : utilisez à la place les [informations d’identification incluses dans l’étendue de la base de données](https://msdn.microsoft.com/library/mt270260.aspx).
- Éléments au niveau du serveur : rôles de serveur, `sys.login_token`. `GRANT`, `REVOKE` et `DENY` des autorisations de niveau serveur ne sont pas disponibles, bien que certains soient remplacés par des autorisations au niveau base de données. Certaines vues de gestion dynamique utiles au niveau du serveur ont des vues de gestion dynamique équivalentes au niveau de la base de données.
- `SET REMOTE_PROC_TRANSACTIONS`
- `SHUTDOWN`
- `sp_addmessage`
- Options `sp_configure` et `RECONFIGURE`. Certaines options sont disponibles à l’aide [d’ALTER DATABASE SCOPED CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx).
- `sp_helpuser`
- `sp_migrate_user_to_contained`
- SQL Server Agent : syntaxe reposant sur SQL Server Agent ou la base de données MSDB (alertes, opérateurs, serveurs d’administration centrale). Utilisez des scripts tels qu’Azure PowerShell à la place.
- Audit SQL Server : utilisez plutôt l’audit SQL Database.
- SQL Server trace
- Indicateurs de trace : certains éléments d’indicateur de trace ont été déplacés vers les modes de compatibilité.
- Débogage Transact-SQL
- Déclencheurs : déclencheurs au niveau du serveur ou de connexion
- Instruction `USE` : pour basculer le contexte de base de données vers une autre base de données, vous devez établir une nouvelle connexion à la nouvelle base de données.

## <a name="full-transact-sql-reference"></a>Référence complète Transact-SQL
Pour plus d'informations sur la grammaire, l'utilisation et les exemples Transact-SQL, consultez [Référence Transact-SQL (moteur de la base de données)](https://msdn.microsoft.com/library/bb510741.aspx) dans la documentation en ligne de SQL Server. 

### <a name="about-the-applies-to-tags"></a>À propos des balises « S’applique à »
La référence sur Transact-SQL comprend des articles relatifs aux versions de SQL Server de 2008 jusqu'à présent. Sous le titre de l’article se trouve une barre d’icônes qui répertorie les quatre plateformes SQL Server et indique l’applicabilité. Par exemple, la fonction des groupes de disponibilité ont été introduits dans SQL Server 2012. L’article [CREATE AVAILABILTY GROUP](https://msdn.microsoft.com/library/ff878399.aspx) indique que l’instruction s’applique à **SQL Server (à partir de 2012)**. L’instruction ne s’applique pas à SQL Server 2008, SQL Server 2008 R2, Azure SQL Database, Azure SQL Data Warehouse ni à Parallel Data Warehouse.

Dans certains cas, le sujet général d'un article peut être utilisé dans un produit, mais il existe des différences mineures entre les produits. Les différences sont indiquées dans l’article comme il convient. Dans certains cas, le sujet général d'un article peut être utilisé dans un produit, mais il existe des différences mineures entre les produits. Les différences sont indiquées dans l’article comme il convient. Par exemple, l’article CREATE TRIGGER est disponible dans SQL Database. Mais l’option **ALL SERVER** pour les déclencheurs de niveau serveur indique que ces derniers ne peuvent pas être utilisés dans SQL Database. Utilisez plutôt des déclencheurs de niveau base de données.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir la liste des fonctionnalités prises en charge et non prises en charge par SQL Database, consultez [Comparaison des fonctionnalités d’Azure SQL Database](sql-database-features.md). Cette liste, qui porte sur les instructions Transact-SQL, vient compléter l’article traitant des instructions et des fonctionnalités.

