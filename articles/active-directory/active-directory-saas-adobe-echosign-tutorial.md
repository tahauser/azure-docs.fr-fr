---
title: "Didacticiel : Intégration d’Azure Active Directory à Adobe Sign | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adobe Sign."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2017
ms.author: jeedes
ms.openlocfilehash: f68701cc345ee1bb04cde265ac4e2d6a402da557
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Didacticiel : Intégration d’Azure Active Directory à Adobe Sign

Dans ce didacticiel, vous allez apprendre à intégrer Adobe Sign à Azure Active Directory (Azure AD).

L’intégration d’Adobe Sign dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Adobe Sign
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Adobe Sign (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Adobe Sign, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Adobe Sign pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Adobe Sign à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-adobe-sign-from-the-gallery"></a>Ajout d’Adobe Sign à partir de la galerie
Pour configurer l’intégration d’Adobe Sign à Azure AD, vous devez ajouter Adobe Sign depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Adobe Sign à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Adobe Sign**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_search.png)

5. Dans le volet de résultats, sélectionnez **Adobe Sign**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Adobe Sign au moyen d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Adobe Sign équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Adobe Sign associé doit être établie.

Dans Adobe Sign, affectez la valeur du **nom d’utilisateur** indiquée dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Adobe Sign, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Adobe Sign](#creating-an-adobe-sign-test-user)** pour avoir dans Adobe Sign un équivalent de Britta Simon lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Adobe Sign.

**Pour configurer l’authentification unique Azure AD avec Adobe Sign, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Adobe Sign**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_samlbase.png)

3. Dans la section **Domaine et URL Adobe Sign**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.echosign.com/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.echosign.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe du support technique d’Adobe Sign](https://helpx.adobe.com/in/contact/support.html). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration Adobe Sign**, cliquez sur **Configurer Adobe Sign** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_configure.png) 


7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adobe Sign en tant qu’administrateur.

8. Dans le menu du haut, cliquez sur **Account** (Compte) puis, dans le volet de navigation à gauche, cliquez sur **SAML Settings** (Paramètres SAML) sous **Account Settings** (Paramètres du compte).
   
   ![Compte](./media/active-directory-saas-adobe-echosign-tutorial/ic789520.png "Compte")

9. Dans la section SAML Settings, procédez comme suit :
   
   ![Paramètres SAML](./media/active-directory-saas-adobe-echosign-tutorial/ic789521.png "Paramètres SAML")
   
   a. Pour **SAML Mode** (Mode SAML), sélectionnez **SAML Mandatory** (SAML obligatoire).
   
   b. Sélectionnez **Allow EchoSign Account Administrators to log in using their EchoSign Credentials** (Autoriser les administrateurs de compte EchoSign à se connecter avec leurs informations d’identification EchoSign).
   
   c. Pour **User Creation** (Création d’utilisateurs), sélectionnez **Automatically add users authenticated through SAML** (Ajouter automatiquement les utilisateurs authentifiés avec SAML).

10. Ensuite, effectuez les étapes suivantes :

       ![Paramètres SAML](./media/active-directory-saas-adobe-echosign-tutorial/ic789522.png "Paramètres SAML")

    a. Dans la zone de texte **IdP Entity ID** (ID d’entité IdP), collez l’**ID d’entité SAML** que vous avez copié à partir du portail Azure.
    
    b. Dans la zone de texte **IdP Login URL** (URL de connexion IdP), collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    c. Dans la zone de texte **Sign-Out URL** (URL de déconnexion IdP), collez l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Ouvrez dans le Bloc-notes votre fichier **Certificate(Base64)** téléchargé, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **IdP Certificate** (Certificat IdP).

    e. Cliquez sur **Enregistrer les modifications**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adobe-echosign-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-an-adobe-sign-test-user"></a>Création d’un utilisateur de test Adobe Sign

Pour se connecter à Adobe Sign, les utilisateurs d’Azure AD doivent être approvisionnés dans Adobe Sign. En l’occurrence, cet approvisionnement est une tâche manuelle.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur fourni par Adobe Sign pour approvisionner des comptes d’utilisateur Azure Active Directory. 

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Adobe Sign** en tant qu’administrateur.

2. Dans le menu du haut, cliquez sur **Account** (Compte) puis, dans le volet de navigation à gauche, cliquez sur **Users & Groups** (Utilisateurs & groupes), puis sur **Create a new user** (Créer un utilisateur).
   
   ![Compte](./media/active-directory-saas-adobe-echosign-tutorial/ic789524.png "Compte")
   
3. Dans la section **Create New User** (Créer un utilisateur), procédez comme suit :
   
   ![Créer un utilisateur](./media/active-directory-saas-adobe-echosign-tutorial/ic789525.png "Créer un utilisateur")
   
   a. Pour le compte AAD (Azure Active Directory) valide que vous souhaitez approvisionner, tapez l’adresse e-mail, le prénom et le nom associés dans les zones de texte correspondantes, à savoir **Email Address**, **First Name** et **Last Name**.
   
   b. Cliquez sur **Create User**.

>[!NOTE]
>Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien pour confirmer le compte avant qu’il ne soit activé. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à Adobe Sign.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Adobe Sign, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Adobe Sign**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adobe-echosign-tutorial/tutorial_adobesign_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Lorsque vous cliquez sur la vignette Adobe Sign dans le volet d’accès, vous devez automatiquement être connecté à votre application Adobe Sign.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobe-echosign-tutorial/tutorial_general_203.png

