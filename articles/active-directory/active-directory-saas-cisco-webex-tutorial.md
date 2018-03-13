---
title: "Didacticiel : Intégration d’Azure Active Directory à Cisco Webex | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Cisco Webex."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory7
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 42632dcf8997ec5e987ac8a6615aae24e903399a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/16/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Didacticiel : Intégration d’Azure Active Directory à Cisco Webex

Ce didacticiel explique comment intégrer Cisco Webex avec Azure Active Directory (Azure AD).

L’intégration de Cisco Webex avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Cisco Webex.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Cisco Webex avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure

Pour plus d’informations sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Cisco Webex, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Cisco Webex pour lequel l’authentification unique est activée

> [!NOTE]
> Nous déconseillons l’utilisation d’un environnement de production pour tester les étapes de ce didacticiel.

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous ne disposez pas d’un environnement d’essai Azure AD, vous pouvez [obtenir un essai gratuit d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Cisco Webex à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-cisco-webex-from-the-gallery"></a>Ajouter Cisco Webex à partir de la galerie
Pour configurer l’intégration de Cisco Webex à Azure AD, vous devez ajouter Cisco Webex, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Cisco Webex à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une nouvelle application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Cisco Webex**. 

5. Sélectionnez **Cisco Webex** dans le volet de résultats. Sélectionnez ensuite le bouton **Ajouter** pour ajouter l’application.

    ![Cisco Webex dans la liste des résultats](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Cisco Webex sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Cisco Webex équivalent dans Azure AD. En d’autres termes, vous devez établir un lien entre un utilisateur Azure AD et l’utilisateur Cisco Webex associé.

Dans Cisco Webex, donnez à la valeur **Username** la même valeur que **Nom d’utilisateur** dans Azure AD. Vous avez maintenant établi le lien entre les deux utilisateurs. 

Pour configurer et tester l’authentification unique Azure AD avec Cisco Webex, suivez les indications des sections suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test Cisco Webex](#create-a-cisco-webex-test-user) pour avoir un équivalent de Britta Simon dans Cisco Webex, lié à la représentation Azure AD associée.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier que la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Cisco Webex.

**Pour configurer l’authentification unique Azure AD avec Cisco Webex, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Cisco Webex**, sélectionnez **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Pour activer l’authentification unique, dans la boîte de dialogue **Authentification unique**, dans la liste déroulante **Mode**, sélectionnez **Authentification basée sur SAML**.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. Dans la section **Domaine et URL Cisco Webex**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    a. Dans la zone **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.webex.com`

    b. Dans la zone **Identificateur**, tapez l’URL `http://www.webex.com`.

    c. Dans la zone **URL de réponse**, tapez une URL au format suivant : `https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez [l’équipe du support client Cisco Webex](https://www.webex.co.in/support/support-overview.html). 

5. Dans la section **Certificat de signature SAML**, sélectionnez **Métadonnées XML**, puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

6. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_400.png)
    
6. Dans la section **Configuration de Cisco Webex**, sélectionnez **Configurer Cisco Webex** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**URL de déconnexion**, l’**ID d’entité SAML** et l’**URL du service d’authentification unique SAML** à partir de la section **Référence rapide**.

    ![Configure Single Sign-On](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Cisco Webex en tant qu’administrateur.

8. Dans le menu situé en haut, sélectionnez **Site Administration**.

    ![Site Administration](./media/active-directory-saas-cisco-webex-tutorial/ic777621.png "Site Administration")

9. Dans la section **Manage Site**, sélectionnez **SSO Configuration**.
   
    ![SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/ic777622.png "SSO Configuration")

10. Dans la section **Federated Web SSO Configuration**, effectuez les étapes suivantes :
   
    ![Configuration de l’authentification unique web fédérée](./media/active-directory-saas-cisco-webex-tutorial/ic777623.png "Configuration de l’authentification unique web fédérée")  

    a. Dans la liste **Federation Protocol**, sélectionnez **SAML 2.0**.

    b. Pour **SSO profile**, sélectionnez **SP Initiated**.

    c. Ouvrez votre certificat téléchargé dans le Bloc-notes, puis copiez le contenu.

    d. Sélectionnez **Import SAML Metadata**, puis collez le contenu copié du certificat.

    e. Dans la zone **Issuer for SAML (IdP ID)**, collez la valeur de **l’ID d’entité SAML** que vous avez copiée à partir du portail Azure.

    f. Dans la zone de texte **Customer SSO Service Login URL**, collez **l’URL du service d’authentification unique SAML** copiée à partir du portail Azure.

    g. Dans la liste **NameID Format**, sélectionnez **Email address**.

    h. Dans la zone **AuthnContextClassRef**, tapez **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**.

    i. Dans la zone **Customer SSO Service Logout URL**, collez **l’URL du service de déconnexion** copiée à partir du portail Azure.
   
    j. Sélectionnez **Update**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, sélectionnez l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée en consultant la [documentation incorporée Azure AD](https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis sélectionnez **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, effectuez les étapes suivantes :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-cisco-webex-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-cisco-webex-test-user"></a>Créer un utilisateur de test Cisco Webex

Pour permettre aux utilisateurs Azure AD de se connecter à Cisco Webex, vous devez les provisionner dans Cisco Webex. En l’occurrence, cet approvisionnement est une tâche manuelle.

**Pour provisionner un compte d’utilisateur, effectuez les étapes suivantes :**

1. Connectez-vous à votre locataire **Cisco Webex**.

2. Accédez à **Manage Users** > **Add User**.
   
    ![Ajouter des utilisateurs](./media/active-directory-saas-cisco-webex-tutorial/ic777625.png "Ajouter des utilisateurs")

3. Dans la section **Add User**, effectuez les étapes suivantes :
   
    ![Ajouter un utilisateur](./media/active-directory-saas-cisco-webex-tutorial/ic777626.png "Ajouter un utilisateur")   

    a. Pour **Account Type**, sélectionnez **Host**.

    b. Dans la zone **First name**, entrez le prénom de l’utilisateur (ici, **Britta**).

    c. Dans la zone **Last name**, entrez le nom de l’utilisateur (ici, **Simon**).

    d. Dans la zone **Username**, entrez l’e-mail de l’utilisateur (ici, **Brittasimon@contoso.com**).

    e. Dans la zone **Email**, entrez l’adresse e-mail de l’utilisateur (ici, **Brittasimon@contoso.com**).

    f. Dans la zone **Password**, entrez le mot de passe de l’utilisateur.

    g. Dans la zone **Confirm**, entrez de nouveau votre mot de passe.

    h. Sélectionnez **Add**.

>[!NOTE]
>Vous pouvez utiliser tout autre outil ou n’importe quelle API de création de compte d’utilisateur fournis par Cisco Webex pour provisionner des comptes d’utilisateur Azure AD. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser l’utilisateur Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Cisco Webex.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Cisco Webex, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications. Ensuite, accédez à la vue d’annuaire, puis à **Applications d’entreprise**.  

2. Sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

3. Dans la liste des applications, sélectionnez **Cisco Webex**.

    ![Lien Cisco Webex dans la liste des applications](./media/active-directory-saas-cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez le bouton **Ajouter**. Ensuite, dans la boîte de dialogue **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, cliquez sur le bouton **Sélectionner**.

7. Cliquez sur le bouton **Attribuer** dans la boîte de dialogue **Ajouter une attribution**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous sélectionnez la vignette Cisco Webex dans le volet d’accès, vous êtes connecté automatiquement à votre application Cisco Webex.

Pour plus d’informations sur le volet d’accès, consultez la page [Présentation du volet d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-webex-tutorial/tutorial_general_203.png

