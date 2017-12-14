---
title: "Didacticiel : Intégration d’Azure Active Directory à 123ContactForm | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et 123ContactForm."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 70955676e78642e6c8a6eb85f8165b327baece3f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Didacticiel : Intégration d’Azure Active Directory à 123ContactForm

Dans ce didacticiel, vous découvrez comment intégrer 123ContactForm à Azure Active Directory (Azure AD).

L’intégration de 123ContactForm à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à 123ContactForm.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à 123ContactForm (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à 123ContactForm, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement 123ContactForm pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de 123ContactForm à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-123contactform-from-the-gallery"></a>Ajout de 123ContactForm à partir de la galerie
Pour configurer l’intégration de 123ContactForm à Azure AD, vous devez ajouter 123ContactForm à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter 123ContactForm à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **123ContactForm**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_search.png)

5. Dans le panneau des résultats, sélectionnez **123ContactForm** puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec 123ContactForm pour un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur 123ContactForm équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur 123ContactForm associé doit être établie.

Dans 123ContactForm, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec 123ContactForm, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test 123ContactForm](#creating-a-123contactform-test-user)** : pour avoir un équivalent de Britta Simon dans 123ContactForm lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et vous configurez l’authentification unique dans votre application 123ContactForm.

**Pour configurer l’authentification unique Azure AD avec 123ContactForm, effectuez les étapes suivantes :**

1. Dans le portail Azure, sur la page d’intégration de l’application **123ContactForm**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_samlbase.png)

3. Dans la section **Domaines et URL 123ContactForm**, si vous voulez configurer l’application en **mode lancé par le fournisseur d’identité**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-123contactform-tutorial/url1.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

4. Si vous voulez configurer l’application en **mode lancé par le fournisseur de service**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-123contactform-tutorial/url2.png)

    a. Cliquez sur l’option **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **URL d’authentification**, entrez l’URL comme ceci : `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Vous devez changer cette valeur pour les URL et l’identificateur réels. Ceci est expliqué plus loin dans le didacticiel.
    
5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-123contactform-tutorial/tutorial_general_400.png)

7. Pour configurer l’authentification unique du côté **123ContactForm**, accédez à [https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/) et effectuez les opérations suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-123contactform-tutorial/submit.png) 

    a. Dans la zone de texte **E-mail**, entrez l’e-mail de l’utilisateur, c’est-à-dire **BrittaSimon@Contoso.com**.

    b. Cliquez sur **Charger** et accédez au fichier XML de métadonnées que vous avez téléchargé à partir du portail Azure.

    c. Cliquez sur **Envoyer le formulaire**.

8. Dans **Microsoft Azure AD - Authentification unique - Configurer les paramètres de l’application**, procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-123contactform-tutorial/url3.png)

    a. Si vous voulez configurer l’application en **mode lancé par le fournisseur d’identité**, copiez la valeur de **IDENTIFICATEUR** pour votre instance et collez-la dans la zone de texte **Identificateur** de la section **Domaine et URL 123ContactForm** sur le portail Azure.
    
    b. Si vous voulez configurer l’application en **mode lancé par le fournisseur d’identité**, copiez la valeur de **URL DE RÉPONSE** pour votre instance et collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL 123ContactForm** sur le portail Azure.

    c. Si vous voulez configurer l’application en **mode lancé par le fournisseur de service**, copiez la valeur de **URL DE CONNEXION** pour votre instance et collez-la dans la zone de texte **URL de connexion** de la section **Domaine et URL 123ContactForm** sur le portail Azure.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-123contactform-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-123contactform-test-user"></a>Création d’un utilisateur de test 123ContactForm

L’application prend en charge la configuration d’utilisateur juste-à-temps, et après authentification, les utilisateurs sont créés automatiquement dans l’application.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à 123ContactForm.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à 123ContactForm, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **123ContactForm**.

    ![Configurer l’authentification unique](./media/active-directory-saas-123contactform-tutorial/tutorial_123contactform_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette 123ContactForm dans le panneau d’accès, vous devez être connecté automatiquement à votre application 123ContactForm.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-123contactform-tutorial/tutorial_general_203.png

