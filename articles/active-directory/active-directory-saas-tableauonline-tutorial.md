---
title: "Didacticiel : Intégration d’Azure Active Directory à Tableau Online | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Tableau Online."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: jeedes
ms.openlocfilehash: 47ae9dbde509726065da7eaee2c7aec491389f45
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Didacticiel : Intégration d’Azure Active Directory à Tableau Online

Dans ce didacticiel, vous allez apprendre à intégrer Tableau Online à Azure Active Directory (Azure AD).

L’intégration de Tableau Online à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Tableau Online.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Tableau Online (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Tableau Online, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Tableau Online pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Tableau Online à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-tableau-online-from-the-gallery"></a>Ajout de Tableau Online à partir de la galerie
Pour configurer l’intégration de Tableau Online à Azure AD, vous devez ajouter Tableau Online à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Tableau Online à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Tableau Online**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_search.png)

5. Dans le panneau de résultats, sélectionnez **Tableau Online**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Tableau Online à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Tableau Online équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Tableau Online associé doit être établie.

Dans Tableau Online, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Tableau Online, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Tableau Online](#creating-a-tableau-online-test-user)** pour avoir un équivalent de Britta Simon dans Tableau Online lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Tableau Online.

**Pour configurer l’authentification unique Azure AD avec Tableau Online, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Tableau Online**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

3. Dans la section **Tableau Online Domain and URLs** (Domaine et URL Tableau Online), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://sso.online.tableau.com`

    b. Dans la zone de texte **Identificateur**, tapez l’URL : `https://sso.online.tableau.com/public/sp/<instancename>`

4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_general_400.png)

6. Dans une autre fenêtre du navigateur, connectez-vous à votre application Tableau Online. Cliquez sur **Settings** (Paramètres), puis sur **Authentication** (Authentification).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
    
7. Pour activer SAML, dans la section **Types d’authentification**, cochez la case **Single sign-on with SAML** (Authentification unique avec SAML).
   
    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

8. Faites défiler la page jusqu’à la section **Import metadata file into Tableau Online (Importer le fichier de métadonnées dans Tableau Online)** .  Cliquez sur Browse (Parcourir) et importez le fichier de métadonnées que vous avez téléchargé à partir d’Azure AD. Cliquez alors sur **Apply (Appliquer)**.
   
   ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

9. Dans la section **Match assertions** (Faire correspondre les assertions), insérez les noms d’assertion du fournisseur d’identité correspondants pour **l’adresse e-mail**, le **prénom** et le **nom**. Pour obtenir ces informations à partir d’Azure AD : 
  
    a. Dans le portail Azure, accédez à la page d’intégration de l’application **Tableau Online**.
    
    b. Dans la section des attributs, cochez la case **« Afficher et modifier tous les autres attributs utilisateur »**. 
    
   ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/attributesection.png)
      
    c. Copiez la valeur de l’espace de noms de ces attributs : prénom, adresse e-mail et nom de famille en procédant comme suit :

   ![Authentification unique Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Cliquez sur la valeur **user.givenname**. 
    
    e. Copiez la valeur à partir de la zone de texte **espace de noms**.

   ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/attributesection2.png)

    f. Pour copier les valeurs d’espace de noms pour l’adresse e-mail et le nom de famille, suivez les étapes précédentes.

    g. Basculez dans l’application Tableau Online, puis définissez la section **Tableau Online Attributes** (Attributs Tableau Online) comme suit :
     * Email (Adresse de messagerie) : **mail** ou **userPrincipalName**
     * First name (Prénom) : **givenName**
     * Last name (Nom) : **surname**
   
   ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-tableau-online-test-user"></a>Création d’un utilisateur de test Tableau Online

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Tableau Online.

1. Dans **Tableau Online**, cliquez sur **Paramètres**, puis sur la section **Authentification**. Faites défiler la page jusqu’à la section **Select Users (Sélectionner des utilisateurs)** . Cliquez sur **Ajouter des utilisateurs**, puis sur **Entrer les adresses de messagerie**.
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Sélectionnez **Add users for single sign-on (SSO) authentication (Ajouter des utilisateurs pour l’authentification unique (SSO))**. Dans la zone de texte **Enter Email Addresses** (Entrez les adresses de messagerie), ajoutez britta.simon@contoso.com
   
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Cliquez sur **Create**.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Tableau Online.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Tableau Online, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Tableau Online**.

    ![Configurer l’authentification unique](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Tableau Online dans le volet d’accès, vous devez vous connecter automatiquement à votre application Tableau Online.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png

