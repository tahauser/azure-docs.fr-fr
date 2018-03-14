---
title: "Didacticiel : Intégration d’Azure Active Directory à Carlson Wagonlit Travel | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Carlson Wagonlit Travel."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2745e165-94ab-43b1-970a-4547b4e5b501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: jeedes
ms.openlocfilehash: 26d141d73dfee5dd47ba09a32262343488487ad1
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2018
---
# <a name="tutorial-azure-active-directory-integration-with-carlson-wagonlit-travel"></a>Didacticiel : Intégration d’Azure Active Directory à Carlson Wagonlit Travel

Dans ce didacticiel, vous allez apprendre à intégrer Carlson Wagonlit Travel à Azure Active Directory (Azure AD).

L’intégration de Carlson Wagonlit Travel à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Carlson Wagonlit Travel.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Carlson Wagonlit Travel (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l'intégration d’Azure AD avec Carlson Wagonlit Travel, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SSO activé à Carlson Wagonlit Travel

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Carlson Wagonlit Travel depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-carlson-wagonlit-travel-from-the-gallery"></a>Ajout de Carlson Wagonlit Travel depuis la galerie
Pour configurer l’intégration de Carlson Wagonlit Travel à Azure AD, vous devez ajouter Carlson Wagonlit Travel depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Carlson Wagonlit Travel depuis la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Carlson Wagonlit Travel**, sélectionnez **Carlson Wagonlit Travel** à partir du volet de résultats, puis sur le bouton **Ajouter** pour ajouter l’application.

    ![Carlson Wagonlit Travel dans la liste des résultats](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Carlson Wagonlit Travel avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur de Carlson Wagonlit Travel équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur de Carlson Wagonlit Travel associé doit être établie.

Dans Carlson Wagonlit Travel, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur de **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Carlson Wagonlit Travel, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Carlson Wagonlit Travel](#create-a-carlson-wagonlit-travel-test-user)** pour avoir un équivalent de Britta Simon dans Carlson Wagonlit Travel, lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Carlson Wagonlit Travel.

**Pour configurer l’authentification unique Azure AD avec Carlson Wagonlit Travel, procédez comme suit :**

1. Dans le Portail Azure, dans la page d’intégration de l’application **Carlson Wagonlit Travel**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_samlbase.png)

3. Dans la section **Domaine et URL Carlson Wagonlit Travel**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Carlson Wagonlit Travel](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_url.png)

    Dans la zone de texte **Identificateur**, entrez la valeur : `cwt-stage`

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique côté **Carlson Wagonlit Travel**, vous devez envoyer les **métadonnées XML** téléchargées à [l’équipe du support technique Carlson Wagonlit Travel](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-carlsonwagonlittravel-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur**, tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-carlson-wagonlit-travel-test-user"></a>Créer un utilisateur test de Carlson Wagonlit Travel

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Carlson Wagonlit Travel. Travailler avec l’[équipe de support Carlson Wagonlit Travel](http://www.carlsonwagonlit.in/content/cwt/in/en/technical-assistance.html) pour ajouter des utilisateurs dans la plateforme Carlson Wagonlit Travel. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Carlson Wagonlit Travel.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Carlson Wagonlit Travel, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Carlson Wagonlit Travel**.

    ![Le lien Carlson Wagonlit Travel dans la liste des applications](./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_carlsonwagonlittravel_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Carlson Wagonlit Travel dans le volet d’accès, vous devez vous connecter automatiquement à votre application Carlson Wagonlit Travel.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-carlsonwagonlittravel-tutorial/tutorial_general_203.png

