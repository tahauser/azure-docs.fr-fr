---
title: "Didacticiel : Intégration de Azure Active Directory à @Task | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et @Task."
services: active-directory
documentationcenter: 
author: jeevansd
manager: mtillman
editor: 
ms.assetid: aab8bd2f-f9dd-42da-a18e-d707865687d7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: 2443c7e2e57bd9c36af292658ff85a973d5ec224
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-task"></a>Didacticiel : Intégration d'Azure Active Directory à @Task
L’objectif de ce didacticiel est de vous montrer comment intégrer @Task dans Azure AD (Azure Active Directory).  
L’intégration de @Task dans Azure AD vous offre les avantages suivants : 

* Dans Azure AD, vous pouvez contrôler qui a accès à @Task
* Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à @Task (par authentification unique) avec leur compte Azure AD
* Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis
Pour configurer l’intégration de Azure AD avec @Task, vous avez besoin des éléments suivants :

* Un abonnement Azure AD
* Un abonnement @Task pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.
> 
> 

Vous devez en outre suivre les recommandations ci-dessous :

* Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
* Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="scenario-description"></a>Description du scénario
Ce didacticiel vise à vous permettre de tester l’authentification unique Azure AD dans un environnement de test.  
Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de @Task depuis la galerie 
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-task-from-the-gallery"></a>Ajout de @Task depuis la galerie
Pour configurer l’intégration de @Task avec Azure AD, vous devez ajouter @Task disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter @Task à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**. 
   
    ![Active Directory][1] 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.
   
    ![Applications][2] 
4. Cliquez sur **Ajouter** en bas de la page.
   
    ![Applications][3] 
5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.
   
    ![Applications][4] 
6. Dans la zone de recherche, tapez **@Task**.
   
    ![Applications][5] 
7. Dans le volet des résultats, sélectionnez **@Task**, puis cliquez sur **Terminer** pour ajouter l’application.
   
    ![Applications][30] 

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
L’objectif de cette section est de vous montrer comment configurer et tester l’authentification unique Azure AD avec @Task sur un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur @Task équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur @Task associé doit être établie.   
Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans @Task.

Pour configurer et tester l’authentification unique Azure AD avec @Task, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test @Tasktest](#creating-a-halogen-software-test-user)** pour avoir un équivalent de Britta Simon dans @Taskthat lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD
L’objectif de cette section est d’activer l’authentification unique Azure AD dans le portail Azure Classic et de configurer l’authentification unique dans votre application @Task.

**Pour configurer l’authentification unique Azure AD avec @Task, procédez comme suit :**

1. Dans le portail Azure Classic, dans la page d’intégration d’applications **@Task**, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
   
    ![Configurer l’authentification unique][6] 
2. Sur la page **Comment voulez-vous que les utilisateurs se connectent à Condeco@Task**, sélectionnez **Authentification unique Azure AD**, puis cliquez sur **Suivant**.
   
    ![Authentification unique Azure AD][7] 
3. Sur la page **Configurer les paramètres d’application** , procédez comme suit :
   
    ![Configurer les paramètres d’application][8] 
   
     a. Dans la zone de texte **URL d’authentification**, entrez l'URL utilisée par vos utilisateurs pour se connecter à votre application @Task (par exemple : *https://<Tenant name>.attask-ondemand.com*).
   
     b. Cliquez sur **Suivant**.
4. Dans la page **Configurer l’authentification unique sur @Task**, cliquez sur **Télécharger les métadonnées**, enregistrez le fichier de métadonnées localement sur votre ordinateur, puis cliquez sur **Suivant**.
   
    ![Qu’est-ce qu’Azure AD Connect ?][9] 
5. Connectez-vous à votre site d’entreprise @Task en tant qu’administrateur.
6. Accédez à **Single Sign On Configuration**.
7. Dans la boîte de dialogue **Authentification unique** , procédez comme suit :
   
    ![Configurer l’authentification unique][23]
   
    a. Comme **Type**, sélectionnez **SAML 2.0**.
   
    b. Sélectionnez **///ID fournisseur de services**.
   
    c. Dans le portail Azure Classic, copiez **l’URL de connexion distante**, puis collez-la dans la zone de texte **URL du portail de connexion**.
   
    d. Dans le portail Azure Classic, copiez **l’URL du service de déconnexion unique**, puis collez-la dans la zone de texte **URL de déconnexion**.
   
    e. Dans le portail Azure Classic, copiez la valeur **Modifier l’URL de mot de passe**, puis collez-la dans la zone de texte **Modifier l’URL de mot de passe**.
   
    f. Cliquez sur **Enregistrer**.
8. Dans le portail Azure Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**. 
   
    ![Qu’est-ce qu’Azure AD Connect ?][10]
9. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.  
   
    ![Qu’est-ce qu’Azure AD Connect ?][11]

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure Classic.  

![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_02.png) 
2. Dans la liste **Annuaire** , sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.
3. Pour afficher la liste des utilisateurs, dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_03.png) 
4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas. 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_04.png) 
5. Sur la page de boîte de dialogue **Dites-nous en plus sur cet utilisateur** , procédez comme suit : 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_05.png) 
   
    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.
   
    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.
   
    c. Cliquez sur **Suivant**.
