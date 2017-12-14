---
title: "Didacticiel : Intégration d’Azure Active Directory avec IdeaScale | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et IdeaScale."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: b9db921a88328c39cbf6bb4fcb6307cd77b748a5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Didacticiel : Intégration d’Azure Active Directory à IdeaScale

Dans ce didacticiel, vous allez apprendre à intégrer IdeaScale à Azure Active Directory (Azure AD).

L’intégration d’IdeaScale à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à IdeaScale.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à IdeaScale (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à IdeaScale, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement IdeaScale pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’IdeaScale à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-ideascale-from-the-gallery"></a>Ajout d’IdeaScale à partir de la galerie
Pour configurer l’intégration d’IdeaScale à Azure AD, vous devez ajouter IdeaScale à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter IdeaScale à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **IdeaScale**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_search.png)

5. Dans le panneau de résultats, sélectionnez **IdeaScale**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec IdeaScale à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur IdeaScale équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur IdeaScale associé doit être établie.

Dans IdeaScale, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec IdeaScale, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test IdeaScale](#creating-an-ideascale-test-user)** pour avoir un équivalent de Britta Simon dans IdeaScale lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application IdeaScale.

**Pour configurer l’authentification unique Azure AD avec IdeaScale, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **IdeaScale**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_samlbase.png)

3. Dans la section **IdeaScale Domain and URLs** (Domaine et URL IdeaScale), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.ideascale.com`

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant :
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique IdeaScale](http://support.ideascale.com/). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/tutorial_general_400.png)

6. Dans la section **IdeaScale Configuration** (Configuration d’IdeaScale), cliquez sur **Configure IdeaScale** (Configurer IdeaScale) pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’ID d’entité SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise IdeaScale en tant qu’administrateur.

8. Accédez à **Community Settings**.
   
    ![Community Settings](./media/active-directory-saas-ideascale-tutorial/ic790847.png "Community Settings")

9. Accédez à **Security \> Single Signon Settings**.
   
    ![Single Signon Settings](./media/active-directory-saas-ideascale-tutorial/ic790848.png "Single Signon Settings")

10. Pour **Single-Signon Type**, sélectionnez **SAML 2.0**.
   
    ![Single Signon Type](./media/active-directory-saas-ideascale-tutorial/ic790849.png "Single Signon Type")

11. Dans la page de boîte de dialogue **Paramètres de l’authentification unique** , procédez comme suit :
   
    ![Single Signon Settings](./media/active-directory-saas-ideascale-tutorial/ic790850.png "Single Signon Settings")
   
    a. Dans la zone de texte **SAML IdP Entity ID** (ID d’entité du fournisseur d’identité SAML), collez la valeur de **l’ID d’entité SAML** que vous avez copiée depuis le portail Azure.

    b. Copiez le contenu de votre fichier de métadonnées téléchargé depuis le portail Azure et collez-le dans la zone de texte **SAML IdP Metadata** (Métadonnées du fournisseur d’identité SAML).

    c. Dans la zone de texte **Logout Success URL** (URL de déconnexion réussie), collez la valeur de **l’URL de déconnexion** que vous avez copiée à partir du portail Azure.

    d. Cliquez sur **Enregistrer les modifications**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-ideascale-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-ideascale-test-user"></a>Création d’un utilisateur de test IdeaScale

Pour permettre aux utilisateurs Azure AD de se connecter à IdeaScale, vous devez les approvisionner dans IdeaScale. Dans le cas d’IdeaScale, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **IdeaScale** en tant qu’administrateur.

2. Accédez à **Community Settings**.
   
    ![Community Settings](./media/active-directory-saas-ideascale-tutorial/ic790847.png "Community Settings")

3. Accédez à **Basic Settings \> Member Management**.

4. Cliquez sur **Add Member**.
   
    ![Member Management](./media/active-directory-saas-ideascale-tutorial/ic790852.png "Member Management")

5. Dans la section Add New Member, procédez comme suit :
   
    ![Add New Member](./media/active-directory-saas-ideascale-tutorial/ic790853.png "Add New Member")
   
    a. Indiquez l’adresse de messagerie d’un compte Azure Active Directory valide que vous souhaitez approvisionner dans la zone de texte **Email Addresses** .
   
    b. Cliquez sur **Enregistrer les modifications**. 
   
    >[!NOTE]
    >Le titulaire du compte Azure Active Directory reçoit un message électronique avec un lien pour confirmer le compte avant qu’il ne soit activé.
      
>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par IdeaScale pour approvisionner des comptes d’utilisateur Azure Active Directory.
 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à IdeaScale.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à IdeaScale, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **IdeaScale**.

    ![Configurer l’authentification unique](./media/active-directory-saas-ideascale-tutorial/tutorial_ideascale_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique


L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette IdeaScale dans le panneau d’accès, vous devez vous connecter automatiquement à votre application IdeaScale.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ideascale-tutorial/tutorial_general_203.png

