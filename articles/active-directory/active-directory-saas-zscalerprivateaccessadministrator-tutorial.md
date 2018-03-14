---
title: "Didacticiel : Intégration d’Azure Active Directory à Zscaler Private Access Administrator | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Zscaler Private Access Administrator."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: bf0b7cbd8047dfdbc1a4503775e6d36f8e8a67c1
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Didacticiel : Intégration d’Azure Active Directory à Zscaler Private Access Administrator

Dans ce didacticiel, vous allez apprendre à intégrer Zscaler Private Access Administrator à Azure Active Directory (Azure AD).

L’intégration de Zscaler Private Access Administrator dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Zscaler Private Access Administrator.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Zscaler Private Access Administrator (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Zscaler Private Access Administrator, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Zscaler Private Access Administrator activé via l’authentification unique (SSO)

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Zscaler Private Access Administrator à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Ajout de Zscaler Private Access Administrator à partir de la galerie
Pour configurer l’intégration de Zscaler Private Access Administrator à Azure AD, vous devez ajouter Zscaler Private Access Administrator à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Zscaler Private Access Administrator à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Zscaler Private Access Administrator**, sélectionnez **Zscaler Private Access Administrator** dans le panneau des résultats puis cliquez sur le bouton **Ajouter** de l’application.

    ![Zscaler Private Access Administrator dans la liste des résultats](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Zscaler Private Access Administrator avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Zscaler Private Access Administrator équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Zscaler Private Access Administrator associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Zscaler Private Access Administrator, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Zscaler Private Access Administrator](#create-a-zscaler-private-access-administrator-test-user)** pour avoir un équivalent de Britta Simon dans Zscaler Private Access Administrator lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application Zscaler Private Access Administrator.

**Pour configurer l’authentification unique Azure AD avec Zscaler Private Access Administrator, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Zscaler Private Access Administrator**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

3. Dans la section **Domaine et URL Zscaler Private Access Administrator**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Informations d’authentification unique dans Domaine et URL Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Cochez **Afficher les paramètres d’URL avancés**.

    d. Dans la zone de texte **RelayState**, tapez une valeur : `idpadminsso`

4.  Si vous voulez configurer l’application en **mode lancé par le fournisseur de service**, procédez comme suit :

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Contactez [l’équipe de support technique Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket) pour obtenir ces valeurs.
 
5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Zscaler Private Access Administrator en tant qu’administrateur.

8. En haut, cliquez sur **Administration** et allez à la section **AUTHENTIFICATION** puis cliquez sur **Configuration IdP**.

    ![Administrateur Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

9. Dans le coin supérieur droit, cliquez sur **Ajouter une configuration IdP**. 

    ![Ajouter IdP Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

10. Sur la **page Ajouter une configuration IdP**, procédez comme suit :
 
    ![Zscaler Private Access Administrator idpselect](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Cliquez sur **Sélectionner fichier** pour charger le fichier de métadonnées téléchargé depuis Azure AD dans le champ **Charger fichier de métadonnée IdP**.

    b. Les **métadonnées IdP** sont lu à partir d’Azure AD et tous les champs sont renseignés, comme montré ci-dessous.

    ![Zscaler Private Access Administrator idpconfig](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Sélectionnez **Authentification unique** comme **Administrateur**.

    d. Sélectionnez votre domaine dans le champ **Domaines**.
    
    e. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Créer un utilisateur test Zscaler Private Access Administrator

Pour permettre à des utilisateurs Azure AD de se connecter à Zscaler Private Access Administrator, ils doivent être configurés dans Zscaler Private Access Administrator. Dans le cas de Zscaler Private Access Administrator, la configuration est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous au site de la société Zscaler Private Access Administrator en tant qu’administrateur.

2. En haut, cliquez sur **Administration** et allez à la section **AUTHENTIFICATION** puis cliquez sur **Configuration IdP**.

    ![Administrateur Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Cliquez sur **Administrateurs** à gauche du menu.

    ![Administrateur Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. Dans le coin supérieur droit, cliquez sur **Ajouter un administrateur** :

    ![Ajouter un administrateur Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Dans la page **Ajouter un administrateur**, procédez comme suit :

    ![Administrateur d’utilisateurs Zscaler Private Access Administrator](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Dans la zone de texte **Nom d’utilisateur**, entrez l’adresse e-mail de l’utilisateur, par exemple **BrittaSimon@contoso.com**.

    b. Dans la zone de texte **Mot de passe**, tapez le mot de passe.

    c. Dans la zone de texte **Confirmer le mot de passe**, entrez le mot de passe.

    d. Sélectionnez **Rôle** pour **Zscaler Private Access Administrator**.

    e. Dans la zone de texte **E-mail**, entrez l’adresse de messagerie de l’utilisateur, par exemple **BrittaSimon@contoso.com**.

    f. Dans la zone de texte **Téléphone**, tapez le numéro de téléphone.

    g. Dans la zone de texte **Fuseau horaire**, sélectionnez le fuseau horaire.

    h. Cliquez sur **Enregistrer**.  

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Zscaler Private Access Administrator.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Zscaler Private Access Administrator, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Zscaler Private Access Administrator**.

    ![Lien Zscaler Private Access Administrator dans la liste des applications](./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Zscaler Private Access Administrator dans le volet d’accès, vous êtes connecté automatiquement à votre application Zscaler Private Access Administrator.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

