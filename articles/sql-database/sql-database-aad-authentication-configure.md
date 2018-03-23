---
title: Configurer l’authentification Azure Active Directory - SQL | Microsoft Docs
description: Découvrez comment vous connecter à SQL Database, à Managed Instance et à SQL Data Warehouse avec l’authentification Azure Active Directory, après avoir configuré Azure AD.
services: sql-database
author: GithubMirek
manager: johammer
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/07/2018
ms.author: mireks
ms.openlocfilehash: 00b5be9863e2bff9e5b82845f99d6829e1bcdf13
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql-database-managed-instance-or-sql-data-warehouse"></a>Configurer et gérer l’authentification Azure Active Directory avec SQL Database, Managed Instance ou SQL Data Warehouse

Cet article vous montre comment créer et remplir Azure AD, puis comment utiliser Azure AD avec Azure SQL Database et SQL Data Warehouse. Pour obtenir une vue d’ensemble, consultez [Authentification Azure Active Directory](sql-database-aad-authentication.md).

>  [!NOTE]  
>  La connexion à SQL Server s’exécutant sur une machine virtuelle Azure n’est pas prise en charge à l’aide d’un compte Azure Active Directory. Utilisez plutôt un compte Active Directory du domaine.

## <a name="create-and-populate-an-azure-ad"></a>Créer et renseigner un répertoire Azure AD
Créez un annuaire Azure AD et renseignez-le avec les utilisateurs et les groupes. Azure AD peut être le domaine managé Azure AD initial. Azure AD peut également être une instance locale de services de domaine Active Directory, fédérée avec l’annuaire Azure AD.

