---
title: "Didacticiel : Intégration d’Azure Active Directory à OpenAthens | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et OpenAthens."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: af26e007c953c4157f5ee7a4251a52e9c45a6eac
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Didacticiel : Intégration d’Azure Active Directory à OpenAthens

Dans ce didacticiel, vous allez apprendre à intégrer OpenAthens dans Azure Active Directory (Azure AD).

L’intégration d’OpenAthens dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à OpenAthens.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à OpenAthens (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à OpenAthens, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement OpenAthens pour lequel l’authentification unique est activée

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous ne disposez pas d’un environnement d’essai Azure AD, vous pouvez [obtenir un essai gratuit d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’OpenAthens à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-openathens-from-the-gallery"></a>Ajout d’OpenAthens à partir de la galerie
Pour configurer l’intégration d’OpenAthens à Azure AD, vous devez ajouter OpenAthens à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter OpenAthens à partir de la galerie**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**, puis à **Toutes les applications**.

    ![Volet Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **OpenAthens**, sélectionnez **OpenAthens** dans le volet de résultats, puis cliquez sur le bouton **Ajouter**.

    ![OpenAthens dans la liste des résultats](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès d’OpenAthens avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur OpenAthens équivalent dans Azure AD. En d’autres termes, vous devez établir une relation entre l’utilisateur Azure AD et l’utilisateur OpenAthens associé.

Dans OpenAthens, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD auprès d’OpenAthens, vous devez suivre les indications des sections suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test OpenAthens](#create-a-openathens-test-user) pour avoir un équivalent de Britta Simon dans OpenAthens lié à la représentation Azure AD associée.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application OpenAthens.

**Pour configurer l’authentification unique Azure AD avec OpenAthens**

1. Dans le portail Azure, dans la page d’intégration de l’application **OpenAthens**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Pour activer l’authentification unique, dans la boîte de dialogue **Authentification unique**, sélectionnez **Authentification basée sur SAML** en tant que **Mode**.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. Dans la section **Domaine et URL OpenAthens**, entrez la valeur `https://login.openathens.net/saml/2/metadata-sp` dans la zone de texte **Identificateur**.

    ![Informations d’authentification unique dans Domaine et URL OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. Dans la section **Certificat de signature SAML**, sélectionnez **Métadonnées XML**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat de signature SAML](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. Sélectionnez le bouton **Enregistrer**.

    ![Bouton Enregistrer de la page Authentification unique](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise OpenAthens en tant qu’administrateur.

7. Sélectionnez **Connexions** dans la liste sous l’onglet **Gestion**. 

    ![Configurer l’authentification unique](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. Sélectionnez **SAML 1.1/2.0**, puis le bouton **Configurer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. Pour ajouter la configuration, sélectionnez le bouton **Parcourir** pour charger le fichier .xml de métadonnées que vous avez téléchargé à partir du portail Azure, puis sélectionnez **Ajouter**.

    ![Configurer l’authentification unique](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. Effectuez les étapes suivantes sous l’onglet **Détails**.

    ![Configurer l’authentification unique](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. Dans **Display name mapping** (Mappage de nom d’affichage), sélectionnez **Use Attribute** (Utiliser l’attribut).

    b. Dans la zone de texte **Display name attribute** (Attribut de nom d’affichage), entrez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. Dans **Unique user mapping** (Mappage d’utilisateur unique), sélectionnez **Use Attribute** (Utiliser l’attribut).

    d. Dans la zone de texte **Unique user attribute** (Attribut d’utilisateur unique), entrez la valeur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. Dans **État**, cochez les trois cases.

    f. Dans **Create local accounts** (Créer des comptes locaux), sélectionnez **automatically** (Automatiquement).

    g. Sélectionnez **Enregistrer les modifications**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez la [documentation incorporée d’Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé « Britta Simon » dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD**

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, saisissez l’adresse e-mail de Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans la zone de texte **Mot de passe**.

    d. Sélectionnez **Créer**.
  
### <a name="create-an-openathens-test-user"></a>Créer un utilisateur de test OpenAthens

OpenAthens prend en charge l’approvisionnement juste-à-temps et les utilisateurs sont créés automatiquement après une authentification réussie. Vous n’avez pas besoin d’effectuer des opérations dans cette section.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous permettez à Britta Simon d’utiliser l’authentification unique Azure en lui accordant l’accès à OpenAthens.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à OpenAthens**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste **Applications**, sélectionnez **OpenAthens**.

    ![Lien OpenAthens dans la liste des applications](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans le volet **Ajouter une attribution**.

    ![Volet Ajouter une attribution][203]

5. Dans la liste **Utilisateurs et groupes**, sélectionnez **Britta Simon**.

6. Dans la liste **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**.

7. Dans le volet **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous sélectionnez la vignette **OpenAthens** dans le volet d’accès, vous devez être automatiquement connecté à votre application OpenAthens.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* Vous trouverez une liste de didacticiels sur l’intégration des applications SaaS à Azure Active Directory à la page [Intégration des applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md).
* Pour en savoir plus sur l’accès aux applications et l’authentification unique avec Azure Active Directory, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png

