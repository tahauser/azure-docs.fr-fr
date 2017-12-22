---
title: "Authentification sur Azure SQL Data Warehouse | Microsoft Docs"
description: Authentification Azure Active Directory (AAD) et SQL Server sur Azure SQL Data Warehouse.
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
tags: 
ms.assetid: fefaaa75-2d0c-4e5d-aadb-410342d1ad73
ms.service: sql-data-warehouse
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.custom: security
ms.date: 03/21/2017
ms.author: rortloff;barbkess
ms.openlocfilehash: 92f48027051bc4aff4d6b8d66fdd6de81bba3657
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="authentication-to-azure-sql-data-warehouse"></a>Authentification sur Azure SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Présentation de la sécurité](sql-data-warehouse-overview-manage-security.md)
> * [Authentification](sql-data-warehouse-authentication.md)
> * [Chiffrement (portail)](sql-data-warehouse-encryption-tde.md)
> * [Chiffrement (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Pour vous connecter à SQL Data Warehouse, vous devez transmettre des informations d’identification de sécurité à des fins d’authentification. Lors de l’établissement d’une connexion, certains paramètres de connexion sont configurés dans le cadre de l’établissement de votre session de requête.  

Vous pouvez consulter l’article [Sécuriser une base de données dans SQL Data Warehouse][Secure a database in SQL Data Warehouse] pour plus d’informations sur la sécurité et la manière d’activer des connexions à votre entrepôt de données.

## <a name="sql-authentication"></a>Authentification SQL
Pour vous connecter à SQL Data Warehouse, vous devez fournir les informations suivantes :

* Nom complet du serveur
* Authentification SQL
* Nom d’utilisateur
* Mot de passe
* Base de données par défaut (facultatif)

Par défaut, votre connexion se connecte à la base de données *MASTER* et non à votre base de données utilisateur. Pour vous connecter à votre base de données utilisateur, vous pouvez choisir d’effectuer l’une des deux opérations suivantes :

* Spécifiez la base de données par défaut lors de l’inscription de votre serveur auprès de l’Explorateur d’objets SQL Server dans SQL Server Data Tools (SSDT), SSMS, ou dans votre chaîne de connexion d’application. Par exemple, insérez le paramètre InitialCatalog pour une connexion ODBC.
* Sélectionnez la base de données utilisateur avant de créer une session dans SSDT.

> [!NOTE]
> L’instruction Transact-SQL **USE MyDatabase;** n’est pas prise en charge pour la modification de la base de données pour une connexion. Pour obtenir des conseils en matière de connexion à SQL Data Warehouse avec SSDT, consultez l’article [Envoyer des requêtes avec Visual Studio][Query with Visual Studio].
> 
> 

## <a name="azure-active-directory-aad-authentication"></a>Authentification Azure Active Directory (AAD)
L’[authentification Azure Active Directory][What is Azure Active Directory] est un mécanisme servant à se connecter à Microsoft Azure SQL Data Warehouse à l’aide d’identités dans Azure Active Directory (Azure AD). Avec l’authentification Azure Active Directory, vous pouvez gérer de manière centralisée les identités des utilisateurs de base de données et d’autres services Microsoft dans un emplacement centralisé. La gestion centralisée des ID fournit un emplacement unique pour gérer les utilisateurs SQL Data Warehouse et elle simplifie la gestion des autorisations. 

### <a name="benefits"></a>Avantages
Les avantages d’Azure Active Directory incluent :

* Fournit une alternative à l’authentification SQL Server.
* Permet de bloquer la prolifération des identités utilisateur sur plusieurs serveurs de base de données.
* Permet la rotation de mot de passe à un emplacement unique
* Gérer les autorisations de base de données à l’aide de groupes (AAD) externes.
* Élimine le stockage des mots de passe en activant l’authentification intégrée Windows et les autres formes d’authentification prises en charge par Azure Active Directory.
* Utilise les utilisateurs de base de données à relation contenant-contenu pour authentifier les identités au niveau de la base de données.
* Prend en charge l’authentification basée sur les jetons pour les applications se connectant à SQL Data Warehouse.
* Prend en charge Multi-Factor Authentication avec l’authentification universelle Active Directory pour SQL Server Management Studio. Pour obtenir une description de Multi-Factor Authentication, consultez [Prise en charge SSMS de Azure AD MFA avec la base de données SQL et SQL Data Warehouse](../sql-database/sql-database-ssms-mfa-authentication.md).

> [!NOTE]
> Azure Active Directory est encore relativement nouveau et présente certaines limitations. Pour vérifier qu’Azure Active Directory est adapté à votre environnement, consultez [Limitations et fonctionnalités Azure AD][Azure AD features and limitations], en particulier la section Considérations supplémentaires.
> 
> 

### <a name="configuration-steps"></a>Configuration
Suivez ces étapes pour configurer l’authentification Azure Active Directory.

1. Créer et renseigner un répertoire Azure Active Directory
2. Facultatif : associer ou modifier le répertoire actif actuellement associé à votre abonnement Azure
3. Créer un administrateur Azure Active Directory pour Azure SQL Data Warehouse
4. Configurer vos ordinateurs clients
5. Créer des utilisateurs de base de données à relation contenant-contenu dans votre base de données mappés sur les identités Azure AD
6. Se connecter à l’entrepôt de données à l’aide des identités Azure AD

Actuellement, les utilisateurs Azure Active Directory ne sont pas affichés dans l’Explorateur d’objets SSDT. Comme solution de contournement, vous pouvez afficher les utilisateurs dans [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).

### <a name="find-the-details"></a>Rechercher des informations détaillées
* Les étapes de configuration et d’utilisation de l’authentification Azure Active Directory sont presque identiques pour Azure SQL Database et Azure SQL Data Warehouse. Suivez les étapes détaillées dans la rubrique [Connexion à SQL Database ou SQL Data Warehouse avec l’authentification Azure Active Directory](../sql-database/sql-database-aad-authentication.md).
* Créez des rôles de base de données personnalisés et ajoutez des utilisateurs aux rôles. Accordez ensuite des autorisations granulaires aux rôles. Pour plus d’informations, consultez [Prise en main des autorisations du moteur de base de données](https://msdn.microsoft.com/library/mt667986.aspx).

## <a name="next-steps"></a>Étapes suivantes
Pour commencer à interroger votre entrepôt de données avec Visual Studio et d’autres applications, consultez [Soumettre des requêtes avec Visual Studio][Query with Visual Studio].

<!-- Article references -->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[What is Azure Active Directory]: ../active-directory/active-directory-whatis.md
[Azure AD features and limitations]: ../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations
