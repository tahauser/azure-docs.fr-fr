---
title: "Didacticiel : Intégration d’Azure Active Directory à Palo Alto Networks - Aperture | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Palo Alto Networks - Aperture."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 75633cbf13756b4b2be3e4be055b12021cc396d2
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2018
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Didacticiel : Intégration d’Azure Active Directory à Palo Alto Networks - Aperture

Dans ce didacticiel, vous découvrez comment intégrer Palo Alto Networks - Aperture avec Azure Active Directory (Azure AD).

L’intégration de Palo Alto Networks - Aperture avec Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Palo Alto Networks - Aperture.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Palo Alto Networks - Aperture (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD avec Palo Alto Networks - Aperture, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Palo Alto Networks - Aperture pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Palo Alto Networks - Aperture depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Ajout de Palo Alto Networks - Aperture depuis la galerie
Pour configurer l’intégration de Palo Alto Networks - Aperture à Azure AD, vous devez ajouter Palo Alto Networks - Aperture à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Palo Alto Networks - Aperture à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Palo Alto Networks - Aperture**, sélectionnez **Palo Alto Networks - Aperture** (Palo Alto Networks - Aperture) dans le volet des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Palo Alto Networks - Aperture dans la liste des résultats](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et vous testez l’authentification unique Azure AD avec Palo Alto Networks - Aperture sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Palo Alto Networks - Aperture équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur Palo Alto Networks - Aperture associé.

Pour configurer et tester l’authentification unique Azure AD avec Palo Alto Networks - Aperture, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créez un utilisateur de test Palo Alto Networks - Aperture](#create-a-palo-alto-networks---aperture-test-user)** pour obtenir un équivalent de Britta Simon dans Palo Alto Networks - Aperture lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et vous configurez l’authentification unique dans votre application Palo Alto Networks - Aperture.

**Pour configurer l’authentification unique Azure AD avec Palo Alto Networks - Aperture, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Palo Alto Networks - Aperture**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

3. Dans la section **Palo Alto Networks - Aperture Domain and URLs** (Domaine et URL - Palo Alto Networks - Aperture), suivez les étapes ci-dessous pour configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Palo Alto Networks - Aperture Domain and URLs (Domaine et URL - Palo Alto Networks - Aperture)](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Palo Alto Networks - Aperture Domain and URLs (Domaine et URL - Palo Alto Networks - Aperture)](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Contactez [l’équipe de support client de Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) pour obtenir ces valeurs. 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


7. Dans la section **Palo Alto Networks - Aperture Configuration** (Configuration de Palo Alto Networks - Aperture), cliquez sur **Configure Palo Alto Networks - Aperture** (Configurer Palo Alto Networks - Aperture) pour ouvrir la fenêtre **Configurer l’authentification unique**. Copiez **l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Lien de configuration](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

8. Dans une autre fenêtre de navigateur web, connectez-vous à Palo Alto Networks - Aperture en tant qu’administrateur.

9. Dans la barre de menus supérieure, cliquez sur **PARAMÈTRES**.

    ![Onglet de paramètres](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

10. Accédez à la section **APPLICATION** et cliquez sur **Authentification** dans le côté gauche du menu.

    ![Onglet d’authentification](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
11. Sur la page **Authentification**, procédez comme suit :
    
    ![Onglet d’authentification](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Cochez l’option **Enable Single Sign-On(Supported SSP Providers are Okta, Onelogin)** (Activer l’authentification unique pour ce réseau (Les fournisseurs SSP pris en charge sont Okta, Onelogin)) dans le champ **Authentification unique**.

    b. Dans la zone de texte **ID du fournisseur d’identité**, collez la valeur de **l’ID d’entité SAML** copiée à partir du portail Azure.

    c. Cliquez sur **Choisir un fichier** pour charger le certificat obtenu à partir d’Azure AD dans le champ **Certificat du fournisseur d’identité**.

    d. Dans la zone de texte **URL d’authentification unique du fournisseur d’identité**, collez la valeur **URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.

    e. Passez en revue les informations des fournisseurs d’identité dans la section **Aperture Info** (Informations Aperture) et téléchargez le certificat à partir du champ **Aperture Key** (Clé Aperture).

    f. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Créer un utilisateur de test Palo Alto Networks - Aperture

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Palo Alto Networks - Aperture. Collaborez avec [l’équipe de support client de Palo Alto Networks - Aperture](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) pour ajouter les utilisateurs à la plateforme Palo Alto Networks - Aperture. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Palo Alto Networks - Aperture.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Palo Alto Networks - Aperture, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Palo Alto Networks - Aperture** (Palo Alto Networks - Aperture).

    ![Lien Palo Alto Networks - Aperture dans la liste des applications](./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Palo Alto Networks - Aperture dans le panneau d’accès, vous êtes normalement connecté automatiquement à votre application Palo Alto Networks - Aperture.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-paloaltonetworks-aperture-tutorial/tutorial_general_203.png

