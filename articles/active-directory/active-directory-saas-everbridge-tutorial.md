---
title: "Didacticiel : Intégration d’Azure Active Directory à EverBridge | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et EverBridge."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 493705359186dc9d27e01d0d5fae65829a8af834
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Didacticiel : Intégration d’Azure Active Directory à Everbridge

Dans ce didacticiel, vous allez apprendre à intégrer EverBridge à Azure Active Directory (Azure AD).

L’intégration d’EverBridge à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à EverBridge
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à EverBridge (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à EverBridge, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement EverBridge pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’EverBridge à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-everbridge-from-the-gallery"></a>Ajout d’EverBridge à partir de la galerie
Pour configurer l’intégration d’EverBridge à Azure AD, vous devez ajouter EverBridge à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter EverBridge à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **EverBridge**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_search.png)

5. Dans le panneau de résultats, sélectionnez **EverBridge**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec EverBridge à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur EverBridge équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur EverBridge associé doit être établie.

Dans EverBridge, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec EverBridge, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test EverBridge](#creating-an-everbridge-test-user)** pour avoir un équivalent de Britta Simon dans EverBridge lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application EverBridge.

**Pour configurer l’authentification unique Azure AD avec EverBridge, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **EverBridge**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_samlbase.png)

3. Dans la section **EverBridge Domain and URLs** (Domaine et URL EverBridge), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://sso.everbridge.net/<companyname>`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://manager.everbridge.net/saml/SSO/<companyname>/alias/defaultAlias`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Contactez [l’équipe de support technique EverBridge](mailto:support@everbridge.com) pour obtenir ces valeurs.
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_general_400.png)

6. Dans la section **EverBridge Configuration** (Configuration d’EverBridge), cliquez sur **Configure EverBridge** (Configurer EverBridge) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_configure.png) 

6. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire Everbridge en tant qu’administrateur.

7. Dans le menu situé en haut, cliquez sur l’onglet **Paramètres** et sélectionnez **Authentification unique** sous **Sécurité**.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. Dans la zone de texte **Nom**, entrez le nom du fournisseur d’identificateurs (par exemple : le nom de votre entreprise).
   
    b. Dans la zone de texte **nom de l’API** , saisissez le nom de l’API.
   
    c. Cliquez sur le bouton **Choisir un fichier** pour charger le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.
   
    d. Dans SAML Identity Location (Emplacement de l’identité SAML), sélectionnez **Identity is in the NameIdentifier element of the Subject statement** (L’identité figure dans l’élément NameIdentifier de l’instruction Subject).
   
    e. Dans la zone de texte **URL du fournisseur d’identité**, collez la valeur de l’URL SSO SAML depuis Azure AD.
   
    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_003.png)
   
    f. Dans Liaison de demande initiée par le fournisseur de service, sélectionnez **Redirection HTTP**.

    g. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-everbridge-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-everbridge-test-user"></a>Création d’un utilisateur de test EverBridge

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Everbridge. Travaillez avec [l’équipe de support technique EverBridge](mailto:support@everbridge.com) pour ajouter les utilisateurs à la plateforme EverBridge.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à EverBridge.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à EverBridge, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **EverBridge**.

    ![Configurer l’authentification unique](./media/active-directory-saas-everbridge-tutorial/tutorial_everbridge_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Everbridge dans le volet d’accès, vous devez être connecté automatiquement à votre application Everbridge.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-everbridge-tutorial/tutorial_general_203.png

