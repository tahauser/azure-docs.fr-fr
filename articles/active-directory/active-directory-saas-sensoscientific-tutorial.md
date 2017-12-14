---
title: "Didacticiel : Intégration d’Azure Active Directory avec Système de surveillance de température sans fil SensoScientific | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Système de surveillance de température sans fil SensoScientific."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.openlocfilehash: e2863e1094cdbd66744141b25213313c09c6de4b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Didacticiel : Intégration d’Azure Active Directory avec Système de surveillance de température sans fil SensoScientific

Dans ce didacticiel, vous allez découvrir comment intégrer Système de surveillance de température sans fil SensoScientific avec Azure Active Directory (Azure AD).

L’intégration de Système de surveillance de température sans fil SensoScientific avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Système de surveillance de température sans fil SensoScientific
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Système de surveillance de température sans fil SensoScientific (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes depuis un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Système de surveillance de température sans fil SensoScientific, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Système de surveillance de température sans fil SensoScientific pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Système de surveillance de température sans fil SensoScientific à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Ajout de Système de surveillance de température sans fil SensoScientific à partir de la galerie
Pour configurer l’intégration de Système de surveillance de température sans fil SensoScientific dans Azure AD, vous devez ajouter Système de surveillance de température sans fil SensoScientific à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter Système de surveillance de température sans fil SensoScientific à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Système de surveillance de température sans fil SensoScientific**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

5. Dans le volet de résultats, sélectionnez **Système de surveillance de température sans fil SensoScientific**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Système de surveillance de température sans fil SensoScientific avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Système de surveillance de température sans fil SensoScientific équivalent dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et un utilisateur Système de surveillance de température sans fil SensoScientific associé.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Système de surveillance de température sans fil SensoScientific.

Pour configurer et tester l’authentification unique Azure AD avec Système de surveillance de température sans fil SensoScientific, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Système de surveillance de température sans fil SensoScientific](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)** pour disposer d’un équivalent de Britta Simon dans Système de surveillance de température sans fil SensoScientific qui est lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Système de surveillance de température sans fil SensoScientific.

**Pour configurer l’authentification unique Azure AD avec Système de surveillance de température sans fil SensoScientific, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration d’application **Système de surveillance de température sans fil SensoScientific**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

3. Dans la section **Domaine et URL de Système de surveillance de température sans fil SensoScientific**, aucune action n’est requise car l’application est déjà pré-intégrée à Azure :

    ![Configurer l’authentification unique](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Système de surveillance de température sans fil SensoScientific**, cliquez sur **Configurer Système de surveillance de température sans fil SensoScientific** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML** et **l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

7. Connectez-vous à votre application Système de surveillance de température sans fil SensoScientific en tant qu’administrateur.

8. Dans le menu de navigation en haut, cliquez sur **Configuration** et accédez à **Configurer** sous **Authentification unique** pour ouvrir les paramètres de l’authentification unique.

    ![Configurer l’authentification unique](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

9. Dans le formulaire **Paramètres de l’authentification unique** , procédez comme suit :
 
    a. Sélectionnez **Nom de l’émetteur** comme Azure AD.
    
    b. Collez l’**ID d’entité SAML** que vous avez copié à partir du portail Azure dans la zone de texte de l’URL de l’émetteur.
    
    c. Collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure dans la zone de texte de l’URL du service d’authentification unique.

    d. Collez l’**URL de déconnexion unique** que vous avez copiée à partir du portail Azure dans la zone de texte de l’URL du service de déconnexion unique.

    e. Accédez au certificat que vous avez téléchargé à partir du portail Azure et chargez-le ici.
    
    f. Cliquez sur **Save**.
  
> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Création d’un utilisateur de test Système de surveillance de température sans fil SensoScientific

Pour autoriser les utilisateurs d’Azure AD à se connecter à Système de surveillance de température sans fil SensoScientific, ils doivent être configurés dans Système de surveillance de température sans fil SensoScientific. Travaillez avec l’[équipe de support technique de Système de surveillance de température sans fil SensoScientific](https://www.sensoscientific.com/contact-us/) pour ajouter des utilisateurs dans la plateforme Système de surveillance de température sans fil SensoScientific. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Système de surveillance de température sans fil SensoScientific.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Système de surveillance de température sans fil SensoScientific, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Système de surveillance de température sans fil SensoScientific**.

    ![Configurer l’authentification unique](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès. Cliquez sur la vignette Système de surveillance de température sans fil SensoScientific dans le panneau d’accès. Vous êtes alors automatiquement connecté à votre application Système de surveillance de température sans fil SensoScientific. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_203.png

