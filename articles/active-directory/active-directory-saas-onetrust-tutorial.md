---
title: "Didacticiel : Intégration d’Azure Active Directory avec OneTrust Privacy Management Software | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et OneTrust Privacy Management Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: jeedes
ms.openlocfilehash: 89d657e754f6908a3afbb615a2158c9d7f3380f9
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Didacticiel : Intégration d’Azure Active Directory avec OneTrust Privacy Management Software

Dans ce didacticiel, vous allez apprendre à intégrer OneTrust Privacy Management Software à Azure Active Directory (Azure AD).

L’intégration de OneTrust Privacy Management Software à Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à OneTrust Privacy Management Software.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à OneTrust Privacy Management Software (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec OneTrust Privacy Management Software, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement OneTrust Privacy Management Software pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de OneTrust Privacy Management Software à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Ajout de OneTrust Privacy Management Software à partir de la galerie
Pour configurer l’intégration de OneTrust Privacy Management Software à Azure AD, vous devez ajouter OneTrust Privacy Management Software, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter OneTrust Privacy Management Software à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **OneTrust Privacy Management Software**, sélectionnez **OneTrust Privacy Management Software** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![OneTrust Privacy Management Software dans la liste des résultats](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec OneTrust Privacy Management Software avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur OneTrust Privacy Management Software équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur OneTrust Privacy Management Software associé doit être établi.

Dans OneTrust Privacy Management Software, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec OneTrust Privacy Management Software, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test OneTrust Privacy Management Software](#create-a-onetrust-privacy-management-software-test-user)** pour avoir un équivalent de Britta Simon dans OneTrust Privacy Management Software qui soit lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application OneTrust Privacy Management Software.

**Pour configurer l’authentification unique Azure AD avec OneTrust Privacy Management Software, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **OneTrust Privacy Management Software**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_samlbase.png)

3. Dans la section **Domaine et URL OneTrust Privacy Management Software**, suivez les étapes ci-dessous pour configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL OneTrust Privacy Management Software](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL : `https://www.onetrust.com/saml2`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.onetrust.com/auth/consumerservice`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL OneTrust Privacy Management Software](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.onetrust.com/auth/login`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Contactez [l’équipe de support technique de OneTrust Privacy Management Software](mailto:support@onetrust.com) pour obtenir ces valeurs. 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-onetrust-tutorial/tutorial_general_400.png)

7. Pour configurer l’authentification unique côté **OneTrust Privacy Management Software**, vous devez envoyer le **XML des métadonnées** téléchargé à l’[équipe de support OneTrust Privacy Management Software](mailto:support@onetrust.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-onetrust-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-onetrust-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-onetrust-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-onetrust-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-onetrust-privacy-management-software-test-user"></a>Créer un utilisateur de test OneTrust Privacy Management Software

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans OneTrust Privacy Management Software. OneTrust Privacy Management Software prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à OneTrust Privacy Management Software s’il n’existe pas déjà.

>[!Note]
>Si vous devez créer manuellement un utilisateur, contactez l’[équipe de support OneTrust Privacy Management Software](mailto:support@onetrust.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à OneTrust Privacy Management Software.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à OneTrust Privacy Management Software, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **OneTrust Privacy Management Software**.

    ![Le lien OneTrust Privacy Management Software dans la liste des applications](./media/active-directory-saas-onetrust-tutorial/tutorial_onetrust_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette OneTrust Privacy Management Software dans le volet d’accès, vous êtes automatiquement connecté à votre application OneTrust Privacy Management Software.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-onetrust-tutorial/tutorial_general_203.png

