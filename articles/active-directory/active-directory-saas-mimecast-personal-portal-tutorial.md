---
title: "Didacticiel : Intégration d’Azure Active Directory avec Mimecast Personal Portal | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mimecast Personal Portal."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: jeedes
ms.openlocfilehash: 4f2c5f7323d9d10b6a784da8f45577ccf774b78f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Didacticiel : Intégration d’Azure Active Directory avec Mimecast Personal Portal

Dans ce didacticiel, vous allez apprendre à intégrer Mimecast Personal Portal avec Azure Active Directory (Azure AD).

L’intégration de Mimecast Personal Portal avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Mimecast Personal Portal.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Mimecast Personal Portal (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec Mimecast Personal Portal, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Mimecast Personal Portal pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Mimecast Personal Portal à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Ajout de Mimecast Personal Portal à partir de la galerie
Pour configurer l’intégration de Mimecast Personal Portal à Azure AD, vous devez ajouter Mimecast Personal Portal à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter Mimecast Personal Portal à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Mimecast Personal Portal**, sélectionnez **Mimecast Personal Portal** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Mimecast Personal Portal dans la liste des résultats](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Mimecast Personal Portal sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Mimecast Personal Portal équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Mimecast Personal Portal associé doit être établie.

Dans Mimecast Personal Portal, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Mimecast Personal Portal, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créez un utilisateur de test Mimecast Personal Portal](#create-a-mimecast-personal-portal-test-user)** pour obtenir un équivalent de Britta Simon dans Mimecast Personal Portal lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Mimecast Personal Portal.

**Pour configurer l’authentification unique Azure AD avec Mimecast Personal Portal, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Mimecast Personal Portal**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. Dans la section **Domaine et URL Mimecast Personal Portal**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. Dans la zone de texte **URL d’authentification**, tapez l’URL : 

    | Région  |  Valeur | 
    | --------------- | --------------- | 
    | Europe          | `https://eu-api.mimecast.com/login/saml`|
    | États-Unis   | `https://us-api.mimecast.com/login/saml`|
    | Afrique du Sud    | `https://za-api.mimecast.com/login/saml`|
    | Australie       | `https://au-api.mimecast.com/login/saml`|
    | Offshore        | `https://jer-api.mimecast.com/login/saml`|

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant :

    | Région  |  Valeur | 
    | --------------- | --------------- |
    | Europe          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | États-Unis   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Afrique du Sud    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australie       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Offshore        | `https://jer-api.mimecast.com/sso/<accountcode>`|
    
    > [!NOTE] 
    > La valeur de l'identificateur n'est pas réelle. Mettez à jour cette valeur avec l’identificateur réel. Pour obtenir la valeur, contactez l’[équipe de support technique Mimecast Personal Portal](http://www.mimecast.com/customer-success/technical-support/). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Mimecast Personal Portal**, cliquez sur **Configurer Mimecast Personal Portal** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration Mimecast Personal Portal](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Mimecast Personal Portal en tant qu’administrateur.

8. Accédez à **Services \> Application**.
   
    ![Applications](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794998.png "Applications")

9. Cliquez sur **Authentication Profiles**.
   
    ![Profils d’authentification](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794999.png "Profils d’authentification")

10. Cliquez sur **New Authentication Profile**.
   
    ![Nouveau profil d’authentification](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795000.png "Nouveau profil d’authentification")

11. Dans la section **Authentication Profile** , procédez comme suit :
   
    ![Profil d’authentification](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795001.png "Profil d’authentification")
   
    a. Dans la zone de texte **Description** , indiquez un nom pour votre configuration.
   
    b. Sélectionnez **Enforce SAML Authentication for Mimecast Personal Portal**.
   
    c. Pour **Provider**, sélectionnez **Azure Active Directory**.
   
    d. Dans la zone de texte **Issuer URL (URL de l’émetteur)**, collez la valeur **SAML Entity ID (ID d’entité SAML)** que vous avez copiée à partir du portail Azure.
   
    e. Dans la zone de texte **Login URL (URL de connexion)**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.
   
    f. Dans la zone de texte **Logout URL (URL de déconnexion)**, collez la valeur **URL de déconnexion** que vous avez copiée à partir du portail Azure.

    g. Ouvrez votre certificat codé **en base 64** dans le bloc-notes téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **Identity Provider Certificate (Metadata)** (Certificat de fournisseur d’identité (métadonnées)).

    h. Sélectionnez **Allow Single Sign On**.
   
    i. Cliquez sur **Save**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Créer un utilisateur de test Mimecast Personal Portal

Pour permettre aux utilisateurs Azure AD de se connecter à Mimecast Personal Portal, vous devez les approvisionner dans Mimecast Personal Portal. Dans le cas de Mimecast Personal Portal, l’approvisionnement est une tâche manuelle.

Vous devez enregistrer un domaine avant de pouvoir créer des utilisateurs.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à **Mimecast Personal Portal** en tant qu’administrateur.

2. Accédez à **Directories \> Internal**.
   
    ![Répertoires](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795003.png "Répertoires")

3. Cliquez sur **Register New Domain**.
   
    ![Enregistrer un nouveau domaine](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795004.png "Enregistrer un nouveau domaine")

4. Après avoir créé votre domaine, cliquez sur **New Address**.
   
    ![Nouvelle adresse](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795005.png "Nouvelle adresse")

5. Dans la boîte de dialogue Nouvelle Adresse, procédez comme suit pour un compte Azure AD valide que vous souhaitez approvisionner :
   
    ![Enregistrer](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795006.png "enregistrer")
   
    a. Dans la zone de texte **Adresse e-mail**, entrez l’**adresse e-mail** de l’utilisateur. Ici, il s’agit de **BrittaSimon@contoso.com**.
    
    b. Dans la zone de texte **Nom global**, tapez le **nom d’utilisateur** sous la forme **BrittaSimon**.

    c. Dans les zones de texte **Mot de passe** et **Confirmer le mot de passe**, type de le **mot de passe** de l’utilisateur.
   
    b. Cliquez sur **Enregistrer**.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou API de création de compte d’utilisateur fourni par Mimecast Personal Portal pour approvisionner des comptes d’utilisateur Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mimecast Personal Portal.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Mimecast Personal Portal, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Mimecast Personal Portal**.

    ![Lien Mimecast Personal Portal dans la liste des applications](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Mimecast Personal Portal dans le panneau d’accès, vous devez être connecté automatiquement à votre application Mimecast Personal Portal.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_203.png

