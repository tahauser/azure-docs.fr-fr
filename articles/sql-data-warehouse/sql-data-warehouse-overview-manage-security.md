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
ms.date: 10/31/2016
ms.author: rortloff;barbkess
ms.openlocfilehash: 36f990dd16a3c6b65d16bab4b945ec56a1bb1000
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
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

Les règles de pare-feu sont utilisées par le serveur et la base de données pour refuser toute tentative de connexion d’une adresse IP qui ne fait pas partie d’une liste approuvée explicite. Pour permettre une connexion depuis l’adresse IP publique de l’ordinateur client ou votre application, vous devez d’abord créer une règle de pare-feu au niveau du serveur à l’aide du portail Azure, d’une API REST ou de PowerShell. Nous vous recommandons, à titre de meilleure pratique, de limiter autant que possible le nombre de plages d’adresses IP autorisées à traverser le pare-feu de votre serveur.  Pour accéder à SQL Data Warehouse à partir de votre ordinateur local, vérifiez que le pare-feu sur votre réseau et l’ordinateur local autorise les communications sortantes sur le port TCP 1433.  Pour plus d’informations, consultez [Pare-feu d’Azure SQL Database][Azure SQL Database firewall], [sp_set_firewall_rule][sp_set_firewall_rule] et [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

Par défaut, les connexions à SQL Data Warehouse sont chiffrées.  La modification des paramètres de connexion pour désactiver le chiffrement est ignorée.

## <a name="authentication"></a>Authentification
Le terme « authentification » fait référence au processus de validation de votre identité lorsque vous vous connectez à la base de données. SQL Data Warehouse prend actuellement en charge l’authentification SQL Server avec un nom d’utilisateur et un mot de passe, ainsi qu’Azure Active Directory. 

Lorsque vous avez créé un serveur logique pour votre base de données, vous avez spécifié un compte de connexion « Admin serveur », associé à un nom d’utilisateur et à un mot de passe. À l’aide de ces informations d’identification, vous pouvez vous authentifier auprès de n’importe quelle base de données sur ce serveur, en tant que propriétaire de la base de données, ou « dbo » via l’authentification SQL Server.

Toutefois, au titre de meilleure pratique, il est recommandé que les utilisateurs de votre organisation utilisent un compte différent pour s’authentifier. Cela vous permet de limiter les autorisations accordées à cette application et de réduire les risques d’activité malveillante, au cas où le code de votre application serait vulnérable à une attaque par injection de code SQL. 

Pour créer un utilisateur authentifié par SQL Server, connectez-vous à la base de données **master** sur votre serveur avec votre identifiant de connexion d’administrateur du serveur, et créez un nouvel identifiant de connexion au serveur.  En outre, il est judicieux de créer un utilisateur dans la base de données master pour les utilisateurs d’Azure SQL Data Warehouse. La création d’un utilisateur dans la base de données master permet à un utilisateur de se connecter à l’aide d’outils tels que SSMS sans spécifier un nom de base de données.  Elle permet également d’utiliser l’Explorateur d’objets pour afficher toutes les bases de données sur un serveur SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Ensuite, connectez-vous à votre **base de données SQL Data Warehouse** avec votre identifiant de connexion d’administrateur du serveur, puis créez un utilisateur de base de données basé sur l’identifiant de connexion au serveur que vous venez de créer.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Si un utilisateur effectue d’autres opérations telles que la création de connexions ou d’une base de données, il devra également être affecté aux rôles `Loginmanager` et `dbmanager` dans la base de données master. Pour en savoir plus sur ces rôles supplémentaires et sur l’authentification auprès d’une base de données SQL, consultez la rubrique [Gestion des bases de données et connexions dans Azure SQL Database][Managing databases and logins in Azure SQL Database].  Pour plus de détails sur Azure AD pour SQL Data Warehouse, consultez [Connexion à SQL Data Warehouse avec l’authentification Azure Active Directory][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorisation
Le terme « autorisation » fait référence aux actions que vous pouvez exécuter dans une base de données Microsoft Azure SQL Data Warehouse, actions contrôlées par les autorisations et l’appartenance au rôle de votre compte utilisateur. Nous vous recommandons, à titre de meilleure pratique, d’accorder aux utilisateurs des privilèges aussi réduits que possible. La base de données Microsoft Azure SQL Data Warehouse facilite la gestion des rôles dans T-SQL :

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

Le compte d’administrateur du serveur avec lequel vous vous connectez appartient au groupe « db_owner », qui est autorisé à effectuer tout type d’opérations dans la base de données. Enregistrez ce compte pour l’utiliser lors du déploiement des mises à niveau de schéma et d’autres opérations de gestion. Utilisez le compte « ApplicationUser », doté d’autorisations plus limitées, pour vous connecter à la base de données à partir de votre application, en bénéficiant du niveau de privilèges le moins élevé requis par cette dernière.

Il existe d’autres méthodes pour limiter le nombre d’actions que peut réaliser un utilisateur avec la base de données SQL Microsoft Azure :

* Des [autorisations][Permissions] granulaires vous permettent de contrôler les opérations que vous pouvez exécuter sur des colonnes, des tables, des vues, des procédures et d’autres objets individuels dans la base de données. Utilisez les autorisations granulaires pour avoir un contrôle optimal et accordez les autorisations minimales nécessaires. Le système d'autorisation granulaire est quelque peu compliqué et nécessitera un apprentissage avant de pouvoir l’utiliser efficacement.
* Les [rôles de base de données][Database roles] autres que « db_datareader » et « db_datawriter » peuvent être utilisés pour créer des comptes d’utilisateur plus puissants ou des comptes de gestion moins puissants pour votre application. Les rôles de base de données fixes intégrés offrent un moyen facile d'accorder des autorisations, mais peuvent entraîner l'octroi d'autorisations excessives.
* Les [procédures stockées][Stored procedures] vous permettent de limiter le nombre d’actions susceptibles d’être exécutées sur la base de données.

La gestion des bases de données et serveurs logiques à partir du portail Azure et l’utilisation de l’API Azure Resource Manager sont contrôlées par les attributions de rôle de votre compte d’utilisateur sur le portail. Pour en savoir plus à ce sujet, consultez [Contrôle d’accès en fonction du rôle dans le portail Azure][Role-based access control in Azure Portal].

## <a name="encryption"></a>Chiffrement
Le chiffrement transparent des données de Microsoft Azure SQL Data Warehouse vous aide à vous protéger contre les menaces d’activités malveillantes, par le biais d’un chiffrement et d’un déchiffrement en temps réel de vos données au repos.  Lorsque vous chiffrez votre base de données, les fichiers de sauvegardes et les journaux de transactions associés sont chiffrés, sans que cela ne nécessite de modifications de vos applications. Le chiffrement transparent des données chiffre le stockage d’une base de données entière à l’aide d’une clé symétrique appelée clé de chiffrement de base de données. Dans la base de données SQL, la clé de chiffrement de base de données est protégée par un certificat de serveur intégré. Le certificat de serveur intégré est unique pour chaque serveur de base de données SQL. Microsoft alterne automatiquement ces certificats au moins tous les 90 jours. L’algorithme de chiffrement utilisé par SQL Data Warehouse est AES-256. Pour obtenir une description générale du chiffrement transparent des données, consultez la page [Transparent Data Encryption][Transparent Data Encryption].

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
[Role-based access control in Azure Portal]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure
