---
title: "Didacticiel : Intégration d’Azure Active Directory à Citrix ShareFile | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Citrix ShareFile."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 8473c262f98e77708f01d17419e935979a533307
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Didacticiel : Intégration d’Azure Active Directory à Citrix ShareFile

Dans ce didacticiel, vous allez apprendre à intégrer Citrix ShareFile à Azure Active Directory (Azure AD).

L’intégration de Citrix ShareFile à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Citrix ShareFile.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Citrix ShareFile (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Citrix ShareFile, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Citrix ShareFile pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Citrix ShareFile à partir de la galerie
2. Configurer et tester l’authentification unique Azure AD

## <a name="add-citrix-sharefile-from-the-gallery"></a>Ajout de Citrix ShareFile à partir de la galerie
Pour configurer l’intégration de Citrix ShareFile avec Azure AD, vous devez ajouter Citrix ShareFile, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Citrix ShareFile à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Citrix ShareFile**, sélectionnez **Citrix ShareFile** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Citrix ShareFile dans la liste des résultats](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Citrix ShareFile avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Citrix ShareFile équivalent dans Azure AD. En d’autres termes, un lien entre un utilisateur Azure AD et l’utilisateur Citrix ShareFile associé doit être établi.

Dans Citrix ShareFile, attribuez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Citrix ShareFile, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Citrix ShareFile](#create-a-citrix-sharefile-test-user)** pour avoir un équivalent de Britta Simon dans Citrix ShareFile, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et vous configurez l’authentification unique dans votre application Citrix ShareFile.

**Pour configurer l’authentification unique Azure AD avec Citrix ShareFile, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Citrix ShareFile**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_samlbase.png)

3. Dans la section **Domaine et URL Citrix ShareFile**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_url.png)
    
    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenant-name>.sharefile.com/saml/login`

    > [!NOTE] 
    > Cette valeur n’est pas la valeur réelle. Mettez à jour cette valeur avec l’URL de connexion réelle. Pour obtenir cette valeur, contactez [l’équipe du support Citrix ShareFile](https://www.citrix.co.in/products/sharefile/support.html). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-sharefile-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Citrix ShareFile** , cliquez sur **Configurer Citrix ShareFile** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise **Citrix ShareFile** en tant qu’administrateur.

8. Dans la barre d’outils située en haut, cliquez sur **Admin**.

9. Dans le volet de navigation de gauche, sélectionnez **Configure Single Sign-On**.
   
    ![Compte d’administration](./media/active-directory-saas-sharefile-tutorial/ic773627.png "Compte d’administration")

10. Dans la page de boîte de dialogue **Single Sign-On/ SAML 2.0 Configuration** sous **Basic Settings**, procédez comme suit :
   
    ![Authentification unique](./media/active-directory-saas-sharefile-tutorial/ic773628.png "Authentification unique")
   
    a. Cliquez sur **Enable SAML**.
    
    b. Dans la zone de texte **Your IDP Issuer/Entity ID** (Votre émetteur IDP/ID d’entité), collez la valeur **ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    c. Cliquez sur **Change** (Modifier) en regard du champ **X.509 Certificate** (Certificat X.509), puis chargez le certificat que vous avez téléchargé à partir du portail Azure.
    
    d. Dans la zone de texte **URL de connexion**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée dans portail Azure.
    
    e. Dans la zone de texte **Logout URL (URL de déconnexion)**, collez la valeur **URL de déconnexion** que vous avez copiée à partir du portail Azure.

11. Cliquez sur **Save** dans le portail de gestion Citrix ShareFile.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-citrix-sharefile-test-user"></a>Créer un utilisateur de test Citrix ShareFile

Pour permettre aux utilisateurs Azure AD de se connecter à Citrix ShareFile, vous devez les approvisionner dans Citrix ShareFile. En l’occurrence, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre locataire **Citrix ShareFile** .

2. Cliquez sur **Manage Users \> Manage Users Home \> + Create Employee**.
   
   ![Create Employee](./media/active-directory-saas-sharefile-tutorial/IC781050.png "Create Employee")

3. Dans la section **Basic Information**, procédez comme suit :
   
   ![Informations de base](./media/active-directory-saas-sharefile-tutorial/IC799951.png "Informations de base")
   
   a. Dans la zone de texte **Email Address** (Adresse e-mail), tapez l’adresse e-mail du compte de Britta Simon au format suivant : **brittasimon@contoso.com**.
   
   b. Dans la zone de texte **First Name**, entrez le **prénom** de l’utilisateur. Ici, il s’agit de **Britta**.
   
   c. Dans la zone de texte **Last Name**, entrez le **nom** de l’utilisateur. Ici, il s’agit de **Simon**.

4. Cliquez sur **Add User**.
  
   >[!NOTE]
   >Le titulaire du compte Azure AD va recevoir un e-mail contenant un lien lui permettant de confirmer son compte avant que celui-ci ne soit activé. Vous pouvez utiliser n’importe quel autre outil de création de compte d’utilisateur Citrix ShareFile ou toute autre API fournie par Citrix ShareFile pour approvisionner des comptes d’utilisateur Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Citrix ShareFile.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Citrix ShareFile, suivez les étapes ci-dessous :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Citrix ShareFile**.

    ![Lien Citrix ShareFile dans la liste des applications](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la mosaïque Citrix ShareFile dans le volet d’accès, vous devez vous connecter automatiquement à votre application Citrix ShareFile.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png

