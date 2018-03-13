---
title: "Didacticiel : Intégration d’Azure Active Directory à Central Desktop | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Central Desktop."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 94c67bef7a0c6ba60fc9c7a60c79a23bf7984fb1
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/21/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Didacticiel : Intégration d’Azure Active Directory à Central Desktop

Dans ce didacticiel, vous allez apprendre à intégrer Central Desktop à Azure Active Directory (Azure AD).

L’intégration de Central Desktop à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Central Desktop
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Central Desktop avec leur compte Azure AD
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour plus d’informations sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Central Desktop, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Central Desktop pour lequel l’authentification unique est activée

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production pour tester les étapes de ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas encore d’environnement d’essai Azure AD, obtenez [un essai gratuit d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Central Desktop à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-central-desktop-from-the-gallery"></a>Ajouter Central Desktop à partir de la galerie
Pour configurer l’intégration de Central Desktop à Azure AD, vous devez ajouter Central Desktop à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Central Desktop à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Central Desktop**. Dans le panneau de résultats, sélectionnez **Central Desktop**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Central Desktop dans la liste des résultats](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Central Desktop pour un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Central Desktop équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Central Desktop associé.

Dans Central Desktop, donnez à la valeur **Username** la même valeur que **Nom d’utilisateur** dans Azure AD. Vous avez maintenant établi le lien entre les deux utilisateurs.

Pour configurer et tester l’authentification unique Azure AD avec Central Desktop, vous devez effectuer les actions essentielles suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test Central Desktop](#create-a-central-desktop-test-user) pour avoir un équivalent de Britta Simon dans Central Desktop lié à la représentation Azure AD de l’utilisateur.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Central Desktop.

**Pour configurer l’authentification unique Azure AD avec Central Desktop, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Central Desktop**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Pour activer l’authentification unique, dans la boîte de dialogue **Authentification unique**, dans la zone de liste déroulante **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

3. Dans la section **Domaine et URL Central Desktop**, effectuez les étapes suivantes :

    ![Informations d’authentification unique : domaine et URL Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. Dans la zone **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.centraldesktop.com`

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant :
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. Dans la zone **URL de réponse**, tapez une URL au format suivant : `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Central Desktop](https://imeetcentral.com/contact-us). 

4. Dans la section **Certificat de signature SAML**, sélectionnez **Certificat**. Ensuite, enregistrez le fichier de certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

5. Sélectionnez le bouton **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-central-desktop-tutorial/tutorial_general_400.png)
    
6. Dans la section **Configuration de Central Desktop**, sélectionnez **Configurer Central Desktop** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la section **Référence rapide**.

    ![Configuration de Central Desktop](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

7. Connectez-vous à votre locataire **Central Desktop**.

8. Accédez à **Settings**. Sélectionnez **Advanced**, puis **Single Sign On**.

    ![Setup - Advanced](./media/active-directory-saas-central-desktop-tutorial/ic769563.png "Setup - Advanced")

9. Dans la page **Single Sign On Settings**, effectuez les étapes suivantes :

    ![Single sign-on settings](./media/active-directory-saas-central-desktop-tutorial/ic769564.png "Single Sign On Settings")
    
    a. Sélectionnez **Enable SAML v2 Single Sign On**.
    
    b. Dans la zone **SSO URL**, collez la valeur **ID d’entité SAML** copiée à partir du portail Azure.
    
    c. Dans la zone **SSO Login URL**, collez la valeur **URL du service d’authentification unique SAML** copiée à partir du portail Azure.
    
    d. Dans la zone **SSO Logout URL**, collez la valeur **URL de déconnexion** copiée à partir du portail Azure.

10. Dans la section **Message Signature Verification Method**, effectuez les étapes suivantes :

    ![Message signature verification method](./media/active-directory-saas-central-desktop-tutorial/ic769565.png "Message Signature Verification Method") a. Sélectionnez **Certificate**.
    
    b. Dans la liste **SSO Certificate**, sélectionnez **RSH SHA256**.
    
    c. Ouvrez votre certificat téléchargé dans le Bloc-notes. Ensuite, copiez le contenu du certificat et collez-le dans le champ **SSO Certificate**.
        
    d. Sélectionnez **Display a link to your SAMLv2 login page**.
    
    e. Sélectionnez **Update**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée en consultant la [documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, effectuez les étapes suivantes :**

1. Dans le volet gauche du portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**. Puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-central-desktop-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-central-desktop-test-user"></a>Créer un utilisateur test Central Desktop

Pour que les utilisateurs Azure AD puissent se connecter, ils doivent être provisionnés dans l’application Central Desktop. Cette section explique comment créer des comptes d’utilisateur Azure AD dans Central Desktop.

> [!NOTE]
> Pour provisionner des comptes d’utilisateur Azure AD, vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Central Desktop.

**Pour approvisionner des comptes d’utilisateur dans Central Desktop :**

1. Connectez-vous à votre locataire Central Desktop.

2. Accédez à **People** > **Internal Members**.

3. Sélectionnez **Add Internal Members**.

    ![Personnes](./media/active-directory-saas-central-desktop-tutorial/ic781051.png "Personnes")
    
4. Dans la zone de texte **Email Address of New Members**, tapez un compte Azure AD à provisionner, puis sélectionnez **Next**.

    ![Email addresses of new members](./media/active-directory-saas-central-desktop-tutorial/ic781052.png "Email addresses of new members")

5. Sélectionnez **Add Internal member(s)**.

    ![Add internal member](./media/active-directory-saas-central-desktop-tutorial/ic781053.png "Add internal member")
   
   >[!NOTE]
   >Les utilisateurs que vous ajoutez reçoivent un e-mail contenant un lien de confirmation pour l’activation de leur compte.
   
### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Central Desktop.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Central Desktop, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications. Accédez à la vue d’annuaire, puis à **Applications d’entreprise**.

2. Sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Central Desktop**.

    ![Lien Central Desktop dans la liste des applications](./media/active-directory-saas-central-desktop-tutorial/tutorial_centraldesktop_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**.

7. Dans la boîte de dialogue **Ajouter une attribution**, sélectionnez le bouton **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette Central Desktop dans le volet d’accès, vous êtes connecté automatiquement à votre application Central Desktop.
Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-central-desktop-tutorial/tutorial_general_203.png

