---
title: "Didacticiel : Intégration d’Azure Active Directory à Jive | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Jive."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9fc5659a-c116-4a1b-a601-333325a26b46
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 272b2697d59ee1ec961db6cdd13231030697d1d1
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Didacticiel : Intégration d’Azure Active Directory avec Jive

Dans ce didacticiel, vous allez apprendre à intégrer Jive à Azure Active Directory (Azure AD).

L’intégration de Jive dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Jive
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Jive (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Jive, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Jive pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Jive depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-jive-from-the-gallery"></a>Ajout de Jive depuis la galerie
Pour configurer l’intégration de Jive avec Azure AD, vous devez ajouter Jive disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Jive à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Jive**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_search.png)

5. Dans le volet de résultats, sélectionnez **Jive**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/tutorial_jive_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Jive avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Jive équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Jive associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Jive.

Pour configurer et tester l’authentification unique Azure AD avec Jive, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Jive](#creating-a-jive-test-user)** pour avoir un équivalent de Britta Simon dans Jive lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Jive.

**Pour configurer l’authentification unique Azure AD avec Jive, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Jive**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_samlbase.png)

3. Dans la section **Domaine et URL Jive**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<instance name>.jivecustom.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<instance name>.jiveon.com`

    > [!NOTE] 
    > Il ne s’agit pas des valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Jive](https://www.jivesoftware.com/services-support/). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique côté **Jive**, connectez-vous au locataire Jive en tant qu’administrateur.

7. Dans le menu du haut, cliquez sur **Saml**.

    ![Configurer l’authentification unique côté application](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)

    a. Sélectionnez **Activé** sous l’onglet **Général**.   
    b. Cliquez sur le bouton «**Enregistrer tous les paramètres saml**».

8. Accédez à l’onglet «**Idp Metadata**(Métadonnées du fournisseur d’identité) ».
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)
   
    a. Copiez le contenu du fichier XML de métadonnées téléchargé et collez-le dans la zone de texte **Métadonnées du fournisseur d'identité (IDP)** .
    
    b. Cliquez sur le bouton «**Enregistrer tous les paramètres saml**». 

9. Accédez à l’onglet «**Mappage d’attributs utilisateur**».
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)
   
    a. Dans la zone de texte **E-mail**, copiez et collez le nom de la valeur **mail**.
   
    b. Dans la zone de texte **Prénom**, copiez et collez le nom de la valeur **givenname**.
   
    c. Dans la zone de texte **Nom**, copiez et collez le nom de la valeur **surname**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-jive-test-user"></a>Création d’un utilisateur de test Jive

Collaborez avec l’[équipe du support du client Jive](https://www.jivesoftware.com/services-support/) pour ajouter des utilisateurs dans la plateforme Jive.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Jive.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Jive, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Jive**.

    ![Configurer l’authentification unique](./media/active-directory-saas-jive-tutorial/tutorial_jive_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Jive dans le volet d’accès, vous devez être connecté automatiquement à votre application Jive.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)
* [Configurer l’approvisionnement de l’utilisateur](active-directory-saas-jive-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jive-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png

