---
title: "Apparence des applications dans le volet d’accès | Microsoft Docs"
description: "Identifier pourquoi une application apparaît dans le volet d’accès"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewr: japere
ms.openlocfilehash: 25e8bd404bc5b622568b8885f241c743f82f59cc
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2018
---
# <a name="how-applications-appear-on-the-access-panel"></a>Apparence des applications dans le volet d’accès

Le volet d’accès est un portail web qui permet à un utilisateur disposant d’un compte professionnel ou scolaire dans Azure Active Directory (Azure AD) de visualiser et de démarrer des applications basées sur le cloud auxquelles l’administrateur Azure AD lui a accordé un accès. Ces applications sont configurées pour le compte de l’utilisateur dans le portail Azure AD. L’administrateur peut fournir directement l’application à l’utilisateur ou à un groupe dont l’utilisateur fait partie, l’application apparaissant alors dans le volet d’accès de l’utilisateur.

## <a name="general-issues-to-check-first"></a>Problèmes d’ordre général à vérifier en premier

-   Si une application a été supprimée d’un utilisateur ou d’un groupe dont fait partie l’utilisateur, essayez de vous connecter/déconnecter de nouveau dans le volet d’accès de l’utilisateur après quelques minutes pour voir si l’application a été supprimée.

-   Si une licence a été supprimée pour un utilisateur ou un groupe dont l’utilisateur est membre, la prise en compte des modifications peut prendre du temps en fonction de la taille et de la complexité du groupe. Prévoyez du temps supplémentaire avant de vous connecter au volet d’accès.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problèmes liés à l’affectation des applications aux utilisateurs

Un utilisateur peut voir une application sur son volet d’accès car il y a été affecté auparavant. Voici différentes méthodes de vérification possibles :

-   [Vérifier si un utilisateur est affecté à l’application](#check-if-a-user-is-assigned-to-the-application)

-   [Vérifier si un utilisateur est affecté à une licence liée à l’application](#check-if-a-user-is-under-a-license-related-to-the-application)


### <a name="check-if-a-user-is-assigned-to-the-application"></a>Vérifier si un utilisateur est affecté à l’application

Pour vérifier si un utilisateur est affecté à l’application, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Applications d’entreprise** dans le menu de navigation de gauche d’Azure Active Directory.

5.  Cliquez sur **Toutes les applications** pour afficher la liste complète de vos applications.

6.  **Recherchez** le nom de l’application en question.

7.  Sélectionnez **Utilisateurs et groupes**.

8.  Vérifiez si votre utilisateur est affecté à l’application.

  * Si vous souhaitez supprimer l’utilisateur de l’application, **cliquez sur la ligne** de l’utilisateur et sélectionnez **Supprimer**.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Vérifier si un utilisateur est affecté à une licence liée à l’application

Pour vérifier les licences affectées à un utilisateur, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** pour le sélectionner.

7.  Cliquez sur **Licences** pour voir quelles licences sont actuellement affectées à l’utilisateur.

   * Si l’utilisateur est affecté à une licence Office, les applications Office internes apparaîtront dans le volet d’accès de l’utilisateur.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problèmes liés à l’affectation des applications aux groupes

Un utilisateur peut ne pas voir une application sur son volet d’accès, car il ne fait pas partie d’un groupe affecté à l’application. Voici différentes méthodes de vérification possibles :

-   [Vérifier les appartenances d’un utilisateur à des groupes](#check-a-users-group-memberships)

-   [Vérifier si un utilisateur fait partie d’un groupe affecté à une licence](#check-if-a-user-is-a-member-of-a-group-assigned-to-a-license)

### <a name="check-a-users-group-memberships"></a>Vérifier les appartenances d’un utilisateur à des groupes

Pour vérifier l’appartenance d’un utilisateur à un groupe, procédez comme suit :

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** pour le sélectionner.

7.  Cliquez sur **Groupes.**

8.  Vérifiez si votre utilisateur fait partie d’un groupe affecté à l’application.

   * Si vous souhaitez supprimer l’utilisateur du groupe, **cliquez sur la ligne** du groupe et sélectionnez Supprimer.

### <a name="check-if-a-user-is-a-member-of-a-group-assigned-to-a-license"></a>Vérifier si un utilisateur fait partie d’un groupe affecté à une licence

1.  Ouvrez le [**portail Azure**](https://portal.azure.com/) et connectez-vous en tant **qu’Administrateur général**.

2.  Ouvrez **l’extension Azure Active Directory** en cliquant sur **Tous les services** en haut du menu de navigation principal de gauche.

3.  Tapez « **Azure Active Directory** » dans la zone de recherche de filtre et sélectionnez l’élément **Azure Active Directory**.

4.  Cliquez sur **Utilisateurs et groupes** dans le menu de navigation.

5.  Cliquez sur **Tous les utilisateurs**.

6.  **Recherchez** l’utilisateur qui vous intéresse et **cliquez sur la ligne** pour le sélectionner.

7.  Cliquez sur **Groupes.**

8.  Cliquez sur la ligne d’un groupe spécifique.

9.  Cliquez sur **Licences** pour voir quelles licences sont affectées au groupe.

  * Si le groupe est affecté à une licence Office, certaines applications Office internes pourront apparaître dans le volet d’accès de l’utilisateur.


## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Si ces étapes de dépannage ne résolvent pas le problème

Créez un ticket de support en fournissant les informations suivantes, si disponibles :

-   ID d’erreur de corrélation

-   Nom d’utilisateur principal (adresse de messagerie de l’utilisateur)

-   ID client

-   Type de navigateur

-   Fuseau horaire et heure/période à laquelle l’erreur se produit

-   Traces Fiddler

## <a name="next-steps"></a>Étapes suivantes
[Gestion des applications avec Azure Active Directory](active-directory-enable-sso-scenario.md)
