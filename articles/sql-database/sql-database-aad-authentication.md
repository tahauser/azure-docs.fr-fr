---
title: Authentification Azure Active Directory - Azure SQL (vue d’ensemble) | Microsoft Docs
description: Découvrez comment utiliser Azure Active Directory pour l’authentification auprès de SQL Database, de Managed Instance et de SQL Data Warehouse
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: cfad53a3f86450163b2e29d5e4d4ed2726ecb0bc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="use-azure-active-directory-authentication-for-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database, de Managed Instance ou de SQL Data Warehouse
L’authentification Azure Active Directory est un mécanisme servant à se connecter aux services Base de données SQL Microsoft Azure et [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) à l’aide d’identités dans Azure Active Directory (Azure AD). Avec l’authentification Azure AD, vous pouvez gérer de manière centralisée les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs de la base de données et simplifie la gestion des autorisations. Les avantages suivants sont inclus :

* Il fournit une alternative à l’authentification SQL Server.
* Permet de bloquer la prolifération des identités utilisateur sur plusieurs serveurs de base de données.
* Permet la rotation de mot de passe à un emplacement unique
* Les clients peuvent gérer les autorisations de base de données à l’aide des groupes (Azure AD) externes.
* Il peut éliminer le stockage des mots de passe en activant l’authentification intégrée Windows et les autres formes d’authentification prises en charge par Azure Active Directory.
* L’authentification Azure AD utilise les utilisateurs de base de données à relation contenant-contenu pour authentifier les identités au niveau de la base de données.
* Azure AD prend en charge l’authentification basée sur les jetons pour les applications se connectant à SQL Database.
* L’authentification Azure AD prend en charge ADFS (fédération de domaine) ou l’authentification utilisateur natif/mot de passe pour un répertoire Azure Active Directory local sans synchronisation du domaine.  
* Azure AD prend en charge les connexions à partir de SQL Server Management Studio qui utilisent l’authentification universelle Active Directory, et notamment Multi-Factor Authentication (MFA).  MFA comprend une authentification forte avec une gamme d’options de vérification simples (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d’application mobile). Pour plus d’informations, voir [Prise en charge de SSMS pour Azure AD MFA avec la base de données SQL et SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).  

>  [!NOTE]  
>  La connexion à SQL Server s’exécutant sur une machine virtuelle Azure n’est pas prise en charge à l’aide d’un compte Azure Active Directory. Utilisez plutôt un compte Active Directory du domaine.  

Les étapes de configuration incluent les procédures suivantes pour configurer et utiliser l’authentification Azure Active Directory.

1. Créer et renseigner Azure AD.
2. Facultatif : associer ou modifier le répertoire actif actuellement associé à votre abonnement Azure.
3. Créez un administrateur Azure Active Directory pour le serveur Azure SQL Database, Managed Instance ou [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
4. Configurer vos ordinateurs clients.
5. Créer des utilisateurs de base de données à relation contenant-contenu dans votre base de données mappés sur les identités Azure AD.
6. Se connecter à la base de données à l’aide des identités Azure AD.

> [!NOTE]
> Pour apprendre à créer et à remplir Azure AD, puis à configurer Azure AD avec Azure SQL Database, Managed Instance et SQL Data Warehouse, consultez [Configurer Azure AD avec Azure SQL Database](sql-database-aad-authentication-configure.md).
>

## <a name="trust-architecture"></a>Architecture d’approbation
Le diagramme de niveau élevé suivant résume l’architecture de solution de l’utilisation de l’authentification Azure AD avec la base de données SQL Azure. Les mêmes concepts s’appliquent à SQL Data Warehouse. Pour prendre en charge le mot de passe des utilisateurs natifs d’Azure AD, seuls la partie cloud et Azure AD/Azure SQL Database sont considérés. Pour prendre en charge l’authentification fédérée (ou utilisateur/mot de passe pour les informations d’identification Windows), la communication avec le bloc ADFS est requise. Les flèches indiquent les voies de communication.

![diagramme autorisation aad][1]

Le diagramme suivant indique la fédération, l’approbation et les relations d’hébergement qui autorisent un client à se connecter à une base de données en soumettant un jeton. Le jeton est authentifié par une instance Azure AD, et approuvé par la base de données. Le client 1 peut représenter un répertoire Azure Active Directory avec des utilisateurs natifs ou un répertoire Azure AD avec des utilisateurs fédérés. Le client 2 représente une solution possible incluant des utilisateurs importés, qui dans cet exemple proviennent d’un répertoire Azure Active Directory fédéré avec la synchronisation d’ADFS avec Azure Active Directory. Il est important de comprendre que l’accès à une base de données à l’aide de l’authentification Azure AD exige que l’abonnement d’hébergement soit associé à Azure AD. Le même abonnement doit être utilisé pour créer le serveur SQL Server hébergeant la base de données SQL Azure ou SQL Data Warehouse.

![relation abonnement][2]

## <a name="administrator-structure"></a>Structure de l’administrateur
En cas d’utilisation de l’authentification Azure AD, il existe deux comptes administrateur pour le serveur SQL Database ; l’administrateur de SQL Server d’origine et l’administrateur Azure AD. Les mêmes concepts s’appliquent à SQL Data Warehouse. Seul l’administrateur basé sur un compte Azure AD peut créer le premier utilisateur de la base de données Azure AD à relation contenant-contenu dans une base de données utilisateur. La connexion d’administrateur Azure AD peut être un utilisateur Azure AD ou un groupe Azure AD. Lorsque l’administrateur est un compte de groupe, il peut être utilisé par n’importe quel membre du groupe, ce qui permet à plusieurs administrateurs Azure AD pour l’instance de SQL Server. L’utilisation d’un compte de groupe en tant qu’administrateur facilite la gestion en vous permettant d’ajouter et de supprimer des membres du groupe dans Azure AD sans modifier les utilisateurs ou les autorisations de base de données SQL. Seul un administrateur Azure AD (utilisateur ou groupe) peut être configuré à tout moment.

![structure admin][3]

## <a name="permissions"></a>Autorisations
Pour créer de nouveaux utilisateurs, vous devez disposer de l’autorisation `ALTER ANY USER` dans la base de données. L’autorisation `ALTER ANY USER` peut être octroyée à un utilisateur de base de données. L’autorisation `ALTER ANY USER` est également détenue par les comptes d’administrateur de serveur et les utilisateurs de base de données avec les autorisations `CONTROL ON DATABASE` ou `ALTER ON DATABASE` pour cette base de données et par les membres du rôle de base de données `db_owner`.

Pour créer un utilisateur de base de données à relation contenant-contenu dans le service Azure SQL Database, Managed Instance ou SQL Data Warehouse, vous devez vous connecter à la base de données à l’aide d’une identité Azure AD. Pour créer le premier utilisateur de la base de données à relation contenant-contenu, vous devez vous connecter à la base de données à l’aide d’un administrateur Azure AD (le propriétaire de la base de données). Cette procédure est expliquée dans [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](sql-database-aad-authentication-configure.md). L’authentification Azure AD n’est possible que si l’administrateur Azure AD a été créé pour le serveur Azure SQL Database ou SQL Data Warehouse. Si l’administrateur Azure Active Directory a été supprimé du serveur, les utilisateurs Azure Active Directory existants créés précédemment dans le serveur SQL Server ne peuvent plus se connecter à la base de données à l’aide de leurs informations d’identification Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Limitations et fonctionnalités azure AD
Les membres suivants d’Azure AD peuvent être configurés dans le serveur Azure SQL Server ou dans SQL Data Warehouse :

- Membre natif : un membre créé dans le domaine géré ou le domaine client de Microsoft Azure AD. Pour plus d’informations, consultez [Ajout de votre nom de domaine personnalisé à Azure AD](../active-directory/active-directory-domains-add-azure-portal.md).
- Membre de domaine fédéré : un membre créé dans Azure AD avec un domaine fédéré. Pour plus d’informations, consultez [Microsoft Azure prend désormais en charge la fédération avec Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).
- Membres importés à partir d’autres répertoires Azure AD qui sont des membres natifs ou de domaine fédéré.
- Groupes Active Directory créés en tant que groupes de sécurité.

Limitations d’Azure AD pour Managed Instance :
- Seul l’administrateur Azure AD peut créer des bases de données, les utilisateurs Azure AD sont limités à une base de données unique et n’ont pas cette autorisation
- Propriété de base de données :
  - Le principal Azure AD ne peut pas modifier la propriété de la base de données (ALTER AUTHORIZATION ON DATABASE) et ne peut pas être défini comme propriétaire.
  - Pour les bases de données créées par l’administrateur Azure AD, aucune propriété n’est définie (le champ owner_sid dans sys.sysdatabases est 0x1)
- L’Agent SQL ne peut pas être géré lors de la connexion à l’aide des principaux Azure AD. 
- L’administrateur Azure AD ne peut pas être représenté à l’aide de EXECUTE AS
- La connexion DAC n’est pas prise en charge avec les principaux Azure AD. 

Ces fonctions système retournent des valeurs NULL lors de leur exécution sous des entités Azure AD :
- `SUSER_ID()`
- `SUSER_NAME(<admin ID>)`
- `SUSER_SNAME(<admin SID>)`
- `SUSER_ID(<admin name>)`
- `SUSER_SID(<admin name>)`

## <a name="connecting-using-azure-ad-identities"></a>Connexion à l’aide des identités Azure AD

L’authentification Azure Active Directory prend en charge les méthodes suivantes de connexion à une base de données à l’aide d’identités Azure AD :

* À l’aide de l’authentification Windows.
* À l’aide d’un nom principal et d’un mot de passe Azure AD
* À l’aide de l’authentification par jeton d’application

### <a name="additional-considerations"></a>Considérations supplémentaires

* Pour améliorer la facilité de gestion, nous vous conseillons de mettre en service un groupe Azure AD dédié en tant qu’administrateur.   
* Un seul utilisateur administrateur Azure AD (utilisateur ou groupe) peut être configuré pour un serveur Azure SQL Database, Managed Instance ou Azure SQL Data Warehouse à tout moment.   
* Seul un administrateur d’Azure AD pour SQL Server peut se connecter initialement au serveur Azure SQL Database, à Managed Instance, ou à Azure SQL Data Warehouse à l’aide d’un compte Azure Active Directory. L’administrateur Active Directory peut configurer les utilisateurs de base de données Azure AD suivants.   
* Nous vous conseillons de définir l’expiration du délai de connexion à 30 secondes.   
* SQL Server 2016 Management Studio et SQL Server Data Tools pour Visual Studio 2015 (version 14.0.60311.1 d’avril 2016 ou ultérieure) prennent en charge l’authentification Azure Active Directory. (L’authentification Azure AD est prise en charge par le **Fournisseur de données .NET Framework pour SQL Server** ; .NET Framework version 4.6 minimum). Par conséquent, les dernières versions de ces outils et applications de la couche Données (DAC et .bacpac) peuvent utiliser l’authentification Azure AD.   
* [ODBC version 13.1`bcp.exe` prend en charge l’authentification Azure Active Directory. Toutefois ](https://www.microsoft.com/download/details.aspx?id=53339) ne peut pas se connecter avec l’authentification Azure Active Directory car il utilise un fournisseur ODBC plus ancien.   
* `sqlcmd` prend en charge l’authentification Azure Active Directory depuis la version 13.1 disponible dans le [Centre de téléchargement](http://go.microsoft.com/fwlink/?LinkID=825643).   
* SQL Server Data Tools pour Visual Studio 2015 requiert la version d’avril 2016 (version 14.0.60311.1) ou une version ultérieure. Actuellement, les utilisateurs Azure AD ne sont pas affichés dans l’Explorateur d’objets SSDT. Comme solution de contournement, vous pouvez afficher les utilisateurs dans [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).   
* Le [pilote Microsoft JDBC 6.0 pour SQL Server](https://www.microsoft.com/download/details.aspx?id=11774) prend en charge l’authentification Azure AD. Consultez également [Définition des propriétés de connexion](https://msdn.microsoft.com/library/ms378988.aspx).   
* PolyBase ne peut pas s’authentifier avec l’authentification Azure AD.   
* L’authentification Azure AD est prise en charge pour SQL Database dans les panneaux **Importer la base de données** et **Exporter la base de données** du portail Azure. L’importation et l’exportation à l’aide de l’authentification Azure AD sont également prises en charge depuis l’invite de commandes PowerShell.   
* L’authentification Azure AD est prise en charge pour SQL Database, Managed Instance et SQL Data Warehouse via l’interface de ligne de commande (CLI). Pour plus d’informations, consultez [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](sql-database-aad-authentication-configure.md) et [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="next-steps"></a>Étapes suivantes
- Pour apprendre à créer et à remplir Azure AD, puis à configurer Azure AD avec Azure SQL Database ou Azure SQL Data Warehouse, consultez [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](sql-database-aad-authentication-configure.md).
- Pour obtenir une vue d’ensemble de l’accès et du contrôle dans la base de données SQL, voir [Accès à la base de données SQL et contrôle](sql-database-control-access.md).
- Pour une vue d’ensemble des connexions, des utilisateurs et des rôles de base de données dans la base de données SQL, voir [Connexions, utilisateurs et rôles de base de données](sql-database-manage-logins.md).
- Pour en savoir plus sur les principaux de base de données, voir [Principaux](https://msdn.microsoft.com/library/ms181127.aspx).
- Pour en savoir plus sur les rôles de base de données, voir [Rôles de base de données](https://msdn.microsoft.com/library/ms189121.aspx).
- Pour en savoir plus sur les règles de pare-feu dans la base de données SQL, voir [Règles de pare-feu de la base de données SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

