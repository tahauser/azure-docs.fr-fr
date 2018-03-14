---
title: "Didacticiel : Intégration d’Azure Active Directory à vxMaintain | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et vxMaintain."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 66165b2586304f3726f5d712fb334fe67e2cd02b
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>Didacticiel : Intégration d’Azure Active Directory à vxMaintain

Dans ce didacticiel, vous allez apprendre à intégrer vxMaintain à Azure Active Directory (Azure AD).

Cette intégration offre plusieurs avantages importants. Vous pouvez :

- Contrôler qui a accès à vxMaintain dans Azure AD.
- Autoriser vos utilisateurs à se connecter automatiquement à vxMaintain par le biais de l’authentification unique, avec leur compte Azure AD.
- Gérer vos comptes à un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à vxMaintain, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement vxMaintain dans lequel l’authentification unique (SSO) est incluse

> [!NOTE]
> Lorsque vous testez les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. 

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

* Ajout de vxMaintain à partir de la galerie
* Configuration et test de l’authentification unique Azure AD

## <a name="add-vxmaintain-from-the-gallery"></a>Ajouter vxMaintain à partir de la galerie
Pour configurer l’intégration de vxMaintain dans Azure AD, vous devez ajouter vxMaintain à partir de la galerie à votre liste d’applications SaaS gérées.

Pour ajouter vxMaintain à partir de la galerie, procédez comme suit :

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), cliquez sur le bouton **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Sélectionnez **Applications d’entreprise** > **Toutes les applications**.

    ![Volet Applications d’entreprise][2]
    
3. Pour ajouter une application, dans la boîte de dialogue **Toutes les applications**, sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **vxMaintain**.

    ![Liste déroulante Mode](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_search.png)

5. Dans la liste des résultats, sélectionnez **vxMaintain**, puis **Ajouter**.

    ![Lien vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec vxMaintain au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit connaître l’équivalent de vxMaintain pour l’utilisateur Azure AD. Autrement dit, vous devez établir une relation entre l’utilisateur Azure AD et l’utilisateur vxMaintain correspondant.

Pour établir cette relation, assignez la valeur du **nom d’utilisateur** vxMaintain en tant que valeur de **nom d’utilisateur** Azure AD.

Pour configurer et tester l’authentification unique Azure AD avec vxMaintain, suivez les indications des sections ci-après :

### <a name="configure-azure-ad-sso"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique (SSO) Azure AD dans le portail Azure et la configurer dans votre application vxMaintain en procédant comme suit :

1. Dans la page d’intégration de l’application **vxMaintain** sur le portail Azure, sélectionnez **Authentification unique**.

    ![Commande Authentification unique][4]

2. Pour activer l’authentification unique, dans la liste déroulante **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Commande Authentification basée sur SAML](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

3. Sous **Domaine et URL vxMaintain**, procédez comme suit :

    ![Section Domaine et URL vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL dont la syntaxe est la suivante : `https://<company name>.verisae.com`

    b. Dans la zone de texte **URL de réponse**, tapez une URL dont la syntaxe est la suivante : `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Les valeurs ci-dessus ne sont pas réelles. Mettez-les à jour avec l’identificateur et l’URL de réponse réels. Pour obtenir les valeurs, contactez l’[équipe de support vxMaintain](http://www.verisae.com/contact-us).
 
4. Sous **Certificat de signature SAML**, sélectionnez **XML des métadonnées** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Section Certificat de signature SAML](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

5. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer](./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique **vxMaintain**, envoyez le fichier **XML des métadonnées** téléchargé à l’[équipe de support vxMaintain](http://www.verisae.com/contact-us).

> [!TIP]
> Lors de la configuration de l’application, vous pouvez lire une version abrégée des instructions précédentes sur le [portail Azure](https://portal.azure.com). Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée à partir de la section **Configuration**. 
>
>Pour en savoir plus sur la fonctionnalité de documentation incorporée, consultez [Gestion de l’authentification unique pour les applications d’entreprise](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test nommé Britta Simon dans le portail Azure en procédant comme suit :

![Utilisateur de test Azure AD][100]

1. Dans le volet gauche du **portail Azure**, cliquez sur le bouton **Azure Active Directory**.

    ![Le bouton « Azure Active Directory »](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes** > **Tous les utilisateurs**.
    
    ![Lien Tous les utilisateurs](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_02.png)  
    La boîte de dialogue **Tous les utilisateurs** s’ouvre. 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter**.
 
    ![Bouton Ajouter](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-vxmaintain-tutorial/create_aaduser_04.png) 

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur de test Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur générée dans la zone **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-vxmaintain-test-user"></a>Créer un utilisateur de test vxMaintain

Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans vxMaintain. Pour ajouter des utilisateurs dans la plateforme vxMaintain, collaborez avec l’[équipe du support technique de vxMaintain](http://www.verisae.com/contact-us). Avant d’utiliser l’authentification unique, créez et activez les utilisateurs.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur de test Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à vxMaintain. Pour ce faire, procédez comme suit :

![Utilisateur de test dans la liste Nom affiché][200] 

1. Dans la vue **Applications** du portail Azure, accédez à la vue **Répertoire** >**Applications d’entreprise** > **Toutes les applications**.

    ![Lien Toutes les applications][201] 

2. Dans la liste **Applications**, sélectionnez **vxMaintain**.

    ![Lien vxMaintain](./media/active-directory-saas-vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

3. Dans le volet gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Sélectionnez **Ajouter** puis, dans le volet **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes** de la liste **Utilisateurs**, sélectionnez **Britta Simon**, puis sélectionnez le bouton **Sélectionner**.

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Affecter**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Tester votre authentification unique Azure AD

Dans cette section, vous allez tester la configuration SSO Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette **vxMaintain** dans le panneau d’accès, vous devez être connecté automatiquement à votre application vxMaintain.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>étapes suivantes

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vxmaintain-tutorial/tutorial_general_203.png

