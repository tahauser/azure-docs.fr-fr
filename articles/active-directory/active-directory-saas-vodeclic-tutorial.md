---
title: "Didacticiel : Intégration d’Azure Active Directory à Vodeclic | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Vodeclic."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 940c7bb5040fb91a03b01dc43ee07d52e3d4e63b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>Didacticiel : Intégration d’Azure Active Directory à Vodeclic

Dans ce didacticiel, vous allez apprendre à intégrer Vodeclic à Azure Active Directory (Azure AD).

L’intégration de Vodeclic dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Vodeclic.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Vodeclic (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Vodeclic, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Vodeclic pour lequel l’authentification unique (SSO) est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, obtenez [un essai gratuit d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Vodeclic à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-vodeclic-from-the-gallery"></a>Ajouter Vodeclic à partir de la galerie
Pour configurer l’intégration de Vodeclic à Azure AD, vous devez ajouter Vodeclic à votre liste d’applications SaaS gérées, à partir de la galerie.

**Pour ajouter Vodeclic à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, entrez **Vodeclic**. Sélectionnez **Vodeclic** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Vodeclic dans la liste des résultats](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec Vodeclic, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Vodeclic équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Vodeclic associé.

Dans Vodeclic, donnez à la valeur **Username** la même valeur que **Nom d’utilisateur** dans Azure AD. Vous avez maintenant établi le lien entre les deux utilisateurs.

Pour configurer et tester l’authentification unique Azure AD avec Vodeclic, suivez les indications des sections suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test Vodeclic](#create-a-vodeclic-test-user) pour avoir un équivalent de Britta Simon dans Vodeclic lié à la représentation Azure AD de l’utilisateur.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Vodeclic.

**Pour configurer l’authentification unique Azure AD avec Vodeclic, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Vodeclic**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, sous **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. Si vous souhaitez configurer l’application en mode initié par **IDP**, dans la section **Domaines et URL Vodeclic**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaines et URL Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. Dans la zone de texte **Identificateur**, entrez une URL au format suivant : `https://<companyname>.lms.vodeclic.net/auth/saml`

    b. Dans la zone **URL de réponse**, tapez une URL au format suivant : `https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. Si vous voulez configurer l’application en mode initié par le **fournisseur de services**, cochez la case **Afficher les paramètres d’URL avancés**, puis effectuez l’étape suivante :

    ![Informations d’authentification unique dans Domaines et URL Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    Dans la zone **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Vodeclic](mailto:hotline@vodeclic.com).

5. Dans la section **Certificat de signature SAML**, sélectionnez **XML des métadonnées**. Ensuite, enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. Pour configurer l’authentification unique côté **Vodeclic**, envoyez le **XML de métadonnées** téléchargé à [l’équipe de support technique de Vodeclic](mailto:hotline@vodeclic.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée en consultant la [documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :**

1. Dans le volet gauche du portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**. Puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-vodeclic-test-user"></a>Créer un utilisateur de test Vodeclic

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Vodeclic. Collaborez avec [l’équipe du support technique Vodeclic](mailto:hotline@vodeclic.com) pour ajouter des utilisateurs dans la plateforme Vodeclic. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

> [!NOTE]
> En fonction des besoins de l’application, vous devrez peut-être faire figurer votre ordinateur dans la liste verte. Pour cela, vous devez partager votre adresse IP publique avec [l’équipe de support technique Vodeclic](mailto:hotline@vodeclic.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Vodeclic.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Vodeclic, effectuez les étapes suivantes :**

1. Sur le Portail Azure, ouvrez l’affichage des applications, puis accédez à l’affichage des répertoires. Ensuite, accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Vodeclic**.

    ![Lien Vodeclic dans la liste des applications](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**.

7. Dans la boîte de dialogue **Ajouter une attribution**, cliquez sur le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette Vodeclic dans le panneau d’accès, vous êtes connecté automatiquement à votre application Vodeclic.

Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

