---
title: "Didacticiel : Intégration d’Azure Active Directory avec SSO SAML pour Bamboo de resolution GmbH | Documents Microsoft"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SSO SAML pour Bamboo de resolution GmbH."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: 3195dee2df560993e7885d138168e4514272f7eb
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Didacticiel : Intégration d’Azure Active Directory avec SSO SAML pour Bamboo de resolution GmbH

Dans ce didacticiel, vous allez apprendre à intégrer SSO SAML pour Bamboo de resolution GmbH avec Azure Active Directory (Azure AD).

L’intégration de SSO SAML pour Bamboo de resolution GmbH avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à SSO SAML pour Bamboo de resolution GmbH.
- Vous pouvez permettre à vos utilisateurs d’être automatiquement authentifiés auprès de SSO SAML pour Bamboo de resolution GmbH (authentification unique) avec leurs comptes Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec SSO SAML pour Bamboo de resolution GmbH, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une authentification unique SSO SAML pour Bamboo de resolution GmbH sur un abonnement activé

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SSO SAML pour Bamboo de resolution GmbH à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Ajout de SSO SAML pour Bamboo de resolution GmbH à partir de la galerie
Pour configurer l’intégration de SSO SAML Bamboo Jira de resolution GmbH dans Azure AD, vous devez ajouter SSO SAML pour Bamboo de resolution GmbH à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter SSO SAML pour Bamboo de resolution GmbH à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **SSO SAML pour Bamboo de resolution GmbH**, sélectionnez **SSO SAML pour Bamboo de resolution GmbH** dans le volet de résultats, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![SSO SAML pour Bamboo de resolution GmbH dans la liste des résultats](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec SSO SAML pour Bamboo de resolution GmbH en vous servant d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur SSO SAML pour Bamboo de resolution GmbH équivaut à un utilisateur dans Azure AD. En d’autres termes, un lien doit être établi entre un utilisateur d’Azure AD et l’utilisateur de SSO SAML pour Bamboo de resolution GmbH associé.

Dans SSO SAML pour Bamboo de resolution GmbH, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SSO SAML pour Bamboo de resolution GmbH, vous devez suivre les indications des blocs suivants :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un SSO SAML pour Bamboo de resolution GmbH](#create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** : pour avoir un équivalent de Britta Simon dans SSO SAML pour Bamboo de resolution GmbH qui soit lié à la représentation Azure AD de l’utilisateur.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SSO SAML pour Bamboo de resolution GmbH.

**Pour configurer l’authentification unique Azure AD avec SSO SAML pour Bamboo de resolution GmbH, procédez comme suit :**

1. Sur le portail Azure, sur la page intégration de l’application **SSO SAML pour Bamboo de resolution GmbH**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_samlbase.png)

3. Dans la section **Domaine et URL de SSO SAML pour Bamboo de resolution GmbH**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par IDP :

    ![informations d’authentification unique pour le domaine et les URL de SSO SAML pour Bamboo de resolution GmbH](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![informations d’authentification unique pour le domaine et les URL de SSO SAML pour Bamboo de resolution GmbH](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_url1.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support de SSO SAML pour Bamboo de resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support). 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Lien Téléchargement de certificat](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-bamboo-tutorial/tutorial_general_400.png)

7. Connectez-vous à votre site d’entreprise SSO SAML pour Bamboo de resolution GmbH en tant qu’administrateur.

8. À droite dans la barre d’outils principale, cliquez sur **Paramètres** > **Composants additionnels**.

    ![Les paramètres](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_setings.png)

9. Accédez à la section SÉCURITÉ, cliquez sur **SAML SingleSignOn** sur la barre de menus.

    ![Samlsingle](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_samlsingle.png)

10. Sur la **page Configuration du plug-in SAML SIngleSignOn**, cliquez sur **Ajouter idp**. 

    ![Ajout d’un fournisseur d’identité (idp)](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_addidp.png)

11. Sur la page **Choisir votre fournisseur d’identité SAML**, procédez comme suit :

    ![Fournisseur d’identité](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Sélectionnez **Type d’Idp** en tant qu’**AD AZURE**.

    b. Dans la zone de texte **Nom**, tapez le nom.

    c. Dans la zone de texte **Description**, tapez la description.

    d. Cliquez sur **Suivant**.

12. Sur la page **Configuration du fournisseur d’identité**, cliquez sur le bouton **Suivant**.

    ![La configuration de l’identité](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_identityconfig.png)

13.  Sur la page **Importer les métadonnées SAML Idp**, cliquez sur **Charger le fichier** pour charger le fichier **METADATA XML** que vous avez téléchargé à partir du portail Azure.

    ![idpmetadata](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

14. Cliquez sur **Suivant**.

15. Cliquez sur **Enregistrer les paramètres**.

    ![Enregistrement](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_save.png)
    
> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-bamboo-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-bamboo-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-bamboo-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-bamboo-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Création d’un utilisateur de test de SSO SAML pour Bamboo de resolution GmbH

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans SSO SAML pour Bamboo de resolution GmbH. SSO SAML pour Bamboo de resolution GmbH prend en charge l’approvisionnement juste-à-temps et des utilisateurs peuvent également être créés manuellement. Contactez [équipe de support SSO SAML pour Bamboo de resolution GmbH Client](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) en fonction de vos besoins.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon pour utiliser l’authentification unique Azure en accordant l’accès à SSO SAML pour Bamboo de resolution GmbH.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à SSO SAML pour Bamboo de resolution GmbH, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SSO SAML pour Bamboo de resolution GmbH**.

    ![La SSO SAML pour Bamboo de resolution GmbH a un lien dans la liste des applications](./media/active-directory-saas-bamboo-tutorial/tutorial_bamboo_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SSO SAML pour Bamboo de resolution GmbH dans le panneau d’accès, vous devez être automatiquement authentifiés auprès de votre application SSO SAML pour Bamboo de resolution GmbH.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bamboo-tutorial/tutorial_general_203.png

