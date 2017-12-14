---
title: "Tutoriel : intégration d’Azure Active Directory à LCVista | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et LCVista."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8db80d6e-3275-419f-aa39-6115a7bc9800
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2017
ms.author: jeedes
ms.openlocfilehash: 367e1416fb6c1d1b710c7e47240951ee46cf9f5b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-lcvista"></a>Tutoriel : Intégration d’Azure Active Directory à LCVista

Dans ce tutoriel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) dans LCVista.

L’intégration de LCVista dans Azure AD offre les avantages suivants :

- Azure AD vous permet de contrôler l’accès à LCVista
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à LCVista (via l'authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l'intégration d'Azure AD avec LCVista, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement LCVista pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de LCVista à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-lcvista-from-the-gallery"></a>Ajout de LCVista à partir de la galerie
Pour configurer l'intégration de LCVista avec Azure AD, vous devez ajouter LCVista disponible dans la galerie, à votre liste d'applications SaaS gérées.

**Pour ajouter LCVista à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **LCVista**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_search.png)

5. Dans le panneau des résultats, sélectionnez **LCVista**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec LCVista, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur LCVista équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur LCVista associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** (NomUtilisateur) dans LCVista.

Pour configurer et tester l’authentification unique Azure AD avec LCVista, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test LCVista](#creating-a-lcvista-test-user)** pour avoir un équivalent de Britta Simon dans LCVista lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application LCVista.

**Pour configurer l’authentification unique Azure AD avec LCVista, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **LCVista**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_samlbase.png)

3. Dans la section **Domaine et URL LCVista**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.lcvista.com/rainier/login`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.lcvista.com` 
     
    > [!NOTE] 
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique LCVista](https://lcvista.com/contact). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-lcvista-tutorial/tutorial_general_400.png)
    
6. Dans la section **Configuration de LCVista**, cliquez sur **Configurer LCVista** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’ID d’entité SAML** et **l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_configure.png) 

7.  Connectez-vous à votre application LCVista en tant qu’administrateur.

8. Dans la section **SAML Config** (Configuration SAML), cochez la case **Enable SAML login** (Activer la connexion SAML), puis entrez les informations détaillées, comme indiqué dans l’image ci-dessous. 

    ![Configurer l’authentification unique](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_config.png)

    a. Collez l’**URL de l’émetteur** que vous avez copiée à partir d’Azure AD dans la section **ID d’entité**. 

    b. Collez l’**URL du service d’authentification unique** que vous avez copiée à partir d’Azure AD dans la section **URL**.

    c. Dans le fichier de métadonnées (XML) que vous avez téléchargé à partir du portail Azure, copiez la valeur de l’objet **X509Certificate** et collez-la dans la section **Certificat x509**.

    d. Dans la zone de texte **First Name Attribute** (Attribut de prénom) entrez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Dans la zone de texte **Last name attribute** (Attribut de nom), collez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Dans la zone de texte **Email attribute** (Attribut d’email), collez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Dans la zone de texte **Username attribute** (Attribut de nom d’utilisateur), collez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Cliquez sur **Enregistrer** pour enregistrer les paramètres.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lcvista-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lcvista-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lcvista-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-lcvista-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-lcvista-test-user"></a>Création d’un utilisateur de test LCVista

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans LCVista. Vous devez contactez l’[équipe de support technique LCVista](https://lcvista.com/contact) pour ajouter des utilisateurs dans l’application LCVista. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LCVista.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à LCVista, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **LCVista**.

    ![Configurer l’authentification unique](./media/active-directory-saas-lcvista-tutorial/tutorial_lcvista_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Cliquez sur la vignette LCVista dans le volet d’accès. Vous êtes redirigé vers la page Organization sign on (Authentification d’organisation). Une fois authentifié, vous êtes connecté à votre application LCVista. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-lcvista-tutorial/tutorial_general_203.png

