---
title: "Didacticiel : Intégration d’Azure Active Directory avec Picturepark | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Picturepark."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: 4d9fd7127a36e9a699a352dbe6899edd5ea99e92
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Didacticiel : Intégration d’Azure Active Directory avec Picturepark

Ce didacticiel explique comment intégrer Picturepark avec Azure Active Directory (Azure AD).

L’intégration de Picturepark avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Picturepark.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Picturepark (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec Picturepark, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Picturepark pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Picturepark à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-picturepark-from-the-gallery"></a>Ajout de Picturepark à partir de la galerie
Pour configurer l’intégration de Picturepark à Azure AD, vous devez ajouter Picturepark à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Picturepark à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Picturepark**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_search.png)

5. Dans le volet de résultats, sélectionnez **Picturepark**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Picturepark sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Picturepark équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur associé dans Picturepark.

Dans Picturepark, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Picturepark, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Picturepark](#creating-a-picturepark-test-user)** pour avoir un équivalent de Britta Simon dans Picturepark, lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Picturepark.

**Pour configurer l’authentification unique Azure AD avec Picturepark, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Picturepark**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_samlbase.png)

3. Dans la section **Domaine et URL Picturepark**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.picturepark.com`

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant : 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique Picturepark](https://picturepark.com/about/contact/). 
 
4. Dans la section **Certificat de signature SAML**, copiez la valeur **THUMBPRINT** du certificat.

    ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Picturepark**, cliquez sur **Configurer Picturepark** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Picturepark en tant qu’administrateur.

8. Dans la barre d’outils située en haut, cliquez sur **Administrative tools**, puis sur **Management Console**.
   
    ![Console de gestion](./media/active-directory-saas-picturepark-tutorial/ic795062.png "Console de gestion")

9. Cliquez sur **Authentication**, puis sur **Identity providers**.
   
    ![Authentication](./media/active-directory-saas-picturepark-tutorial/ic795063.png "Authentication")

10. Dans la section **Identity provider configuration** , procédez comme suit :
   
    ![Configuration du fournisseur d’identité](./media/active-directory-saas-picturepark-tutorial/ic795064.png "Configuration du fournisseur d’identité")
   
    a. Cliquez sur **Add**.
  
    b. Entrez un nom pour votre configuration.
   
    c. Sélectionnez **Set as default**.
   
    d. Dans la zone de texte **Issuer URI (URI de l’émetteur)**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    e. Dans la zone de texte **Trusted Issuer Thumb Print** (Empreinte numérique de l’émetteur approuvé), collez la valeur de l’**empreinte** que vous avez copiée à partir de la section **Certificat de signature SAML**. 

11. Cliquez sur **JoinDefaultUsersGroup**.

12. Pour définir l’attribut **Emailaddress** dans la zone de texte **Revendication**, tapez `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`, puis cliquez sur **Enregistrer**.

      ![Configuration](./media/active-directory-saas-picturepark-tutorial/ic795065.png "Configuration")

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-picturepark-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-picturepark-test-user"></a>Création d’un utilisateur de test Picturepark

Pour permettre aux utilisateurs Azure AD de se connecter à Picturepark, vous devez les approvisionner dans Picturepark. Dans le cas de Picturepark, l’approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Picturepark** .

2. Dans la barre d’outils située en haut, cliquez sur **Administrative tools**, puis sur **Users**.
   
    ![Utilisateurs](./media/active-directory-saas-picturepark-tutorial/ic795067.png "Utilisateurs")

3. Dans l’onglet **Users overview**, cliquez sur **New**.
   
    ![Gestion des utilisateurs](./media/active-directory-saas-picturepark-tutorial/ic795068.png "gestion des utilisateurs")

4. Dans la boîte de dialogue **Créer un utilisateur**, procédez comme suit pour un utilisateur Azure Active Directory valide que vous souhaitez approvisionner :
   
    ![Create User](./media/active-directory-saas-picturepark-tutorial/ic795069.png "Create User")
   
    a. Dans la zone de texte **Email Address** (Adresse e-mail), entrez l’**adresse e-mail** de l’utilisateur **BrittaSimon@contoso.com**.  
   
    b. Dans les zones de texte **Password** (Mot de passe) et **Confirm Password** (Confirmer le mot de passe), entrez le **mot de passe** de BrittaSimon. 
   
    c. Dans la zone de texte **First Name** (Prénom), entrez le **prénom** de l’utilisateur **Britta**. 
   
    d. Dans la zone de texte **Last Name** (Nom), entrez le **nom** de l’utilisateur **Simon**.
   
    e. Dans la zone de texte **Company** (Société), entrez le **nom de la société** de l’utilisateur. 
   
    f. Dans la zone de texte **Country** (Pays), sélectionnez le **pays** de l’utilisateur.
  
    g. Dans la zone de texte **ZIP** (Code postal), entrez le **code postal** de la ville.
   
    h. Dans la zone de texte **City** (Ville), entrez le **nom de la ville** de l’utilisateur.

    i. Sélectionnez une langue dans **Language**.
   
    j. Cliquez sur **Create**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel outil ou API de création de compte utilisateur fournis par Picturepark pour approvisionner des comptes utilisateur Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Picturepark.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Picturepark, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Picturepark**.

    ![Configurer l’authentification unique](./media/active-directory-saas-picturepark-tutorial/tutorial_picturepark_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Picturepark dans le volet d’accès, vous devez être connecté automatiquement à votre application Picturepark. Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-picturepark-tutorial/tutorial_general_203.png

