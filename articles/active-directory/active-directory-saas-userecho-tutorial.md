---
title: "Didacticiel : Intégration d’Azure Active Directory à UserEcho | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et UserEcho."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a3e2d6263b475ca72df93f0f20874971409e4c54
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Didacticiel : Intégration d’Azure Active Directory à UserEcho

Dans ce didacticiel, vous allez apprendre à intégrer UserEcho à Azure Active Directory (Azure AD).

L’intégration d’UserEcho avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à UserEcho.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à UserEcho (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à UserEcho, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement UserEcho pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’UserEcho à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-userecho-from-the-gallery"></a>Ajout d’UserEcho à partir de la galerie
Pour configurer l’intégration d’UserEcho à Azure AD, vous devez ajouter UserEcho à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter UserEcho à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **UserEcho**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_search.png)

5. Dans le volet de résultats, sélectionnez **UserEcho**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec UserEcho pour un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur UserEcho équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur UserEcho associé doit être établie.

Dans UserEcho, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec UserEcho, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test UserEcho](#creating-a-userecho-test-user)** pour avoir un équivalent de Britta Simon dans UserEcho lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application UserEcho.

**Pour configurer l’authentification unique Azure AD avec UserEcho, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **UserEcho**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_samlbase.png)

3. Dans la section **Domaine et URL UserEcho**, effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.userecho.com/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de UserEcho](https://feedback.userecho.com/). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de UserEcho** , cliquez sur **Configurer UserEcho** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_configure.png) 

7. Dans une autre fenêtre de navigateur, connectez-vous à votre site d’entreprise UserEcho en tant qu’administrateur.

8. Dans la barre d’outils située en haut, cliquez sur votre nom d’utilisateur pour développer le menu, puis cliquez sur **Setup**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png) 

9. Cliquez sur **Integrations**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_07.png) 

10. Cliquez sur **Website**, puis sur **Single sign-on (SAML2)**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_08.png) 

11. Sur la page **Single sign-on (SAML)** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_09.png)
    
    a. Pour **SAML-enabled**, sélectionnez **Yes**.
    
    b. Collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure dans la zone de texte **SAML SSO URL**.
    
    c. Collez l’**URL de déconnexion** que vous avez copiée à partir du portail Azure dans la zone de texte **Remote logoout URL**.
    
    d. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, copiez son contenu, puis collez-le dans la zone de texte **Certificat X.509** .
    
    e. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-userecho-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-userecho-test-user"></a>Création d’un utilisateur de test UserEcho

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans UserEcho.

**Pour créer un utilisateur appelé Britta Simon dans UserEcho, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise UserEcho en tant qu’administrateur.

2. Dans la barre d’outils située en haut, cliquez sur votre nom d’utilisateur pour développer le menu, puis cliquez sur **Setup**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_06.png)

3. Cliquez sur **Users**, pour développer la section **Users**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_10.png)

4. Cliquez sur **Utilisateurs**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_11.png)

5. Cliquez sur **Invite a new user**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_12.png)

6. Dans la boîte de dialogue **Invite a new user** , procédez comme suit :
   
    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_13.png)

    a. Dans la zone de texte **Name**, tapez le nom complet d’un utilisateur, par exemple Britta Simon.
    
    b.  Dans la zone de texte **Email** (E-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.
    
    c. Cliquez sur **Inviter**.

Une invitation est envoyée à Britta, ce qui lui permet de commencer à utiliser UserEcho. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à UserEcho.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à UserEcho, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **UserEcho**.

    ![Configurer l’authentification unique](./media/active-directory-saas-userecho-tutorial/tutorial_userecho_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.  

Quand vous cliquez sur la vignette UserEcho dans le volet d’accès, vous devez être connecté automatiquement à votre application UserEcho.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-userecho-tutorial/tutorial_general_203.png