6. Sur la page de boîte de dialogue **Profil utilisateur** , procédez comme suit : 
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_06.png) 
   
    a. Dans la zone de texte **First Name**, tapez **Britta**.  
   
    b. Dans la zone de texte **Last Name**, tapez **Simon**.
   
    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.
   
    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Suivant**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_07.png) 
8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire** , procédez comme suit :
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-attask-tutorial/create_aaduser_08.png) 
   
    a. Notez la valeur du **Nouveau mot de passe**.
   
    b. Cliquez sur **Terminé**.   

### <a name="creating-an-task-test-user"></a>Création d’un utilisateur de test @Task
L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans @Task.

**Pour créer un utilisateur appelé Britta Simon dans @Task, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise @Task en tant qu’administrateur.
2. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
3. Cliquez sur **New Person**. 
4. Dans la boîte de dialogue New User, procédez comme suit :
   
    ![Création d’un utilisateur de test @Task][21] 
   
    a. Dans la zone de texte **Prénom** , tapez Britta.
   
    b. Dans la zone de texte **Last Name** , tapez Simon.
   
    c. Dans la zone de texte **Adresse de messagerie** , tapez l'adresse de messagerie de Simon Britta dans Azure Active Directory.
   
    d. Cliquez sur **Add Person**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD
L’objectif de cette section est de permettre à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à @Task.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à @Task, procédez comme suit :**

1. Pour ouvrir l’affichage des applications dans le portail Azure Classic, cliquez dans l’affichage de l’annuaire sur **Applications** dans le menu du haut.
   
    ![Affecter des utilisateurs][201] 
2. Dans la liste des applications, sélectionnez **@Task**.
   
    ![Affecter des utilisateurs][202] 
3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.
   
    ![Affecter des utilisateurs][203] 
4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.
5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.
   
    ![Affecter des utilisateurs][205]

### <a name="testing-single-sign-on"></a>Test de l’authentification unique
L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  
Lorsque vous cliquez sur la vignette @Task dans le volet d’accès, vous devez être connecté automatiquement à votre application @Task.

## <a name="additional-resources"></a>Ressources supplémentaires
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attask-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attask-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attask-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attask-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_01.png
[30]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_02.png


[6]: ./media/active-directory-saas-attask-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_03.png
[8]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_04.png
[9]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_05.png
[10]: ./media/active-directory-saas-attask-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-attask-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-attask-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_08.png


[23]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_06.png

[200]: ./media/active-directory-saas-attask-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attask-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attask-tutorial/tutorial_attask_09.png
[203]: ./media/active-directory-saas-attask-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-attask-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-attask-tutorial/tutorial_general_205.png






