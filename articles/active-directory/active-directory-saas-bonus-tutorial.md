---
title: "Didacticiel : Intégration d’Azure Active Directory avec Bonusly | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Bonusly."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: b69cf1c42cbc6aedb064378271e2c631b4be39f3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Didacticiel : Intégration d’Azure Active Directory avec Bonusly

Dans ce didacticiel, vous allez apprendre à intégrer Bonusly avec Azure Active Directory (Azure AD).

L’intégration d’Azure AD avec Bonusly offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Bonusly.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Bonusly (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Bonusly, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Bonusly pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Bonusly à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-bonusly-from-the-gallery"></a>Ajout de Bonusly à partir de la galerie
Pour configurer l’intégration de Bonusly dans Azure AD, vous devez ajouter Bonusly à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Bonusly à partir de la galerie, suivez les étapes ci-dessous :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Bonusly**, sélectionnez **Bonusly** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Bonusly dans la liste des résultats](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Bonusly avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Bonusly équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Bonusly associé doit être établie.

Dans Bonusly, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Bonusly, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Bonusly](#create-a-bonusly-test-user)** Permet d’avoir un équivalent de Britta Simon dans Bonusly lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD sur le portail Azure et configurer l’authentification unique dans votre application Bonusly.

**Pour configurer l’authentification unique Azure AD avec Bonusly, suivez les étapes ci-dessous :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Bonusly**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_samlbase.png)

3. Dans la section **Domaine et URL Bonusly**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_url.png)

    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour la valeur avec l’URL de réponse réelle. Pour obtenir cette valeur, contactez [l’équipe de support technique Bonusly](https://Bonusly/contact).
 
4. Dans la section **Certificat de signature SAML**, copiez la valeur **THUMBPRINT** du certificat.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-bonus-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Bonusly**, cliquez sur **Configurer Bonusly** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de Bonusly](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_configure.png) 

7. Dans une autre fenêtre de navigateur, connectez-vous à votre locataire **Bonusly**.

8. Dans la barre d’outils située en haut, cliquez sur **Settings**, puis sélectionnez **Integrations and apps**.
   
    ![Section sociale Bonusly](./media/active-directory-saas-bonus-tutorial/ic773686.png "Bonusly")
9. Sous **Single Sign-On**, sélectionnez **SAML**.

10. Dans la page de boîte de dialogue **SAML** , procédez comme suit :
   
    ![Page de boîte de dialogue Saml Bonusly](./media/active-directory-saas-bonus-tutorial/ic773687.png "Bonusly")
   
    a. Dans la zone de texte **IdP SSO target URL** (URL cible de l’authentification unique), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    b. Dans la zone de texte **Émetteur IdP**, collez la valeur de **ID d’entité SAML** que vous avez copiée depuis le portail Azure. 

    c. Dans la zone de texte **IdP Login URL** (URL de connexion de fournisseur d’identité), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    d. Copiez la valeur située sous **Empreinte** dans le portail Azure, puis collez-la dans la zone de texte **Empreinte du certificat**.
   
11. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-bonus-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-bonus-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Bouton Ajouter](./media/active-directory-saas-bonus-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-bonus-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-bonusly-test-user"></a>Création d’un utilisateur de test Bonusly

Pour permettre aux utilisateurs Azure AD de se connecter à Bonusly, vous devez les approvisionner dans Bonusly. En l’occurrence, cet approvisionnement est une tâche manuelle.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Bonusly pour approvisionner des comptes d’utilisateur Azure Active Directory.
>  

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Dans une fenêtre de navigateur web, connectez-vous à votre locataire Bonusly.

2. Cliquez sur **Settings**.
 
    ![Paramètres](./media/active-directory-saas-bonus-tutorial/ic781041.png "Paramètres")

3. Cliquez sur l’onglet **Users and bonuses** .
   
    ![Users and bonuses](./media/active-directory-saas-bonus-tutorial/ic781042.png "Users and bonuses")

4. Cliquez sur **Manage Users**.
   
    ![Gestion des utilisateurs](./media/active-directory-saas-bonus-tutorial/ic781043.png "Gestion des utilisateurs")

5. Cliquez sur **Add User**.
   
    ![Ajouter un utilisateur](./media/active-directory-saas-bonus-tutorial/ic781044.png "Ajouter un utilisateur")

6. Dans la boîte de dialogue **Ajouter un utilisateur**, procédez comme suit :
   
    ![Ajouter un utilisateur](./media/active-directory-saas-bonus-tutorial/ic781045.png "Ajouter un utilisateur")  

    a. Dans la zone de texte **First name**, entrez le prénom de l’utilisateur, par exemple **Britta**.

    b. Dans la zone de texte **Last name**, tapez le nom de l’utilisateur, par exemple **Simon**.
 
    c. Dans la zone de texte **E-mail**, entrez l’adresse de messagerie de l’utilisateur, par exemple **brittasimon@contoso.com**.

    d. Cliquez sur **Enregistrer**.
   
     >[!NOTE]
     >Le titulaire du compte Azure AD reçoit alors un e-mail contenant un lien pour confirmer le compte avant qu’il ne soit activé.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Bonusly.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Bonusly, suivez les étapes ci-dessous :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Bonusly**.

    ![Lien Bonusly dans la liste des applications](./media/active-directory-saas-bonus-tutorial/tutorial_bonusly_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Le fait de cliquer sur la vignette Bonusly dans le volet d’accès vous connecte automatiquement à votre application Bonusly.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonus-tutorial/tutorial_general_203.png

