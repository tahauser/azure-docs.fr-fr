---
title: "Sécuriser une base de données dans SQL Data Warehouse | Microsoft Docs"
description: "Conseils relatifs à la sécurisation d’une base de données dans Microsoft Azure SQL Data Warehouse, dans le cadre du développement de solutions."
services: sql-data-warehouse
documentationcenter: NA
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 8fa2f5ca-4cf5-4418-99a2-4dc745799850
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 12/14/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: efc0ca9b156bd69a39197d40083830c6c7e77647
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2018
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Sécuriser une base de données dans SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
> * [Authentification](sql-data-warehouse-authentication.md)
> * [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
> * [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Cet article présente les principes de base de la sécurisation de votre base de données Microsoft Azure SQL Data Warehouse. Plus spécifiquement, cet article vous offre un aperçu sur les ressources dédiées à la limitation de l’accès, à la protection des données et à la surveillance des activités sur une base de données.

## <a name="connection-security"></a>Sécurité de la connexion
L’expression « sécurité de la connexion » fait référence au mode de restriction et de sécurisation appliqué aux connexions à votre base de données, au moyen de règles de pare-feu et d’une fonction de chiffrement des connexions.

Les règles de pare-feu sont utilisées par le serveur et la base de données pour refuser toute tentative de connexion d’une adresse IP qui ne fait pas partie d’une liste approuvée explicite. Pour autoriser les connexions à partir de l’adresse IP publique de l’ordinateur client ou de votre application, vous devez d’abord créer une règle de pare-feu au niveau du serveur à l’aide du portail Azure, de l’API REST ou de PowerShell. Nous vous recommandons, à titre de meilleure pratique, de limiter autant que possible le nombre de plages d’adresses IP autorisées à traverser le pare-feu de votre serveur.  Pour accéder à SQL Data Warehouse à partir de votre ordinateur local, vérifiez que le pare-feu sur votre réseau et l’ordinateur local autorise les communications sortantes sur le port TCP 1433.  

SQL Data Warehouse utilise des règles de pare-feu au niveau du serveur. Il ne prend pas en charge les règles de pare-feu au niveau de la base de données. Pour plus d’informations, consultez [Pare-feu Azure SQL Database][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule].

Par défaut, les connexions à SQL Data Warehouse sont chiffrées.  La modification des paramètres de connexion pour désactiver le chiffrement est ignorée.

## <a name="authentication"></a>Authentification
Le terme « authentification » fait référence au processus de validation de votre identité lorsque vous vous connectez à la base de données. SQL Data Warehouse prend actuellement en charge l’authentification SQL Server avec un nom d’utilisateur et un mot de passe, et avec Azure Active Directory. 

Lorsque vous avez créé un serveur logique pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données, ou « dbo » via l’authentification SQL Server.

Toutefois, au titre de meilleure pratique, il est recommandé que les utilisateurs de votre organisation utilisent un compte différent pour s’authentifier. Cela vous permet de limiter les autorisations accordées à cette application et de réduire les risques d’activité malveillante, au cas où le code de votre application serait vulnérable à une attaque par injection de code SQL. 

Pour créer un utilisateur authentifié par SQL Server, connectez-vous à la base de données **master** sur votre serveur avec votre identifiant de connexion d’administrateur du serveur, et créez un nouvel identifiant de connexion au serveur.  En outre, il est judicieux de créer un utilisateur dans la base de données master pour les utilisateurs d’Azure SQL Data Warehouse. La création d’un utilisateur dans la base de données master permet à un utilisateur de se connecter à l’aide d’outils tels que SSMS sans spécifier de nom de base de données.  Elle permet également d’utiliser l’Explorateur d’objets pour afficher toutes les bases de données sur un serveur SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ensuite, connectez-vous à votre **base de données SQL Data Warehouse** avec votre identifiant de connexion d’administrateur du serveur, puis créez un utilisateur de base de données basé sur l’identifiant de connexion au serveur que vous avez créé.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Pour autoriser un utilisateur à effectuer d’autres opérations telles que la création de connexions ou de bases de données, affectez-le aux rôles `Loginmanager` et `dbmanager` dans la base de données master. Pour en savoir plus sur ces rôles supplémentaires et sur l’authentification auprès d’une base de données SQL, consultez [Gestion des bases de données et connexions dans Azure SQL Database][Managing databases and logins in Azure SQL Database].  Pour plus d’informations, consultez [Connexion à SQL Data Warehouse avec l’authentification Azure Active Directory][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorisation
L’autorisation fait référence à ce que vous pouvez faire dans une base de données Azure SQL Data Warehouse. Les privilèges d’autorisation sont déterminés par les autorisations et les appartenances aux rôles. Nous vous recommandons, à titre de meilleure pratique, d’accorder aux utilisateurs des privilèges aussi réduits que possible. Pour gérer les rôles, vous pouvez utiliser les procédures stockées suivantes :

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Le compte d’administrateur du serveur avec lequel vous vous connectez appartient au groupe « db_owner », qui est autorisé à effectuer tout type d’opérations dans la base de données. Enregistrez ce compte pour l’utiliser lors du déploiement des mises à niveau de schéma et d’autres opérations de gestion. Utilisez le compte « ApplicationUser », doté d’autorisations plus limitées, pour vous connecter à la base de données à partir de votre application, en bénéficiant du niveau de privilèges le moins élevé requis par cette dernière.

D’autres méthodes permettent de limiter les actions de l’utilisateur dans Azure SQL Data Warehouse :

* Des [autorisations][Permissions] granulaires vous permettent de contrôler les opérations que vous pouvez exécuter sur des colonnes, des tables, des vues, des schémas, des procédures et d’autres objets individuels dans la base de données. Utilisez les autorisations granulaires pour avoir un contrôle optimal et accordez les autorisations minimales nécessaires. 
* Les [rôles de base de données][Database roles] autres que « db_datareader » et « db_datawriter » peuvent être utilisés pour créer des comptes d’utilisateur plus puissants ou des comptes de gestion moins puissants pour votre application. Les rôles de base de données fixes intégrés offrent un moyen facile d'accorder des autorisations, mais peuvent entraîner l'octroi d'autorisations excessives.
* Les [procédures stockées][Stored procedures] vous permettent de limiter le nombre d’actions susceptibles d’être exécutées sur la base de données.

L’exemple suivant accorde un accès en lecture à un schéma défini par l’utilisateur.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

La gestion des bases de données et serveurs logiques à partir du portail Azure et l’utilisation de l’API Azure Resource Manager sont contrôlées par les attributions de rôle de votre compte d’utilisateur sur le portail. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle dans le portail Azure][Role-based access control in Azure portal].

## <a name="encryption"></a>Chiffrement
Azure SQL Data Warehouse Transparent Data Encryption (TDE) vous aide à vous protéger contre les menaces d’activités malveillantes en chiffrant et en déchiffrant vos données au repos.  Lorsque vous chiffrez votre base de données, les fichiers de sauvegardes et les journaux de transactions associés sont chiffrés, sans que cela ne nécessite de modifications de vos applications. Le chiffrement transparent des données chiffre le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. 

Dans SQL Database, la clé de chiffrement de base de données est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur de base de données SQL. Microsoft alterne automatiquement ces certificats au moins tous les 90 jours. L’algorithme de chiffrement utilisé par SQL Data Warehouse est AES-256. Pour obtenir une description générale du chiffrement transparent des données, consultez la page [Transparent Data Encryption][Transparent Data Encryption].

Vous pouvez chiffrer votre base de données à l’aide du [portail Azure][Encryption with Portal] ou de [T-SQL][Encryption with TSQL].

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la connexion à SQL Data Warehouse avec différents protocoles et voir des exemples, consultez [Se connecter à SQL Data Warehouse][Connect to SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Connect to SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Encryption with Portal]: ./sql-data-warehouse-encryption-tde.md
[Encryption with TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Azure SQL Database firewall]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Database roles]: https://msdn.microsoft.com/library/ms189121.aspx
[Managing databases and logins in Azure SQL Database]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissions]: https://msdn.microsoft.com/library/ms191291.aspx
[Stored procedures]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Azure portal]: https://portal.azure.com/

<!--Other Web references-->
[Role-based access control in Azure portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
