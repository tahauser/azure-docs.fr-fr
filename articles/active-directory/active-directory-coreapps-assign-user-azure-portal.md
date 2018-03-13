---
title: "Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory | Microsoft Docs"
description: "Comment sélectionner une application d’entreprise pour affecter un utilisateur ou un groupe dans Azure Active Directory"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2017
ms.author: daveba
ms.reviewer: luleon
ms.openlocfilehash: b65284f799eca956c30db21d5d4171d0495297ea
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Affecter un utilisateur ou un groupe à une application d’entreprise dans Azure Active Directory
Pour affecter un utilisateur ou un groupe à une application d’entreprise, vous devez disposer des autorisations nécessaires pour gérer l’application d’entreprise, et vous devez être l’administrateur général du répertoire.
> [!NOTE]
> Dans le cas des applications Microsoft (par exemple, les applications Office 365), utilisez PowerShell pour affecter des utilisateurs à une application d’entreprise.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Comment faire pour attribuer l’accès à une application d’entreprise à un utilisateur dans le portail Azure ?
1. Connectez-vous au [portail Azure](https://portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Tous les services**, entrez Azure Active Directory dans la zone de texte, puis sélectionnez **Entrée**.
3. Dans le panneau **Azure Active Directory - *Nom_répertoire*** (autrement dit, le panneau Azure AD correspondant au répertoire que vous gérez), sélectionnez **Applications d’entreprise**.

    ![Ouverture des applications d’entreprise](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. Dans le panneau **Applications d’entreprise**, sélectionnez **Toutes les applications**. Cette action répertorie les applications que vous pouvez gérer.
5. Sur le panneau **Applications d’entreprise - Toutes les applications** , sélectionnez une application.
6. Dans le panneau ***NomApplication*** (autrement dit, le panneau avec le nom de l’application sélectionnée dans le titre), sélectionnez **Utilisateurs et groupes**.

    ![Sélection de la commande Toutes les applications](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. Dans le panneau ***NomApplication*** **- Affectation d’utilisateurs et de groupes**, sélectionnez la commande **Ajouter**.
8. Dans le panneau **Ajouter une affectation**, sélectionnez **Utilisateurs et groupes**.

    ![Affecter un utilisateur ou un groupe à l’application](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. Dans le panneau **Utilisateurs et groupes**, sélectionnez un ou plusieurs utilisateurs ou groupes dans la liste, puis sélectionnez le bouton **Sélectionner** en bas du panneau.
10. Dans le panneau **Ajouter une affectation**, sélectionnez **Rôle**. Puis, dans le panneau **Sélectionner un rôle**, choisissez un rôle à appliquer aux utilisateurs ou groupes sélectionnés, puis cliquez sur le bouton **OK** en bas du panneau.
11. Dans le panneau **Ajouter une affectation**, sélectionnez le bouton **Affecter** en bas du panneau. Les utilisateurs ou les groupes ont les autorisations définies par le rôle sélectionné pour cette application d’entreprise.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Comment faire pour affecter un utilisateur à une application d’entreprise à l’aide de PowerShell ?

1. Ouvrez une invite de commandes Windows PowerShell avec des privilèges élevés.

    >[!NOTE] 
    > Vous devez installer le module AzureAD (utilisez la commande `Install-Module -Name AzureAD`). Si vous y êtes invité, installez un module NuGet ou le nouveau module Azure Active Directory V2 PowerShell, tapez O et appuyez sur Entrée.

2. Exécutez `Connect-AzureAD` et connectez-vous avec un compte d’utilisateur Administrateur général.
3. Pour affecter un utilisateur et un rôle à une application, utilisez le script suivant :

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

Pour plus d’informations sur la façon d’affecter un utilisateur à un rôle d’application, consultez la documentation de [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0).

### <a name="example"></a>exemples

Cet exemple affecte l’utilisateur Britta Simon à l’application [Microsoft Workplace Analytics](https://products.office.com/en-us/business/workplace-analytics) à l’aide de PowerShell.

1. Dans PowerShell, affectez les valeurs correspondantes aux variables $username, $app_name et $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. Dans cet exemple, nous ignorons le nom exact du rôle d’application à attribuer à Britta Simon. Exécutez les commandes suivantes pour obtenir l’utilisateur ($user) et le principal du service ($sp) à l’aide de l’UPN de l’utilisateur et du nom complet du principal du service.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Exécutez la commande `$sp.AppRoles` afin d’afficher les rôles disponibles pour l’application Workplace Analytics. Dans cet exemple, vous souhaitez affecter à Britta Simon le rôle Analyst (Limited access).
    
    ![Rôle dans Workplace Analytics](media/active-directory-coreapps-assign-user-azure-portal/workplace-analytics-role.png)

4. Affectez le nom de rôle à la variable `$app_role_name`.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    ```

5. Exécutez la commande suivante pour affecter l’utilisateur au rôle d’application :

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Étapes suivantes
* [Voir tous mes groupes](active-directory-groups-view-azure-portal.md)
* [Supprimer l’affectation d’un utilisateur ou d’un groupe à une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Désactiver les connexions utilisateur pour une application d’entreprise](active-directory-coreapps-disable-app-azure-portal.md)
* [Modifier le nom ou le logo d’une application d’entreprise dans la version préliminaire d’Azure Active Directory](active-directory-coreapps-change-app-logo-user-azure-portal.md)
