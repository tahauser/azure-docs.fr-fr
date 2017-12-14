---
title: "Didacticiel : Intégration de Azure Active Directory à Schoox | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Schoox."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: f8b4cdcc-cbf6-4229-9427-05632e33f942
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: jeedes
ms.openlocfilehash: 6b23b7d4f62994c81de211823822192956c8df84
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-schoox"></a>Didacticiel : Intégration de Azure AD avec Schoox

Dans ce didacticiel, vous allez apprendre à intégrer Schoox à Azure Active Directory (Azure AD).

L’intégration de Schoox dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Schoox.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Schoox (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration de Azure AD à Schoox, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Schoox pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Schoox à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-schoox-from-the-gallery"></a>Ajout de Schoox à partir de la galerie
Pour configurer l’intégration de Schoox à Azure AD, vous devez ajouter Schoox à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Schoox à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Schoox**, sélectionnez **Schoox** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Schoox dans la liste des résultats](./media/active-directory-saas-schoox-tutorial/tutorial_schoox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Schoox, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Schoox équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Schoox associé doit être établie.

Dans Schoox, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Schoox, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Schoox](#create-a-schoox-test-user)** pour avoir un équivalent de Britta Simon dans Schoox lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Schoox.

**Pour configurer l’authentification unique Azure AD avec Schoox, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Schoox**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-schoox-tutorial/tutorial_schoox_samlbase.png)

3. Dans la section **Domaines et URL Schoox**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Schoox](./media/active-directory-saas-schoox-tutorial/tutorial_schoox_url.png)

    Dans la zone de texte **Identificateur**, tapez une URL : `https://saml.schoox.com/saml/adfsmetadata`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Schoox](./media/active-directory-saas-schoox-tutorial/tutorial_schoox_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://saml.schoox.com/saml/login?idpUrl=<entityID>`
    
    > [!Note]
    > `<entityID>` est l’ID d’entité SAML copié à partir de la section de référence rapide, décrite plus loin dans le didacticiel. 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-schoox-tutorial/tutorial_schoox_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-schoox-tutorial/tutorial_general_400.png)

7. Dans la section **Configuration de Schoox** , cliquez sur **Configurer Schoox** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML** à partir de la **section de référence rapide** et utilisez-le pour compléter l’**URL de connexion** dans la section **Domaine et URL Schoox**. 

    ![Configuration de Schoox](./media/active-directory-saas-schoox-tutorial/config.png)

8. Pour configurer l’authentification unique côté **Schoox**, vous devez envoyer le fichier **XML des métadonnées** téléchargé à [l’équipe de support Schoox](https://www.schoox.com/help/). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-schoox-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-schoox-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-schoox-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-schoox-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-schoox-test-user"></a>Créer un utilisateur de test Schoox

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Schoox. Collaborez avec l’[équipe du support technique Schoox](https://www.schoox.com/help/) pour ajouter des utilisateurs dans la plate-forme Schoox. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Schoox.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Schoox, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Schoox**.

    ![Lien Schoox dans la liste des applications](./media/active-directory-saas-schoox-tutorial/tutorial_schoox_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Schoox dans le volet d’accès, vous devez être connecté automatiquement à votre application Schoox.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-schoox-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-schoox-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-schoox-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-schoox-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-schoox-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-schoox-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-schoox-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-schoox-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-schoox-tutorial/tutorial_general_203.png

