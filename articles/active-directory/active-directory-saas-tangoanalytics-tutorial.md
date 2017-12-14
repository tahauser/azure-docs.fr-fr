---
title: "Didacticiel : intégration d’Azure Active Directory à Tango Analytics | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tango Analytics."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2f7555d3-e9ba-40b2-9b3a-2f0ab38a4c08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: c38d98f790d6b2cfd6553f11cc8317174461967f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tango-analytics"></a>Didacticiel : intégration d’Azure Active Directory à Tango Analytics

Dans ce didacticiel, vous allez apprendre à intégrer Tango Analytics à Azure Active Directory (Azure AD).

L’intégration de Tango Analytics dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Tango Analytics
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Tango Analytics (via l’authentification unique) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Tango Analytics, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Tango Analytics pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Tango Analytique à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-tango-analytics-from-the-gallery"></a>Ajout de Tango Analytique à partir de la galerie
Pour configurer l’intégration de Tango Analytics à Azure AD, vous devez ajouter Tango Analytics à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Tango Analytics à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Tango Analytics**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoanalytics-tutorial/tutorial_tangoanalytics_search.png)

5. Dans le panneau de résultats, sélectionnez **Tango Analytics**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoanalytics-tutorial/tutorial_tangoanalytics_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Tango Analytics avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Tango Analytics équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Tango Analytics associé doit être établie.

Dans Tango Analytics, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur **Nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Tango Analytics, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Tango Analytics](#creating-a-tango-analytics-test-user)** pour avoir un équivalent de Britta Simon dans Tango Analytics lié à la représentation de l’utilisateur Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Tango Analytics.

**Pour configurer l’authentification unique Azure AD avec Tango Analytics, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Tango Analytics**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-tangoanalytics-tutorial/tutorial_tangoanalytics_samlbase.png)

3. Dans la section **Domaine et URL Tango Analytics**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-tangoanalytics-tutorial/tutorial_tangoanalytics_url.png)

    a. Dans la zone de texte **Identificateur**, entrez la valeur `TACORE_SSO`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://mts.tangoanalytics.com/saml2/sp/acs/post`

    > [!NOTE] 
    > La valeur de l’URL de réponse n’est pas réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez [l’équipe de support technique de Tango Analytics](mailto:support@tangoanalytics.com).

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-tangoanalytics-tutorial/tutorial_tangoanalytics_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique du côté de **Tango Analytics**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à [l’équipe de support technique Tango Analytics](mailto:support@tangoanalytics.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoanalytics-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoanalytics-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoanalytics-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tangoanalytics-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-tango-analytics-test-user"></a>Création d’un utilisateur de test Tango Analytics

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Tango Analytics. Travailler avec [l’équipe de support technique Tango Analytics](mailto:support@tangoanalytics.com) pour ajouter des utilisateurs dans la plateforme Tango Analytics. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Tango Analytics.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Tango Analytics, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Tango Analytics**.

    ![Configurer l’authentification unique](./media/active-directory-saas-tangoanalytics-tutorial/tutorial_tangoanalytics_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette Tango Analytics dans le volet d’accès, vous devez vous connecter automatiquement à votre application Tango Analytics.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tangoanalytics-tutorial/tutorial_general_203.png

