---
title: "Didacticiel : Intégration d’Azure Active Directory avec Pingboard | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Pingboard."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: 733eff519f75368fd78824deea56f63940c0b49f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Didacticiel : Intégration d’Azure Active Directory avec Pingboard

Ce didacticiel explique comment intégrer Pingboard avec Azure Active Directory (Azure AD).

L’intégration de Pingboard avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Pingboard
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Pingboard (via l’authentification unique) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Pingboard, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Pingboard pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Pingboard à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-pingboard-from-the-gallery"></a>Ajout de Pingboard à partir de la galerie
Pour configurer l’intégration de Pingboard avec Azure AD, vous devez ajouter Pingboard, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Pingboard à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications d’entreprise][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Pingboard**, sélectionnez **Pingboard** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Pingboard dans la liste des résultats](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Pingboard sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Pingboard équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre l’utilisateur Azure AD et l’utilisateur Pingboard associé.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Pingboard.

Pour configurer et tester l’authentification unique Azure AD avec Pingboard, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Pingboard](#create-a-pingboard-test-user)** - pour avoir un équivalent de Britta Simon dans Pingboard lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Pingboard.

**Pour configurer l’authentification unique Azure AD auprès de Pingboard, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Pingboard**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2.  Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. Dans la section **Domaines et URL Pingboard**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP**:

    ![DIP d’informations d’authentification unique dans Domaine et URL Pingboard](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_url.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur `http://app.pingboard.com/sp`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<entity-id>.pingboard.com/auth/saml/consume`

4. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode lancé par le **fournisseur de service** :

    ![Fournisseurs de service d’informations d’authentification unique dans Domaine et URL Pingboard](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     Dans la zone de texte **URL de connexion**, tapez l’URL au format suivant : `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE] 
    > Notez que ces valeurs ne sont pas réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe de support technique Pingboard](https://support.pingboard.com/).

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Métadonnées XML de Pingboard](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/tutorial_general_400.png)

7. Pour configurer l’authentification unique côté Pingboard, ouvrez une nouvelle fenêtre de navigateur et connectez-vous à votre compte Pingboard. Pour configurer l’authentification unique, vous devez être un administrateur Pingboard.

8. Dans le menu supérieur, sélectionnez **Applications > Intégrations**

    ![Configurer l’authentification unique](./media/active-directory-saas-pingboard-tutorial/Pingboard_integration.png)

9.  Sur la page **Intégrations**, recherchez la mosaïque **« Azure Active Directory »**, puis cliquez dessus.

    ![Intégration de l’authentification unique Pingboard](./media/active-directory-saas-pingboard-tutorial/Pingboard_aad.png)

10. Dans la fenêtre modale qui suit, cliquez sur **« Configurer »**

    ![Bouton de configuration Pingboard](./media/active-directory-saas-pingboard-tutorial/Pingboard_configure.png)

11. Sur la page suivante, vous remarquez que « l’intégration Azure SSO est activée ». Ouvrez le fichier XML de métadonnées téléchargé dans un bloc-notes puis collez le contenu dans **IDP Metadata** (métadonnées du fournisseur d’identité).

    ![Écran de configuration SSO de Pingboard](./media/active-directory-saas-pingboard-tutorial/Pingboard_sso_configure.png)

12. Le fichier est validé et, si tout est correct, l’authentification unique est alors activée.

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-pingboard-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-pingboard-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Bouton Ajouter](./media/active-directory-saas-pingboard-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-pingboard-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-pingboard-test-user"></a>Créer un utilisateur de test Pingboard

Pour permettre aux utilisateurs Azure AD de se connecter à Pingboard, vous devez les approvisionner dans Pingboard. Pour Pingboard, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site d’entreprise Pingboard en tant qu’administrateur.

2. Cliquez sur le bouton **« Ajouter un employé »** sur la page **Annuaire**.

    ![Ajouter un employé](./media/active-directory-saas-pingboard-tutorial/create_testuser_add.png)

3. Dans la boîte de dialogue **Ajouter un employé** , procédez comme suit :

    ![Inviter des personnes](./media/active-directory-saas-pingboard-tutorial/create_testuser_name.png)

    a. Dans la zone de texte **Nom complet**, tapez le nom complet d’un utilisateur, par exemple, **Britta Simon**.

    b. Dans la zone de texte **E-mail**, tapez l’adresse e-mail d’un utilisateur, par exemple, **brittasimon@contoso.com**.

    c. Dans la zone de texte **Poste**, tapez le poste de Britta Simon.

    d. Dans la liste déroulante **Emplacement**, sélectionnez l’emplacement de Britta Simon.
    
    e. Cliquez sur **Add**.   

4. Un écran de confirmation s’affiche pour confirmer l’ajout de l’utilisateur.
    
    ![Confirmer](./media/active-directory-saas-pingboard-tutorial/create_testuser_confirm.png)
        
    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Pingboard.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Pingboard, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Pingboard**.

    ![Lien correspondant à Pingboard dans la liste Applications](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

Lorsque vous cliquez sur la mosaïque Pingboard dans le volet d’accès, vous devez être connecté automatiquement à votre application Pingboard.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_203.png
