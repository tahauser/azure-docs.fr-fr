---
title: Configurer Multi-Factor Authentication - Azure SQL | Microsoft Docs
description: Découvrez l’utilisation de Multi-Factor Authentication avec SSMS pour SQL Database et SQL Data Warehouse.
services: sql-database
author: GithubMirek
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 09/27/2017
ms.author: mireks
ms.openlocfilehash: 7b74cd6b62686fd03d9f42316701f44daf99eed8
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Configurer l’authentification multifacteur pour SQL Server Management Studio et Azure AD

Cette rubrique montre comment utiliser l’authentification multifacteur Azure Active Directory (MFA) avec SQL Server Management Studio. Azure AD MFA peut être utilisé au moment de la connexion de SSMS ou SqlPackage.exe à Azure SQL Database et Azure SQL Data Warehouse.

Pour une vue d’ensemble de l’authentification multifacteur Azure SQL Database, consultez [Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA)](sql-database-ssms-mfa-authentication.md).

## <a name="configuration-steps"></a>Configuration

1. **Configurer un annuaire Azure Active Directory** : pour plus d’informations, consultez [Administration de votre annuaire Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx), [Intégration de vos identités locales à Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Ajout de votre nom de domaine personnalisé à Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure prend désormais en charge la fédération avec Windows Server Active Directory) et [Gestion d’Azure AD à l’aide de Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).
2. **Configurer l’authentification MFA** : pour obtenir des instructions pas à pas, consultez [Présentation d’Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) et [Accès conditionnel (MFA) avec Azure SQL Database et Data Warehouse](sql-database-conditional-access.md). (L’accès conditionnel complet requiert un annuaire Azure Active Directory (Azure AD) Premium. L’authentification MFA limitée est disponible avec un annuaire Azure AD standard.)
3. **Configurer SQL Database ou SQL Data Warehouse pour l’authentification Azure AD** : pour obtenir des instructions pas à pas, consultez [Connexion à SQL Database ou SQL Data Warehouse avec l’authentification Azure Active Directory](sql-database-aad-authentication.md).
4. **Télécharger SSMS** : sur l’ordinateur client, téléchargez la dernière version de SSMS à partir de la page [Télécharger SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Pour toutes les fonctionnalités abordées dans cette rubrique, utilisez au minimum la version 17.2 (juillet 2017).  

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Connexion à l’aide de l’authentification universelle avec SSMS

Les étapes suivantes montrent comment se connecter à la base de données SQL ou à SQL Data Warehouse à l’aide de la version la plus récente de SSMS.

1. Pour vous connecter à l’aide de l’authentification universelle, accédez à la boîte de dialogue **Se connecter au serveur**, puis sélectionnez **Active Directory - Authentification universelle avec MFA**. (Si vous voyez **Authentification universelle Active Directory**, vous n’êtes pas en train d’utiliser la dernière version de SSMS.)  
   ![1mfa-universal-connect][1]  
2. Renseignez la zone **Nom d’utilisateur** avec les informations d’identification Azure Active Directory, selon le format `user_name@domain.com`.  
   ![1mfa-universal-connect-user](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect-user.png)   
3. Si vous vous connectez en tant qu’utilisateur invité, vous devez cliquer sur **Options**, puis, dans la boîte de dialogue **Propriété de connexion**, renseigner la zone **Nom du domaine AD ou ID de locataire**. Pour plus d’informations, consultez [Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA)](sql-database-ssms-mfa-authentication.md).
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   
4. Comme d’habitude pour SQL Database et SQL Data Warehouse, vous devez cliquer sur **Options** et spécifier la base de données dans la boîte de dialogue **Options**. Si l’utilisateur connecté est un utilisateur invité (c’est-à-dire joe@outlook.com), vous devez cocher la case et ajouter le nom de domaine AD ou l’ID de locataire actuel parmi les options. Consultez [Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA)]()(sql-database-ssms-mfa-authentication.md). Puis, cliquez sur **Se connecter**.  
5. Lorsque la boîte de dialogue **Connectez-vous à votre compte** s’affiche, spécifiez le compte et le mot de passe de votre identité Azure Active Directory. Aucun mot de passe n’est requis si un utilisateur fait partie d’un domaine fédéré avec Azure AD.  
   ![2mfa-sign-in][2]  

   > [!NOTE]
   > Pour l’authentification universelle avec un compte qui ne nécessite pas MFA, vous vous connectez à ce stade. Pour les utilisateurs nécessitant MFA, passez aux étapes suivantes :
   >  
   
6. Deux boîtes de dialogue de configuration de MFA peuvent s’afficher. Cette opération à exécuter une seule fois dépend de l’administrateur du paramètre de l’administrateur MFA et peut être par conséquent facultative. Pour un domaine avec MFA activée, cette étape est parfois prédéfinie (par exemple, le domaine exige que les utilisateurs utilisent une carte à puce et un code confidentiel).  
   ![3mfa-setup][3]  
7. La deuxième boîte de dialogue unique possible vous permet de sélectionner les détails de votre méthode d’authentification. Les options possibles sont configurées par votre administrateur.  
   ![4mfa-verify-1][4]  
8. Azure Active Directory vous envoie les informations de confirmation. Lorsque vous recevez le code de vérification, entrez-le dans la zone **Entrez le code de vérification**, puis cliquez sur **Connexion**.  
   ![5mfa-verify-2][5]  

Lorsque la vérification est terminée, SSMS se connecte normalement à condition que les informations d’identification soient valides et qu’un accès au pare-feu soit possible.

## <a name="next-steps"></a>Étapes suivantes

- Pour une vue d’ensemble de l’authentification multifacteur Azure SQL Database, consultez [Authentification universelle avec SQL Database et SQL Data Warehouse (prise en charge de SSMS pour MFA)](sql-database-ssms-mfa-authentication.md).  
- Octroyer à d’autres utilisateurs l’accès à votre base de données : [Authentification et autorisation de base de données SQL : octroi de l’accès](sql-database-manage-logins.md)  
- Vérifiez que les autres utilisateurs peuvent se connecter par le biais du pare-feu : [Configurer une règle de pare-feu au niveau du serveur sur Azure SQL Database à l’aide du portail Azure](sql-database-configure-firewall-settings.md)  
- Quand vous utilisez l’authentification **Active Directory - Authentification universelle avec prise en charge de MFA**, le suivi ADAL est disponible à compter de [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Le suivi ADAL étant désactivé par défaut, vous pouvez l’activer comme suit : dans le menu **Outils**, choisissez **Options**, puis sous **Services Azure**, choisissez **Cloud Azure** et **Niveau de trace dans la fenêtre Sortie ADAL**, et activez **Sortie** dans le menu **Affichage**. Les suivis sont disponibles dans la fenêtre de sortie quand vous sélectionnez l’option **Azure Active Directory**.   


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

