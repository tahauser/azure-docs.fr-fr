---
title: Multi-Factor Authentication - Azure SQL | Microsoft Docs
description: Azure SQL Database et Azure SQL Data Warehouse prennent en charge les connexions depuis SQL Server Management Studio (SSMS) à l’aide de l’authentification universelle Active Directory.
services: sql-database
documentationcenter: ''
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 09/29/2017
ms.author: mireks
ms.openlocfilehash: 3a950ac837b543cbcbfd4ae43cf4b9ebeb537cd5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA)
Azure SQL Database et Azure SQL Data Warehouse prennent en charge les connexions à partir de SQL Server Management Studio (SSMS) à l’aide de *l’authentification universelle Active Directory*. 
**Télécharger la dernière version de SSMS** : sur l’ordinateur client, téléchargez la dernière version de SSMS à partir de la page [Télécharger SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Pour toutes les fonctionnalités abordées dans cette rubrique, utilisez au minimum la version 17.2 (juillet 2017).  La boîte de dialogue de connexion la plus récente ressemble à ceci : ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Renseigner la zone Nom d’utilisateur.")  

## <a name="the-five-authentication-options"></a>Les cinq options d’authentification  
- Authentification universelle Active Directory prend en charge les deux méthodes d’authentification non interactives (authentification `Active Directory - Password` et authentification `Active Directory - Integrated`). Les méthodes d’authentification `Active Directory - Password` et `Active Directory - Integrated` non interactives peuvent être utilisées dans de nombreuses applications (ADO.NET, JDBC, ODBC, etc.). Ces deux méthodes n’entraînent jamais l’affichage de boîtes de dialogue contextuelles.

- L’authentification `Active Directory - Universal with MFA` est une méthode interactive prenant également en charge *Azure Multi-Factor Authentication* (MFA). Azure MFA contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Il assure une authentification forte avec une gamme d’options de vérification simples (appel téléphonique, SMS, cartes à puce avec code PIN ou notification d’application mobile) pour permettre aux utilisateurs de choisir leur méthode préférée. L’authentification multifacteur (MFA) interactive avec Azure AD peut afficher une boîte de dialogue contextuelle de validation.

Pour une description de Multi-Factor Authentication, consultez la rubrique [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md).
Pour les étapes de configuration, consultez [Configuration de l’authentification multifacteur aux bases de données Azure SQL pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Nom de domaine Azure AD et paramètre d’ID de locataire   

À partir de [la version 17 de SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), les utilisateurs qui sont importés dans le répertoire Active Directory en cours à partir d’autres répertoires Azure Active Directory en tant qu’utilisateurs invités peuvent fournir le nom de domaine Azure AD ou l’ID de locataire quand ils se connectent. Les utilisateurs invités incluent les utilisateurs qui sont invités à partir d’autres annuaires Azure AD, de comptes Microsoft, tels qu’outlook.com, hotmail.com et live.com, ou d’autres comptes, comme gmail.com. Grâce à ces informations, **l’authentification universelle Active Directory avec MFA** peut identifier l’autorité d’authentification appropriée. Cette option est également requise pour prendre en charge les comptes Microsoft (MSA) tels qu’outlook.com, hotmail.com, live.com, ou les comptes non-MSA. Les utilisateurs qui souhaitent être authentifiés à l’aide de l’authentification universelle doivent entrer leur nom de domaine Azure AD ou leur ID de locataire. Ce paramètre représente le nom de domaine/ID de locataire Azure AD lié au serveur Azure. Par exemple, si le serveur Azure est associé au domaine Azure AD `contosotest.onmicrosoft.com` où l’utilisateur `joe@contosodev.onmicrosoft.com` est hébergé en tant qu’utilisateur importé du domaine Azure AD `contosodev.onmicrosoft.com`, le nom de domaine requis pour authentifier cet utilisateur est `contosotest.onmicrosoft.com`. Lorsque l’utilisateur est un utilisateur natif d’Azure AD lié au serveur Azure et qu’il n’a pas de compte MSA, aucun nom de domaine ou ID de locataire n’est requis. Pour entrer le paramètre (à compter de SSMS version 17.2), dans la boîte de dialogue **Connexion à une base de données**, sélectionnez **Active Directory - Authentification universelle avec MFA**, cliquez sur **Options**, renseignez la zone **Nom d’utilisateur**, puis cliquez sur l’onglet **Propriétés de connexion**. Cochez la case **Nom du domaine AD ou ID de locataire** et fournissez l’autorité d’authentification, telle que le nom de domaine (**contosotest.onmicrosoft.com**) ou le GUID de l’ID de locataire.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Prise en charge d’Azure AD B2B   
Les utilisateurs Azure AD pris en charge pour les scénarios d’Azure AD B2B en tant qu’utilisateurs invités (consulter [Qu’est-ce qu’Azure AD B2B Collaboration ?](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)) ne peuvent se connecter à SQL Database et SQL Data Warehouse qu’en tant que membres d’un groupe créé dans Azure AD et mappé manuellement à l’aide de l’instruction Transact-SQL `CREATE USER` dans une base de données spécifique. Par exemple, si `steve@gmail.com` est invité à Azure AD `contosotest` (avec le domaine Azure AD `contosotest.onmicrosoft.com`), un groupe Azure AD, tel que `usergroup`, doit être créé dans l’annuaire Azure AD qui contient le membre `steve@gmail.com`. Ensuite, l’administrateur Azure AD SQL ou le propriétaire de la base de données Azure AD doit créer ce groupe pour une base de données spécifique (MyDatabase) en exécutant une instruction Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER`. Une fois l’utilisateur de base de données créé, l’utilisateur `steve@gmail.com` peut se connecter à `MyDatabase` à l’aide de l’option d’authentification SSMS `Active Directory – Universal with MFA support`. Par défaut, le groupe d’utilisateurs bénéficie uniquement de l’autorisation de connexion et de tout accès aux données accordé de façon habituelle. Notez que l’utilisateur `steve@gmail.com` en tant qu’utilisateur invité doit cocher la case et ajouter le nom de domaine AD `contosotest.onmicrosoft.com` à la boîte de dialogue **Propriété de connexion** SSMS. L’option **Nom du domaine AD ou ID de locataire** est uniquement prise en charge pour les options Authentification universelle avec prise en charge de MFA ; dans le cas contraire, elle est grisée.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limites de l’authentification universelle pour la base de données SQL et SQL Data Warehouse
- SSMS et SqlPackage.exe sont les seuls outils activés pour MFA par le biais de l’authentification universelle Active Directory.
- SSMS version 17.2 prend en charge l’accès simultané de plusieurs utilisateurs à l’aide de l’authentification universelle avec MFA. Les versions 17.0 et 17.1 limitaient à un seul compte Azure Active Directory la connexion à une instance de SSMS à l’aide de l’authentification universelle. Pour vous connecter comme un autre compte Azure AD, vous devez utiliser une autre instance de SSMS. (Cette restriction est limitée à l’authentification universelle Active Directory. Vous pouvez vous connecter à différents serveurs à l’aide de l’authentification de mot de passe Active Directory, l’authentification intégrée à Active Directory ou l’authentification SQL Server).
- SSMS prend en charge l’authentification universelle Active Directory pour la visualisation de l’Explorateur d’objets, de l’Éditeur de requête et du magasin de requêtes.
- SSMS version 17.2 fournit une prise en charge de l’Assistant DacFx pour l’exportation, l’extraction et le déploiement de la base de données. Une fois qu’un utilisateur spécifique est authentifié via la boîte de dialogue d’authentification initiale à l’aide de l’authentification universelle, l’Assistant DacFx fonctionne de la même manière que pour toutes les autres méthodes d’authentification.
- Le Concepteur de tables SSMS ne prend pas en charge l’authentification universelle.
- Il n’existe aucune configuration logicielle supplémentaire nécessaire pour l’authentification universelle Active Directory, sauf que vous devez utiliser une version prise en charge de SSMS.  
- La version ADAL (Active Directory Authentication Library) pour l’authentification universelle a été mise à jour vers la dernière version publiée disponible ADAL.dll 3.13.9. Consultez [Active Directory Authentication Library 3.14.1](http://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Étapes suivantes

- Pour les étapes de configuration, consultez [Configuration de l’authentification multifacteur aux bases de données Azure SQL pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Octroyer à d’autres utilisateurs l’accès à votre base de données : [Authentification et autorisation de base de données SQL : octroi de l’accès](sql-database-manage-logins.md)  
- Vérifiez que les autres utilisateurs peuvent se connecter par le biais du pare-feu : [Configurer une règle de pare-feu au niveau du serveur sur Azure SQL Database à l’aide du portail Azure](sql-database-configure-firewall-settings.md)  
- [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx)  
- [Importer un fichier BACPAC dans une nouvelle base de données SQL Azure](../sql-database/sql-database-import.md)  
- [Exporter une base de données SQL Azure dans un fichier BACPAC](../sql-database/sql-database-export.md)  
- Interface C# [Interface IUniversalAuthProvider](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Quand vous utilisez l’authentification **Active Directory - Authentification universelle avec prise en charge de MFA**, le suivi ADAL est disponible à compter de [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Le suivi ADAL étant désactivé par défaut, vous pouvez l’activer comme suit : dans le menu **Outils**, choisissez **Options**, puis sous **Services Azure**, choisissez **Cloud Azure** et **Niveau de trace dans la fenêtre Sortie ADAL**, et activez **Sortie** dans le menu **Affichage**. Les suivis sont disponibles dans la fenêtre de sortie quand vous sélectionnez l’option **Azure Active Directory**.  
