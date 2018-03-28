---
title: 'Didacticiel : Intégrer Azure Active Directory dans Box | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Box.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: 638ae63057df00375b05a58e3ceab510e2a608de
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="integrate-azure-active-directory-with-box"></a>Intégrer Azure Active Directory dans Box

Dans ce didacticiel, vous allez apprendre à intégrer Azure Active Directory (Azure AD) dans Box.

L’intégration d’Azure AD dans Box vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Box.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement àBox (par le biais de l’authentification unique, ou SSO) avec leurs comptes Azure AD.
- Vous pouvez centraliser la gestion de vos comptes à un seul emplacement : le Portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS dans Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à Box, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Box dans lequel l’authentification unique (SSO) est incluse

> [!NOTE]
> Lorsque vous testez les étapes de ce didacticiel, nous *déconseillons* l’utilisation d’un environnement de production.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Box à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-box-from-the-gallery"></a>Ajouter Box à partir de la galerie
Pour configurer l’intégration d’Azure AD dans Box, ajoutez Box à partir de la galerie à votre liste d’applications SaaS gérées en procédant comme suit :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Fenêtre « Applications d’entreprise »][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la fenêtre.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Box**, sélectionnez **Box** dans la liste des résultats, puis sélectionnez **Ajouter**.

    ![Box dans la liste des résultats](./media/active-directory-saas-box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Box, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit identifier l’utilisateur Box et son équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et le même utilisateur dans Box doit être établie.

Pour établir la relation, affectez la valeur du *nom d’utilisateur* dans Azure AD au *Nom d’utilisateur* dans Box.

Pour configurer et tester l’authentification unique Azure AD avec Box, suivez les indications des sections dans les cinq sections suivantes.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Activez l’authentification unique Azure AD dans le portail Azure, et configurez l’authentification unique dans votre application Box en procédant comme suit :

1. Dans le portail Azure, dans la fenêtre d’intégration de l’application **Box**, sélectionnez **Authentification unique**.

    ![Lien « Authentification unique »][4]

2. Dans la zone **Mode d’authentification unique** de la fenêtre **Authentification unique**, sélectionnez **Authentification SAML**.
 
    ![Fenêtre « Authentification unique »](./media/active-directory-saas-box-tutorial/tutorial_box_samlbase.png)

3. Sous **Domaine et URL Box**, effectuez les actions suivantes :

    ![Informations d’authentification unique dans « Domaine et URL Box »](./media/active-directory-saas-box-tutorial/url3.png)

    a. Dans la zone **URL de connexion**, tapez une URL au format suivant : *https://\<subdomain>.box.com*.

    b. Dans la zone de texte **Identificateur**, tapez **box.et**.
     
    > [!NOTE] 
    > Les valeurs ci-dessus ne sont pas réelles. Mettez-les à jour avec l’URL de connexion et l’identificateur réels. Pour obtenir les valeurs, contactez l’[équipe de support client Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

4. Sous **Certificat de signature SAML**, sélectionnez **XML de métadonnées**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-box-tutorial/tutorial_box_certificate.png) 

5. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-box-tutorial/tutorial_general_400.png)
    
6. Pour configurer l’authentification unique pour votre application, suivez la procédure décrite dans [Configurer vous-même l’authentification unique](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Si vous ne pouvez pas activer les paramètres d’authentification unique pour votre compte Box, vous pouvez avoir besoin de contacter l’[équipe de support client Box](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) et de lui fournir le fichier XML téléchargé.

> [!TIP]
> Au moment de configurer l’application, vous pouvez lire une version abrégée des instructions précédentes sur le [Portail Azure](https://portal.azure.com). Après avoir ajouté l’application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique**, puis accédez à la documentation intégrée dans la section **Configuration** en bas. Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez la [documentation incorporée d’Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Azure en procédant comme suit :

![Créer un utilisateur de test Azure AD][100]

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**.

    ![Lien Azure Active Directory](./media/active-directory-saas-box-tutorial/create_aaduser_01.png)

2. Pour afficher une liste des utilisateurs actuels, sélectionnez **Utilisateurs et groupes** > **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-box-tutorial/create_aaduser_02.png)

3. En haut de la fenêtre **Tous les utilisateurs**, sélectionnez **Ajouter**.

    ![Bouton Ajouter](./media/active-directory-saas-box-tutorial/create_aaduser_03.png)

    La fenêtre **Utilisateur** s’ouvre.

4. Dans la fenêtre **Utilisateur**, suivez les étapes ci-dessous :

    ![Fenêtre Utilisateur](./media/active-directory-saas-box-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-box-test-user"></a>Créer un utilisateur de test Box

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans Box. Box prend en charge l’approvisionnement juste-à-temps, option activée par défaut. S’il n’y a pas déjà un utilisateur, un nouvel utilisateur est créé lorsque vous tentez d’y accéder. Aucune action n’est nécessaire pour créer l’utilisateur.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisatrice Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Box. Pour ce faire, procédez comme suit :

![Attribuer le rôle utilisateur][200]

1. Sur le Portail Azure, ouvrez la vue **Applications**, accédez à la vue **Répertoire**, puis sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Liens « Applications d’entreprise » et « Toutes les applications »][201] 

2. Dans la liste **Applications**, sélectionnez **Box**.

    ![Le lien de Box](./media/active-directory-saas-box-tutorial/tutorial_box_app.png)  

3. Dans le volet gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez **Ajouter** puis, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la liste **Utilisateurs** de la fenêtre **Utilisateurs et groupes**, sélectionnez **Britta Simon**.

6. Cliquez sur le bouton **Sélectionner**.

7. Dans la fenêtre **Ajouter une affectation**, sélectionnez **Affecter**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous sélectionnez la mosaïque **Box** dans le volet d’accès, vous ouvrez la page de connexion pour vous connecter à votre application Box.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’approvisionnement des utilisateurs](active-directory-saas-box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-box-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-box-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-box-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-box-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-box-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-box-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-box-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-box-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-box-tutorial/tutorial_general_203.png

