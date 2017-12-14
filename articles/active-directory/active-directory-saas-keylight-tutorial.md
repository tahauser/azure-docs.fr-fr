---
title: "Didacticiel : Intégration d’Azure Active Directory avec LockPath Keylight | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et LockPath Keylight."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 74d9d80a1ce35f1766261038a985cc6f0cc39fd3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Didacticiel : Intégration d’Azure Active Directory avec LockPath Keylight

Dans ce didacticiel, vous allez apprendre à intégrer LockPath Keylight avec Azure Active Directory (Azure AD).

L’intégration de LockPath Keylight avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à LockPath Keylight.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à LockPath Keylight (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec LockPath Keylight, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement LockPath Keylight pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de LockPath Keylight à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Ajout de LockPath Keylight à partir de la galerie
Pour configurer l’intégration de LockPath Keylight à Azure AD, vous devez ajouter LockPath Keylight à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter LockPath Keylight à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **LockPath Keylight**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_search.png)

5. Dans le volet de résultats, sélectionnez **LockPath Keylight**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec LockPath Keylight sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur LockPath Keylight équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur LockPath Keylight associé doit être établie.

Dans LockPath Keylight, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec LockPath Keylight, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test LockPath Keylight](#creating-a-lockpath-keylight-test-user)** pour avoir un équivalent de Britta Simon dans LockPath Keylight lié à sa représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application LockPath Keylight.

**Pour configurer l’authentification unique Azure AD avec LockPath Keylight, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **LockPath Keylight**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_samlbase.png)

3. Dans la section **Domaine et URL LockPath Keylight**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.keylightgrc.com/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company name>.keylightgrc.com`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique LockPath Keylight](https://www.lockpath.com/contact/). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Certificat (brut)**, puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_general_400.png)
    
6. Dans la section **Configuration de LockPath Keylight**, cliquez sur **Configurer LockPath Keylight** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_configure.png) 

7. Pour activer l’authentification unique dans LockPath Keylight, procédez comme suit :
   
    a. Connectez-vous à votre compte LockPath Keylight en tant qu’administrateur.
    
    b. Dans le menu situé en haut, cliquez sur **Person** (Personne), puis sélectionnez **Keylight Setup** (Installation de Keylight).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. Dans l’arborescence sur la gauche, cliquez sur **SAML**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. Dans la boîte de dialogue **SAML Settings** (Paramètres SAML), cliquez sur **Edit** (Modifier).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/404.png) 

8. Dans la page de boîte de dialogue **Edit SAML Settings** (Modifier les paramètres SAML), procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    a. Affectez à **SAML authentication** (Authentication SAML) la valeur **Active**.

    b. Collez la valeur **URL du service d’authentification unique SAML** copiée dans le portail Azure dans la zone de texte **Identity Provider Login URL (URL de connexion du fournisseur d’identité)**.

    c. Collez la valeur **URL du service d’authentification unique** copiée dans le portail Azure dans la zone de texte **Identity Provider Logout URL (URL de déconnexion du fournisseur d’identité)**.

    d. Cliquez sur **Choose File** (Choisir un fichier) pour sélectionner votre certificat LockPath Keylight téléchargé, puis sur **Ouvrir** pour charger le certificat.

    e. Affectez à **SAML User Id location** (Emplacement de l’ID utilisateur SAML) la valeur **NameIdentifier element of the subject statement** (Élément NameIdentifier de l’instruction Subject).
    
    f. Indiquez la valeur **Keylight Service Provider** (Fournisseur de service Keylight) au format suivant : **https://&lt;Nom société&gt;.keylightgrc.com**.
    
    g. Affectez à **Auto-provision users** (Configuration automatique des utilisateurs) la valeur **Active**.

    h. Affectez à **Auto-provision account type** (Configuration automatique du type de compte) la valeur **Full User** (Utilisateur global).

    i. Définissez **Auto-provision security role** (Configuration automatique du rôle de sécurité), sélectionnez **Standard User with SAML** (Utilisateur standard avec SAML).
    
    j. Définissez **Auto-provision security config** (Configuration automatique des paramètres de sécurité), sélectionnez **Standard User Configuration** (Configuration utilisateur standard).
     
    k. Dans la zone de texte **Email Attribute** (Attribut d’e-mail), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. Dans la zone de texte **First name attribute** (Attribut de prénom), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. Dans la zone de texte **Last name attribute** (Attribut de nom), entrez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Création d’un utilisateur de test LockPath Keylight

Dans cette section, vous allez créer un utilisateur nommé Britta Simon dans LockPath Keylight. LockPath Keylight prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors de l’accès à LockPath Keylight si l’utilisateur n’existe pas encore. 

>[!NOTE]
>Si vous devez créer un utilisateur manuellement, contactez l’[équipe de support technique LockPath Keylight](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LockPath Keylight.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à LockPath Keylight, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **LockPath Keylight**.

    ![Configurer l’authentification unique](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette LockPath Keylight dans le volet d’accès, vous devez être connecté automatiquement à votre application LockPath Keylight. 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png

