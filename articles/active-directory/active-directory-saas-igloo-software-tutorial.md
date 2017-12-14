---
title: "Didacticiel : Intégration d’Azure Active Directory avec Igloo Software | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Igloo Software."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 8fc957a6a54f9f3a640147f453ebefcb8aac9d4c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Didacticiel : Intégration d’Azure Active Directory avec Igloo Software

Dans ce didacticiel, vous allez apprendre à intégrer Igloo Software à Azure Active Directory (Azure AD).

L’intégration d’Igloo Software à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Igloo Software.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Igloo Software (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Igloo Software, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Igloo Software pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Igloo Software à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-igloo-software-from-the-gallery"></a>Ajout d’Igloo Software à partir de la galerie
Pour configurer l’intégration d’Igloo Software à Azure AD, vous devez ajouter Igloo Software à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Igloo Software à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Igloo Software**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. Dans le panneau de résultats, sélectionnez **Igloo Software**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Igloo Software à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD a besoin de savoir qui est l’utilisateur Igloo Software équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Igloo Software associé doit être établi.

Dans Igloo Software, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Igloo Software, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Igloo Software](#creating-an-igloo-software-test-user)** pour avoir un équivalent de Britta Simon dans Igloo Software lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Igloo Software.

**Pour configurer l’authentification unique Azure AD avec Igloo Software, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Igloo Software**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. Dans la section **Igloo Software Domain and URLs** (Domaine et URL Igloo Software), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<company name>.igloocommmunities.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company name>.igloocommmunities.com/saml.digest`

    c. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Igloo Software](https://www.igloosoftware.com/services/support). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-igloo-software-tutorial/tutorial_general_400.png)
    
6. Dans la section **Igloo Software Configuration** (Configuration d’Igloo Software), cliquez sur **Configure Igloo Software** (Configurer Igloo Software) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Igloo Software en tant qu’administrateur.

8. Accédez à **Control panel**.
   
     ![Control Panel (Panneau de configuration)](./media/active-directory-saas-igloo-software-tutorial/ic799949.png "Control Panel (Panneau de configuration)")

9. Sous l’onglet **Membership** (Appartenance), cliquez sur **Paramètres de connexion**.
   
    ![Sign in Settings (Paramètres de connexion)](./media/active-directory-saas-igloo-software-tutorial/ic783968.png "Sign in Settings (Paramètres de connexion)")

10. Dans la section SAML Configuration, cliquez sur **Configure SAML Authentication**.
   
    ![Configuration SAML](./media/active-directory-saas-igloo-software-tutorial/ic783969.png "Configuration SAML")
   
11. Dans la section **General Configuration** , procédez comme suit :
   
    ![General Configuration (Configuration générale)](./media/active-directory-saas-igloo-software-tutorial/ic783970.png "General Configuration (Configuration générale)")

    a. Dans la zone de texte **Connection Name** , entrez un nom personnalisé pour votre configuration.
   
    b. Dans la zone de texte **IdP Login URL** (URL de connexion de fournisseur d’identité), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    c. Dans la zone de texte **IdP Logout URL** (URL de déconnexion de fournisseur d’identité), collez la valeur de **l’URL de déconnexion** que vous avez copiée à partir du portail Azure.
    
    d. Pour **Logout Response and Request HTTP Type** (Type de réponse de déconnexion et de requête HTTP), sélectionnez **POST**.
   
    e. Ouvrez dans le Bloc-notes votre certificat codé en **base 64** téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Certificat public**.
    
12. Dans **Response and Authentication Configuration**, procédez comme suit :
    
    ![Response and Authentication Configuration (Configuration de la réponse et de l’authentification)](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "Response and Authentication Configuration (Configuration de la réponse et de l’authentification)")
  
      a. Pour **Fournisseur d’identité**, sélectionnez **Microsoft ADFS**.
      
      b. Pour **Type d’identificateur**, sélectionnez **Adresse de messagerie**. 

      c. Dans la zone de texte **Email Attribute**, entrez **emailaddress**.

      d. Dans la zone de texte **Attribut de prénom**, saisissez **givenname**.

      e. Dans la zone de texte **Attribut de nom**, saisissez **surname**.

13. Pour terminer la configuration, procédez comme suit :
    
    ![Création d’utilisateurs à l’authentification](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "Création d’utilisateurs à l’authentification") 

     a. Pour **Création d’utilisateurs à l’authentification**, sélectionnez **Create a new user in your site when they sign in** (Créer un nouvel utilisateur sur votre site au moment de la connexion).

     b. Pour **Paramètres de connexion**, sélectionnez **Use SAML button on “Sign in” screen** (Utiliser le bouton SAML sur l’écran de connexion).

     c. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-igloo-software-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-igloo-software-test-user"></a>Création d’un utilisateur de test Igloo Software

Aucun élément d’action ne vous permet de configurer l’approvisionnement des utilisateurs dans Igloo Software.  

Lorsqu’un utilisateur assigné tente de se connecter à Igloo Software à l’aide du panneau d’accès, Igloo Software vérifie si cet utilisateur existe.  Si aucun compte d’utilisateur n’est disponible, Igloo Software le crée automatiquement.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Igloo Software.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à Igloo Software, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Igloo Software**.

    ![Configurer l’authentification unique](./media/active-directory-saas-igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Igloo Software dans le panneau d’accès, vous devez être connecté automatiquement à votre application Igloo Software.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-igloo-software-tutorial/tutorial_general_203.png

