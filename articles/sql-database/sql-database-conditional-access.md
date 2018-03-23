---
title: Accès conditionnel - Azure SQL Database et Data Warehouse | Microsoft Docs
description: Découvrez comment configurer l’accès conditionnel pour Azure SQL Database et Data Warehouse.
services: sql-database
author: GithubMirek
manager: johammer
ms.custom: security
ms.service: sql-database
ms.topic: article
ms.date: 06/07/2017
ms.author: mireks
ms.openlocfilehash: f32928805275c9d797c8cb15bb731fe172d5903f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Accès conditionnel (MFA) avec Azure SQL Database et Data Warehouse  

SQL Database et SQL Data Warehouse prennent tous deux en charge l’accès conditionnel Microsoft. Les étapes suivantes montrent comment configurer SQL Database pour appliquer une stratégie d’accès conditionnel.  

## <a name="prerequisites"></a>Prérequis
  
- Vous devez configurer SQL Database ou SQL Data Warehouse pour prendre en charge l’authentification Azure Active Directory. Pour connaître la procédure spécifique, consultez [Configurer et gérer l’authentification Azure Active Directory avec SQL Database ou SQL Data Warehouse](sql-database-aad-authentication-configure.md).  
- Quand Multi-Factor Authentication est activé, vous devez vous connecter avec un outil pris en charge, tel que la dernière version de SSMS. Pour plus d’informations, consultez [Configurer Azure SQL Database Multi-Factor Authentication pour SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Configurer l’accès conditionnel pour Azure SQL DB/DW  
1.  Connectez-vous au portail, sélectionnez **Azure Active Directory**, puis **Accès conditionnel**. Pour plus d’informations, consultez [Référence technique de l’accès conditionnel Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference).  
  ![panneau d’accès conditionnel](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  Dans le panneau des **stratégies d’accès conditionnel**, cliquez sur **Nouvelle stratégie**, fournissez un nom, puis cliquez sur **Configure rules** (Configurer les règles).  
3.  Sous **Affectations**, sélectionnez **Utilisateurs et groupes**, cochez **Sélectionner des utilisateurs et des groupes**, puis sélectionnez l’utilisateur ou le groupe pour l’accès conditionnel. Cliquez sur **Sélectionner**, puis sur **Terminé** pour valider la sélection.  
  ![sélectionner des utilisateurs et des groupes](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  Sélectionnez **Applications cloud**, puis cliquez sur **Sélectionner des applications**. Vous voyez toutes les applications disponibles pour l’accès conditionnel. Sélectionnez **Azure SQL Database**, cliquez en bas sur **Sélectionner**, puis sur **Terminé**.  
  ![sélectionner SQL Database](./media/sql-database-conditional-access/select-sql-database.png)  
  Si vous ne trouvez pas **Azure SQL Database** répertorié dans la troisième capture d’écran ci-dessous, effectuez les étapes suivantes :   
  - Connectez-vous à votre instance Azure SQL DB/DW à l’aide de SSMS avec un compte d’administrateur AAD.  
  - Exécutez `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER`.  
  - Connectez-vous à AAD et vérifiez qu’Azure SQL Database et Data Warehouse sont répertoriés dans les applications d’AAD.  

5.  Sélectionnez **Contrôles d’accès**, **Accorder**, puis cochez la stratégie que vous souhaitez appliquer. Pour cet exemple, nous sélectionnons **Imposer l’authentification multifacteur**.  
  ![sélectionner Accorder l’accès](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>Résumé  
L’application sélectionnée (Azure SQL Database), qui permet de se connecter à Azure SQL DB/DW à l’aide d’Azure AD Premium, applique maintenant la stratégie d’accès conditionnel sélectionnée **Imposer l’authentification multifacteur**.  
Pour toute question sur Azure SQL Database et Data Warehouse en ce qui concerne Multi-Factor Authentication, contactez MFAforSQLDB@microsoft.com.  

## <a name="next-steps"></a>Étapes suivantes  

Pour obtenir un didacticiel, consultez [Sécuriser votre base de données Azure SQL Database](sql-database-security-tutorial.md).