Pour plus d’informations, consultez [Intégration des identités locales avec Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Ajout de votre propre nom de domaine à Azure AD](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure prend désormais en charge la fédération avec Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Administration de votre annuaire Azure AD](../active-directory/active-directory-administer.md), [Gestion d’Azure AD à l’aide de Windows PowerShell](/powershell/azure/overview?view=azureadps-2.0) et [Ports et protocoles nécessaires à l’identité hybride](..//active-directory/connect/active-directory-aadconnect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associer ou ajouter un abonnement Azure à Azure Active Directory

1. Pour associer votre abonnement Azure à Azure Active Directory, faites de l’annuaire un annuaire approuvé pour l’abonnement Azure qui héberge la base de données. Pour plus d’informations, consultez [Association des abonnements Azure avec Azure AD](../active-directory/active-directory-how-subscriptions-associated-directory.md).
2. Utilisez le sélecteur de répertoire dans le portail Azure pour basculer vers l’abonnement associé au domaine.

   **Additional information :** chaque abonnement Azure dispose d’une relation d’approbation avec une instance Azure AD. Cela signifie qu'il approuve ce répertoire pour authentifier les utilisateurs, les services et les appareils. Plusieurs abonnements peuvent approuver le même annuaire, mais un abonnement n’approuve qu’un seul annuaire. Cette relation de confiance qu’un abonnement possède avec un répertoire est contraire à celle établie entre un abonnement et toutes les autres ressources Azure (sites Web, bases de données, etc.), qui se rapprochent plus des ressources enfants d'un abonnement. Lorsqu’un abonnement expire, les autres ressources associées à l'abonnement deviennent également inaccessibles. Mais le répertoire reste dans Azure, et vous pouvez associer un autre abonnement à ce répertoire et continuer à gérer les utilisateurs du répertoire. Pour plus d’informations sur les ressources, consultez [Comprendre l’accès aux ressources dans Azure](../active-directory/active-directory-b2b-admin-add-users.md). Pour en savoir plus sur cette relation approuvée, consultez [Comment associer ou ajouter un abonnement Azure à Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Créer un administrateur d’Azure AD pour le serveur SQL Azure
Chaque serveur Azure SQL Server (qui héberge une base de données SQL ou un entrepôt SQL Data Warehouse) démarre avec un compte d’administrateur de serveur unique, qui est l’administrateur du serveur Azure SQL Server entier. Un deuxième administrateur SQL Server doit être créé. Il s’agit d’un compte Azure AD. Cet utilisateur principal est créé en tant qu’utilisateur de base de données à relation contenant-contenu dans la base de données master. En tant qu’administrateurs, les comptes d’administrateur de serveur sont des membres du rôle **db_owner** de chaque base de données utilisateur, puis saisissez chaque base de données utilisateur en tant utilisateur **dbo**. Pour plus d’informations sur les comptes d’administrateur de serveur, consultez [Gestion des bases de données et des connexions dans Azure SQL Database](sql-database-manage-logins.md).

Lorsque vous utilisez Azure Active Directory avec la géo-réplication, le compte administrateur de Microsoft Azure Active Directory doit être configuré pour le serveur principal et le serveur secondaire. Si un serveur ne dispose pas d’un administrateur Azure Active Directory, les utilisateurs Azure Active Directory reçoivent un message d’erreur « Impossible de se connecter au serveur ».

> [!NOTE]
> Les utilisateurs qui ne sont pas basés sur un compte Azure AD (y compris le compte d’administrateur de serveur SQL Azure) ne peuvent pas créer d’utilisateurs Azure AD, car ils n’ont pas l’autorisation de valider des utilisateurs de base de données proposés avec Azure AD.
> 

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Approvisionner un administrateur d’Azure Active Directory pour votre instance gérée

> [!IMPORTANT]
> Suivez ces étapes uniquement si vous approvisionnez une instance gérée. Cette opération peut être exécutée uniquement par l’administrateur global/de la société dans Azure AD. Les étapes suivantes décrivent le processus d’octroi d’autorisations pour les utilisateurs bénéficiant de privilèges différents dans le répertoire.

Votre instance gérée a besoin d’autorisations de lecture pour Azure AD afin d’effectuer correctement des tâches telles que l’authentification des utilisateurs via l’appartenance au groupe de sécurité ou la création de nouveaux utilisateurs. Pour ce faire, vous devez accorder des autorisations de lecture pour Azure AD à une instance gérée. Il y a deux manières de procéder : à partir du portail ou à partir de PowerShell. Les deux méthodes sont décrites ci-dessous.

1. Dans le portail Azure, dans le coin supérieur droit, cliquez sur votre connexion pour développer une liste déroulante de répertoires Active Directories potentiels. 
2. Choisissez l’annuaire Active Directory approprié en tant qu’Azure AD par défaut. 

   Cette étape lie l’abonnement associé à Active Directory à l’instance gérée, en s’assurant que le même abonnement est bien utilisé pour Azure AD et pour l’instance gérée.
3. Accédez à une instance gérée et sélectionnez celle que vous souhaitez utiliser pour l’intégration d’Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4.  Cliquez sur la bannière en haut de la page d’administration Active Directory. Si vous êtes connecté à Azure AD en tant qu’administrateur global/de société, vous pouvez le faire depuis le portail Azure ou à l’aide de PowerShell.

    ![accorder des autorisations – portail](./media/sql-database-aad-authentication/grant-permissions.png)

    ![accorder des autorisations – powershell](./media/sql-database-aad-authentication/grant-permissions-powershell.png)
    
    Si vous êtes connecté à Azure AD en tant qu’administrateur global/de société, vous pouvez le faire depuis le portail Azure ou exécuter un script PowerShell.

5. Après que l’opération est terminée avec succès, la notification suivante s’affichera dans le coin supérieur droit :

    ![réussi](./media/sql-database-aad-authentication/success.png)

6.  Vous pouvez maintenant choisir votre administrateur Azure AD pour votre Instance gérée. Pour cela, à la page Administrateur Active Directory, cliquez sur la commande **Définir l’administrateur**.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. À la page Ajouter un administrateur, recherchez un utilisateur, sélectionnez l’utilisateur ou le groupe à définir en tant qu’administrateur, puis cliquez sur **Sélectionner**. 

   La page Administrateur Active Directory affiche tous les membres et groupes présents dans Active Directory. Les utilisateurs ou les groupes grisés ne peuvent être sélectionnés, car ils ne sont pas pris en charge en tant qu’administrateurs Azure AD. Voir la liste des administrateurs pris en charge dans [Fonctionnalités et limitations Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). Le contrôle d'accès basé sur les rôles (RBAC) s'applique uniquement au portail Azure et n'est pas propagé vers SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. En haut de la page Administrateur Active Directory, cliquez sur **Enregistrer**.

    ![Enregistrer](./media/sql-database-aad-authentication/save.png)

    La procédure de changement de l’administrateur peut prendre plusieurs minutes. Le nouvel administrateur apparaît dans la zone Administrateur Active Directory.

> [!IMPORTANT]
> Lors de la configuration de l’administrateur Azure AD, le nom du nouvel administrateur (utilisateur ou groupe) ne peut pas déjà être présent dans la base de données MASTER virtuelle en tant qu’utilisateur de l’authentification SQL Server. Si tel est le cas, la configuration de l’administrateur d’Azure AD échoue et annule sa création, indiquant que cet administrateur (ce nom) existe déjà. Dans la mesure où un utilisateur de l’authentification SQL Server ne fait pas partie d’Azure AD, tout effort pour se connecter au serveur à l’aide de l’authentification Azure AD échoue.

> [!TIP]
> Pour supprimer un administrateur, en haut de la page Administrateur Active Directory, cliquez sur **Supprimer l’administrateur**, puis sur **Enregistrer**.
 
## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Approvisionner un administrateur Azure Active Directory pour votre serveur Azure SQL Database

> [!IMPORTANT]
> Suivez ces étapes uniquement si vous configurez un serveur Azure SQL Database ou Data Warehouse.

Les deux procédures suivantes vous montrent comment approvisionner un administrateur d’Active Directory Azure pour votre serveur SQL Azure dans le portail Azure et à l’aide de PowerShell.

### <a name="azure-portal"></a>Portail Azure
1. Dans le [Portail Azure](https://portal.azure.com/), dans le coin supérieur droit, cliquez sur votre connexion pour développer une liste déroulante de répertoires Active Directories potentiels. Choisissez l’annuaire Active Directory approprié en tant qu’Azure AD par défaut. Cette étape lie l’association de l’abonnement avec Active Directory et le serveur SQL Azure, ce qui garantit que le même abonnement est utilisé à la fois pour Azure AD et pour SQL Server. (Le serveur SQL Azure peut héberger la base de données SQL Azure ou l’entrepôt Azure SQL Data Warehouse.)   
    ![choose-ad][8]   
    
2. Dans la bannière de gauche, sélectionnez **Serveurs SQL**, sélectionnez votre **serveur SQL**, puis, à la page **SQL Server**, cliquez sur **Administrateur Active Directory**.   
3. À lapage **Administrateur Active Directory**, cliquez sur **Définir l’administrateur**.   
    ![sélectionner active directory](./media/sql-database-aad-authentication/select-active-directory.png)  
    
4. À la page **Ajouter un administrateur**, recherchez un utilisateur, sélectionnez l’utilisateur ou le groupe à définir en tant qu’administrateur, puis cliquez sur **Sélectionner**. (La page Administrateur Active Directory affiche tous les membres et groupes présents dans Active Directory. Les utilisateurs ou les groupes grisés ne peuvent être sélectionnés, car ils ne sont pas pris en charge en tant qu’administrateurs Azure AD. (Consultez la liste des administrateurs de prise en charge dans la section **Fonctionnalités et limitations d’Azure AD** de [Utiliser l’authentification Azure Active Directory pour l’authentification auprès de SQL Database ou de SQL Data Warehouse](sql-database-aad-authentication.md).) Le contrôle d'accès basé sur les rôles (RBAC) s'applique uniquement au portail et n'est pas propagé vers SQL Server.   
    ![sélectionner l’administrateur](./media/sql-database-aad-authentication/select-admin.png)  
    
5. En haut de la page **Administrateur Active Directory**, cliquez sur **ENREGISTRER**.   
    ![enregistrer l’administrateur](./media/sql-database-aad-authentication/save-admin.png)   

La procédure de changement de l’administrateur peut prendre plusieurs minutes. Le nouvel administrateur apparaîtra dans la zone **Administrateur Active Directory** .

   > [!NOTE]
   > Lors de la configuration de l’administrateur Azure AD, le nom du nouvel administrateur (utilisateur ou groupe) ne peut pas déjà être présent dans la base de données MASTER virtuelle en tant qu’utilisateur de l’authentification SQL Server. Si tel est le cas, la configuration de l’administrateur d’Azure AD échoue, annulant sa création et indiquant que cet administrateur (ce nom) existe déjà. Dans la mesure où un utilisateur de l’authentification SQL Server ne fait pas partie d’Azure AD, tout effort pour se connecter au serveur à l’aide de l’authentification Azure AD échoue.
   > 


Pour supprimer un administrateur, en haut de la page **Administrateur Active Directory**, cliquez sur **Supprimer l’administrateur**, puis sur **Enregistrer**.

### <a name="powershell"></a>PowerShell
Pour exécuter les applets de commande PowerShell, Azure PowerShell doit être installé et en cours d’exécution. Pour plus de détails, consultez la rubrique [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

Pour configurer un administrateur Azure AD, exécutez les commandes Azure PowerShell suivantes :

* Add-AzureRmAccount
* Select-AzureRmSubscription

Applets de commande utilisées pour configurer et gérer Azure AD admin :

| Nom de l’applet de commande | Description |
| --- | --- |
| [Set-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/set-azurermsqlserveractivedirectoryadministrator) |Approvisionne un administrateur Azure Active Directory pour le serveur Azure SQL Server ou Azure SQL Data Warehouse. (À partir de l’abonnement actuel) |
| [Remove-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/remove-azurermsqlserveractivedirectoryadministrator) |Supprime un administrateur Azure Active Directory pour le serveur Azure SQL Server ou Azure SQL Data Warehouse. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator) |Renvoie les informations sur un administrateur Azure Active Directory actuellement configuré pour le serveur Azure SQL Server ou Azure SQL Data Warehouse. |

Utilisez la commande PowerShell get-help pour obtenir plus de détails sur chacune de ces commandes, par exemple ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Le script suivant configure un groupe d’administrateurs Azure AD nommé **DBA_Group** (id d’objet `40b79501-b343-44ed-9ce7-da4c8cc7353f`) pour le serveur **demo_server** d’un groupe de ressources nommé **Group-23** :

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

Le paramètre d’entrée **DisplayName** accepte le nom d’affichage Azure AD ou le nom principal de l’utilisateur. Par exemple, ``DisplayName="John Smith"`` et ``DisplayName="johns@contoso.com"``. Pour les groupes Azure AD, seul le nom d’affichage est pris en charge.

> [!NOTE]
> La commande Azure PowerShell ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` ne vous empêche pas de configurer des administrateurs Azure AD pour des utilisateurs non pris en charge. Un utilisateur non pris en charge peut être configuré, mais il ne peut pas se connecter à une base de données. 
> 

L'exemple suivant utilise l' **ObjectID**facultatif en option :

```
Set-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **L’ObjectID** Azure AD est requis lorsque le **DisplayName** n’est pas unique. Pour récupérer les valeurs **ObjectID** et **DisplayName**, utilisez la section Active Directory du portail Azure Classic et affichez les propriétés d’un utilisateur ou d’un groupe.
> 

L’exemple suivant renvoie des informations sur l’administrateur Azure AD admin pour le serveur SQL Azure :

```
Get-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

L’exemple suivant supprime un administrateur Azure AD :

```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

Vous pouvez également approvisionner un administrateur Azure Active Directory à l’aide de l’API REST. Pour plus d’informations, consultez [Référence de l’API REST de gestion des services et Opérations sur les bases de données SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

### <a name="cli"></a>Interface de ligne de commande  
Vous pouvez également configurer un administrateur Azure AD en appelant les commandes CLI suivantes :
| Commande | Description |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_create) |Approvisionne un administrateur Azure Active Directory pour le serveur Azure SQL Server ou Azure SQL Data Warehouse. (À partir de l’abonnement actuel) |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_delete) |Supprime un administrateur Azure Active Directory pour le serveur Azure SQL Server ou Azure SQL Data Warehouse. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) |Renvoie les informations sur un administrateur Azure Active Directory actuellement configuré pour le serveur Azure SQL Server ou Azure SQL Data Warehouse. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_update) |Met à jour l’administrateur Active Directory pour un serveur Azure SQL Server ou pour Azure SQL Data Warehouse. |

Pour plus d’informations sur les commandes CLI, consultez [SQL Server - az sql server](https://docs.microsoft.com/cli/azure/sql/server).  


## <a name="configure-your-client-computers"></a>Configurer vos ordinateurs clients
Sur toutes les machines clientes à partir desquelles vos applications ou les utilisateurs se connectent au service Base de données SQL Azure ou Azure SQL Data Warehouse à l’aide d’identités Azure AD, vous devez installer les logiciels suivants :

* .NET Framework version 4.6 ou ultérieure de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
* La bibliothèque d’authentification Azure Active Directory pour SQL Server (**ADALSQL. DLL**) est disponible en plusieurs langues (x86 et amd64) à partir du centre de téléchargement de la [Bibliothèque d’authentification Microsoft Active Directory pour Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

Vous pouvez répondre à ces exigences en procédant comme suit :

* L’installation de [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou de [SQL Server Data Tools pour Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) est conforme à la configuration requise de .NET Framework 4.6.
* SSMS installe la version x86 de **ADALSQL. DLL**.
* SSDT installe la version amd64 de **ADALSQL. DLL**.
* La dernière version de Visual Studio de [Téléchargements Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) respecte la configuration requise de .NET Framework 4.6, mais n'installe pas la version requise amd64 de **ADALSQL.DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Créer des utilisateurs de base de données à relation contenant-contenu dans votre base de données mappés sur les identités Azure AD

L’authentification Azure Active Directory nécessite que les utilisateurs de base de données soient créés en tant qu’utilisateurs de base de données à relation contenant-contenu. Un utilisateur de base de données à relation contenant-contenu sur une identité Azure AD est un utilisateur de base de données qui ne dispose pas de connexion dans la base de données MASTER, et qui est mappé à une identité située dans l’annuaire Azure AD associé à la base de données. L’identité Azure AD peut être un compte d’utilisateur individuel ou un groupe. Pour plus d'informations sur les utilisateurs de base de données à relation contenant-contenu, consultez [Utilisateurs de base de données - Rendre votre base de données portable](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Il n’est pas possible de créer des utilisateurs de base de données (à l’exception d’administrateurs) via le portail. Les rôles RBAC ne sont pas propagés à SQL Server, Base de données SQL ou SQL Data Warehouse. Les rôles RBAC Azure sont utilisés pour la gestion des ressources Azure et ne s’appliquent pas aux autorisations de base de données. Par exemple, le rôle **Contributeur de SQL Server** ne permet pas de se connecter à Base de données SQL ou à SQL Data Warehouse. L’accès doit être accordé directement dans la base de données à l’aide d’instructions Transact-SQL.
>

Pour créer un utilisateur de base de données à relation contenant-contenu Azure AD (autre que l’administrateur du serveur propriétaire de la base de données), connectez-vous à la base de données avec une identité Azure AD en tant qu’utilisateur avec au moins l’autorisation **MODIFIER UN UTILISATEUR** . Utilisez ensuite la syntaxe Transact-SQL suivante :

```
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* peut être le nom d’utilisateur principal d’un utilisateur Azure AD ou le nom d’affichage d’un groupe Azure AD.

**Exemples :** pour créer une base de données à relation contenant-contenu représentant un utilisateur de domaine fédéré ou géré Azure AD :
```
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Pour créer un utilisateur de base de données à relation contenant-contenu représentant un groupe de domaine Azure AD ou fédéré, définissez le nom complet d’un groupe de sécurité :
```
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Pour créer un utilisateur de base de données à relation contenant-contenu représentant une application qui se connecte à l’aide d’un jeton Azure AD :

```
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

>  [!TIP]
>  Vous ne pouvez pas créer directement un utilisateur à partir d’un annuaire Azure Active Directory autre que l’annuaire Azure Active Directory associé à votre abonnement Azure. Toutefois, les membres d’autres annuaires Active Directory qui sont des utilisateurs importés dans l’annuaire Active Directory associé (appelés utilisateurs externes) peuvent être ajoutés à un groupe Active Directory dans le client Active Directory. En créant un utilisateur de base de données à relation contenant-contenu pour ce groupe AD, les utilisateurs de l’annuaire Active Directory externe peuvent accéder SQL Database.   

Pour plus d’informations sur la création d’utilisateurs de base de données à relation contenant-contenu basés sur des identités Azure Active Directory, voir [CRÉER UN UTILISATEUR (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> La suppression de l’administrateur Azure Active Directory pour le serveur Azure SQL Server empêche tout utilisateur de l’authentification Azure AD de se connecter au serveur. Si nécessaire, des utilisateurs Azure AD inutilisables peuvent être supprimés manuellement par un administrateur du service Base de données SQL.   

>  [!NOTE]
>  Si vous recevez le message **Délai d’expiration de la connexion dépassé**, vous devrez peut-être définir le paramètre `TransparentNetworkIPResolution` de la chaîne de connexion sur la valeur false. Pour plus d’informations, consultez [Problème lié au délai d’expiration de la connexion avec .NET Framework 4.6.1 - TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/2016/05/07/connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).   

   
Lorsque vous créez un utilisateur de base de données, il reçoit l’autorisation **CONNECT** et peut se connecter à cette base de données en tant que membre du rôle **PUBLIC**. À l'origine, les seules autorisations disponibles pour l'utilisateur sont celles qui sont accordées au rôle **PUBLIC** ou aux groupes Azure AD dont il est membre. Une fois que vous avez configuré un utilisateur de base de données Azure à relation contenant-contenu, vous pouvez octroyer à cet utilisateur des autorisations supplémentaires, de la même façon que vous accordez l’autorisation à un autre type d’utilisateur. En général, on accorde les autorisations aux rôles de base de données, puis on ajoute des utilisateurs aux rôles. Pour plus d’informations, consultez [Notions de base sur les autorisations de moteur de base de données](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Pour plus d'informations sur les rôles de base de données SQL, consultez [Gestion des bases de données et des connexions dans la base de données SQL Azure](sql-database-manage-logins.md).
Un compte d’utilisateur de domaine fédéré importé dans un domaine managé comme utilisateur externe doit utiliser l’identité de domaine managé.

> [!NOTE]
> Les utilisateurs AD Azure sont marqués dans les métadonnées de la base de données avec le type E (EXTERNAL_USER) et pour les groupes avec le type X (EXTERNAL_GROUPS). Pour plus d’informations, consultez [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx). 
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Se connecter à la base de données utilisateur ou à l’entrepôt de données à l’aide de SSMS ou de SSDT  
Pour vérifier que l’administrateur Azure AD est correctement configuré, connectez-vous à la base de données **master** en utilisant un compte d’administrateur Azure AD.
Pour configurer un utilisateur de base de données à relation contenant-contenu Azure AD (autre que l’administrateur de serveur propriétaire de la base de données), connectez-vous à la base de données avec une identité Azure AD ayant accès à la base de données.

> [!IMPORTANT]
> La prise en charge de l’authentification Azure Active Directory est disponible avec [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) et [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) dans Visual Studio 2015. La version d’août 2016 de SSMS inclut également la prise en charge de l’authentification universelle Active Directory, qui permet aux administrateurs d’exiger l’authentification multifacteur par appel téléphonique, SMS, cartes à puce avec code PIN ou notification d’application mobile.
 
## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Utilisation d’une identité Azure AD pour se connecter à l’aide de SSMS ou de SSDT  

Les procédures suivantes vous montrent comment se connecter à une base de données SQL avec une identité Azure AD à l’aide de SQL Server Management Studio ou de SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Authentification intégrée d'Active Directory

Utilisez cette méthode si vous êtes connecté à Windows avec vos informations d’identification Azure Active Directory à partir d’un domaine fédéré.

1. Démarrez Management Studio ou Data Tools et, dans la boîte de dialogue **Se connecter au serveur** (ou **Se connecter au moteur de base de données**), dans la zone **Authentification**, sélectionnez **Active Directory - Authentification intégrée**. Aucun mot de passe n’est nécessaire ou ne peut être saisi, car les informations d’identification existantes sont présentées pour la connexion.   

    ![Sélectionner l’authentification intégrée AD][11]
2. Cliquez sur le bouton **Options** puis, sur la page **Propriétés de connexion**, dans la zone **Se connecter à la base de données**, tapez le nom de la base de données utilisateur à laquelle vous souhaitez vous connecter. (L’option **Nom du domaine AD ou ID de locataire** est uniquement prise en charge pour les options **Authentification universelle avec prise en charge de MFA** ; dans le cas contraire, elle est grisée.)  

    ![Sélectionner le nom de la base de données][13]

## <a name="active-directory-password-authentication"></a>Authentification par mot de passe Active Directory

Utilisez cette méthode lors de la connexion avec un nom principal Azure AD à l’aide du domaine géré d’Azure AD. Vous pouvez également l’utiliser pour un compte fédéré sans accéder au domaine, par exemple lorsque vous travaillez à distance.

Utilisez cette méthode si vous êtes connecté à Windows à l’aide des informations d’identification d’un domaine qui n’est pas fédéré avec Azure, ou lorsque vous utilisez l’authentification Azure AD à l’aide d’Azure AD sur le domaine initial ou le domaine client.

1. Démarrez Management Studio ou Data Tools et, dans la boîte de dialogue **Se connecter au serveur** (ou **Se connecter au moteur de base de données**), dans la zone **Authentification**, sélectionnez **Active Directory - Authentification par mot de passe**.
2. Dans la zone **Nom d’utilisateur** tapez votre nom d’utilisateur Azure Active Directory au format **username@domain.com**. Il soit s’agir d’un compte Azure Active Directory ou d’un compte de domaine fédéré avec Azure Active Directory.
3. Dans la zone **Mot de passe** , tapez votre mot de passe utilisateur pour le compte Azure Active Directory ou le compte de domaine fédéré.

    ![Sélectionner l’authentification par mot de passe AD][12]
4. Cliquez sur le bouton **Options** puis, sur la page **Propriétés de connexion**, dans la zone **Se connecter à la base de données**, tapez le nom de la base de données utilisateur à laquelle vous souhaitez vous connecter. (Voir le graphique dans l’option précédente.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Utilisation d’une identité Azure AD pour se connecter à partir d’une application cliente

Les procédures suivantes vous montrent comment se connecter à une base de données SQL avec une identité Azure AD à partir d’une application cliente.

###  <a name="active-directory-integrated-authentication"></a>Authentification intégrée d'Active Directory

Pour utiliser l’authentification Windows intégrée, l’Active Directory de votre domaine doit être fédéré avec Azure Active Directory. Votre application cliente (ou un service) se connectant à la base de données doit être en cours d’exécution sur un ordinateur joint au domaine, et dont les informations d’identification de domaine sont celles d’un utilisateur.

Pour vous connecter à une base de données à l’aide de l’authentification intégrée et d’une identité Azure AD, le mot clé d’authentification de la chaîne de connexion de base de données doit avoir la valeur Active Directory intégré. L’exemple de code C# suivant utilise ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Le mot clé de la chaîne de connexion ``Integrated Security=True`` n’est pas pris en charge pour la connexion à la base de données SQL Azure. Lorsque vous établissez une connexion ODBC, vous devez supprimer les espaces et définir l’authentification sur « ActiveDirectoryIntegrated ».

### <a name="active-directory-password-authentication"></a>Authentification par mot de passe Active Directory

Pour vous connecter à une base de données à l’aide de l’authentification intégrée et d’une identité Azure AD, le mot clé d’authentification doit être le mot de passe Active Directory. La chaîne de connexion doit contenir les mots clés et valeurs d’ID utilisateur/UID et de mot de passe/PWD. L’exemple de code C# suivant utilise ADO .NET.

```
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Pour en savoir plus sur les méthodes d’authentification Azure AD, utilisez les exemples de code de démonstration disponibles dans [Démonstration GitHub de l’authentification Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Jeton Azure AD
Cette méthode d’authentification permet aux services de couche intermédiaire de se connecter à la base de données SQL Azure ou à Azure SQL Data Warehouse en obtenant un jeton d’Azure Active Directory (AAD). Elle permet l’utilisation de scénarios complexes, notamment l’authentification par certificat. Vous devez effectuer quatre étapes de base pour utiliser l’authentification par jeton Azure AD :

1. Inscrivez votre application auprès d’Azure Active Directory et obtenez l’ID client de votre code. 
2. Créez un utilisateur de base de données qui représente l’application. (Effectué à l’étape 6.)
3. Créez un certificat sur l’ordinateur client qui exécute l’application.
4. Ajoutez le certificat en tant que clé pour votre application.

Exemple de chaîne de connexion :

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Pour plus d’informations, consultez le [Blog de sécurité de SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Pour plus d’informations sur l’ajout de certificat, consultez [Bien démarrer avec l’authentification par certificat dans Azure Active Directory](../active-directory/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Les instructions suivantes permettent de se connecter à l’aide de la version 13.1 de sqlcmd, qui est disponible dans le [Centre de téléchargement](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Étapes suivantes
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
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png

