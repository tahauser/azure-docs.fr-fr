---
title: 'Didacticiel : intégration d’Azure Active Directory à Andromeda | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.openlocfilehash: 7e2a140ba6dc4825283801ed4f3435136b307153
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2018
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Didacticiel : intégration d’Azure Active Directory à Andromeda

Dans ce didacticiel, vous apprenez à intégrer Andromeda à Azure Active Directory (Azure AD).

L’intégration d’Andromeda à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Andromeda.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Andromeda (via l’authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à Andromeda, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Andromeda pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Andromeda à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-andromeda-from-the-gallery"></a>Ajout d’Andromeda à partir de la galerie
Pour configurer l’intégration d’Andromeda à Azure AD, vous devez ajouter Andromeda, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Andromeda à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, saisissez**Andromeda**, sélectionnez **Andromeda** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Andromeda dans la liste des résultats](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Andromeda à l’aide d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Andromeda équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Andromeda associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Andromeda, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Andromeda](#create-an-andromeda-test-user)** pour avoir un équivalent de Britta Simon dans Andromeda, associé à sa représentation dans Azure AD.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Andromeda.

**Pour configurer l’authentification unique Azure AD avec Andromeda, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Andromeda**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. Dans la section **Domaine et URL Andromeda**, suivez les étapes ci-dessous pour configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Andromeda](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<tenantURL>.ngcxpress.com/`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Andromeda](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`
     
    > [!NOTE] 
    > La valeur ci-dessus n’est pas une valeur réelle. Vous mettrez à jour la valeur avec l’identificateur, l’URL de réponse et l’URL de connexion réels. La procédure est expliquée plus loin dans le didacticiel.

5. L’application Andromeda attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section **Attributs utilisateur** sur la page d’intégration des applications. La capture d’écran suivante montre un exemple :
    
    ![Configurer l’authentification unique attb](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Effacez les définitions NameSpace lorsque vous configurez celles-ci.
    
6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | -------------- | -------------------- |    
    | role        | Rôle spécifique aux applications |
    | Type        | Type d'application |
    | société       | CompanyName    |

    > [!NOTE]
    > Il ne s’agit pas de valeurs réelles. Ces valeurs sont fournies uniquement à des fins de démonstration ; veuillez utiliser les rôles de votre organisation.

    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique Add](./media/active-directory-saas-andromedascm-tutorial/tutorial_attribute_04.png)

    ![Configurer l’authentification unique Addattb](./media/active-directory-saas-andromedascm-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    e. Cliquez sur **OK**.

7. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-andromedascm-tutorial/tutorial_general_400.png)
    
9. Dans la section **Configuration d’Andromeda**, cliquez sur **Configurer Andromeda** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration d’Andromeda](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Connectez-vous à votre site d’entreprise Andromeda en tant qu’administrateur.

11. En haut de la barre de menus, cliquez sur **Admin** et accédez à **Administration**.

    ![Administrateur Andromeda](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. Sur le côté gauche de la barre d’outils, dans la section **Interfaces**, cliquez sur **SAML Configuration** (Configuration SAML).

    ![SAML Andromeda](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. Dans la section **SAML Configuration** (Configuration SAML), procédez comme suit :

    ![Configuration d’Andromeda](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Cochez **Enable SSO with SAML** (Activer l’authentification unique avec SAML).

    b. Dans la section **Informations Andromeda**, copiez la valeur **Identité SP** et collez-la dans la zone de texte **Identificateur** de la section **Domaine et URL Andromeda**.

    c. Copiez la valeur **URL du consommateur** et collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL Andromeda SCM**.

    d. Copiez la valeur **URL de connexion** et collez-la dans la zone de texte **URL de connexion** de la section **Domaine et URL Andromeda**.

    e. Dans la section **Fournisseur d’identité SAML**, tapez votre nom IDP.

    f. Dans la zone de texte **Single Sign On End Point** (Point de terminaison d’authentification unique), collez la valeur de **SAML Single Sign-On Service URL** (URL du service d’authentification unique SAML) que vous avez copiée à partir du portail Azure.

    g. Ouvrez le **certificat codé en base 64** téléchargé à partir du portail Azure dans le bloc-notes et collez-le dans la zone de texte **Certificat X.509**.
    
    h. Mappez les attributs suivants avec la valeur correspondante pour faciliter la connexion avec authentification unique à partir d’Azure AD. L’attribut **ID utilisateur** est requis pour la connexion. Pour l’approvisionnement, **E-mail**, **Société**, **Type d’utilisateur** et **Rôle** sont requis. Dans cette section, nous définissons le mappage des attributs (nom et valeurs) qui correspondent à ceux définis dans le portail Azure

    ![Andromeda attbmap](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-an-andromeda-test-user"></a>Créer un utilisateur de test Andromeda

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Andromeda. Andromeda prend en charge l’approvisionnement juste à temps, activé par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas déjà, un nouvel utilisateur est créé durant une tentative d’accès à Andromeda.

>[!Note]
>Si vous devez créer un utilisateur manuellement, contactez [l’équipe d’assistance client Andromeda](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Andromeda.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Andromeda, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Andromeda**.

    ![Le lien Andromeda dans la liste des applications](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Andromeda dans le volet d’accès, vous devez être connecté automatiquement à votre application Andromeda.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_203.png
