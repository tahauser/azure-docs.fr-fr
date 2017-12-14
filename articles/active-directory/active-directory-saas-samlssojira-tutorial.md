---
title: "Didacticiel : Intégration d’Azure Active Directory avec SSO SAML pour Jira par résolution GmbH | Documents Microsoft"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et SSO SAML pour Jira de resolution GmbH."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 3a3224acd2376efca19a29576980b6a3ca6a9e99
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Didacticiel : Intégration d’Azure Active Directory avec SSO SAML pour Jira par résolution GmbH

Dans ce didacticiel, vous allez apprendre à intégrer SSO SAML pour Jira de resolution GmbH avec Azure Active Directory (Azure AD).

L’intégration de SSO SAML pour Jira de resolution GmbH avec Azure AD offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à SSO SAML pour Jira de resolution GmbH
- Vous pouvez permettre à vos utilisateurs d’être automatiquement authentifiés auprès de SSO SAML pour Jira de resolution GmbH (authentification unique) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec SSO SAML pour Jira de resolution GmbH, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Une authentification unique SSO SAML pour Jira de resolution GmbH sur un abonnement activé

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SSO SAML pour Jira de resolution GmbH à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Ajout de SSO SAML pour Jira de resolution GmbH à partir de la galerie
Pour configurer l’intégration de SSO SAML pour Jira de resolution GmbH dans Azure AD, vous devez ajouter SSO SAML pour Jira de resolution GmbH à partir de la galerie dans votre liste d’applications SaaS gérées.

**Pour ajouter SSO SAML pour Jira de resolution GmbH à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la boîte de dialogue de recherche, tapez **SSO SAML pour Jira de resolution GmbH**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_search.png)

5. Dans le volet des résultats, sélectionnez **SSO SAML pour Jira de resolution GmbH**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SSO SAML pour Jira de resolution GmbH, en tirant parti d’un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir quel utilisateur SSO SAML pour Jira de resolution GmbH équivaut à un utilisateur dans Azure AD. En d’autres termes, un lien doit être établi entre un utilisateur Azure AD et l’utilisateur de SSO SAML pour Jira de resolution GmbH associé.

Dans SSO SAML pour Jira de resolution GmbH, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SSO SAML pour Jira de resolution GmbH, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test de SSO SAML pour Jira de resolution GmbH](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)**: pour avoir un équivalent de Britta Simon dans SSO SAML pour Jira de resolution GmbH qui soit lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SSO SAML pour Jira de resolution GmbH.

**Pour configurer l’authentification unique Azure AD avec SSO SAML pour Jira de resolution GmbH, procédez comme suit :**

1. Sur le portail Azure, dans la page intégration de l’application **SSO SAML pour Jira de resolution GmbH**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

3. Dans la section **SSO SAML pour Jira de resolution GmbH**, si vous souhaitez configurer l’application en mode initié par le **fournisseur d’identité (IDP)**:

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`

4. Cliquez sur **Afficher les paramètres d’URL avancés**. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service** :

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support de SSO SAML pour Jira de resolution GmbH](https://www.resolution.de/go/support). 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/tutorial_general_400.png)
    
7. Dans une autre fenêtre de navigateur web, connectez-vous à votre **portail d’administration de SSO SAML pour Jira de resolution GmbH** en tant qu’administrateur.

8. Pointez sur le roue dentée, puis cliquez sur **Modules complémentaires**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon1.png)

9. Vous êtes redirigé vers la page d’accès administrateur. Entrez le **mot de passe**, puis cliquez sur le bouton **Confirmer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon2.png)

10. Sous l’onglet Modules complémentaires, cliquez sur **Find new add-ons** (Trouver de nouveaux modules complémentaires). Recherchez **SAML Single Sign On (SSO) for Jira**, puis cliquez sur le bouton **Install** pour installer le nouveau plug-in SAML.

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon7.png)

11. L’installation du plug-in démarre. Cliquez sur **Fermer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon8.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon9.png)

12. Cliquez sur **Gérer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon10.png)
    
13. Cliquez sur **Configurer** pour configurer le nouveau plug-in.

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon11.png)

14. Dans la page **SAML SingleSignOn Plugin Configuration** (Configuration du plug-in d’authentification unique SAML), cliquez sur le bouton **Add new IdP** (Ajouter un nouveau fournisseur d’identité) pour configurer les paramètres du fournisseur d’identité.

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon4.png)

15. Dans la page **Choose your SAML Identity Provider** (Choisissez votre fournisseur d’identité SAML), effectuez les actions suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon5a.png)
 
    a. Définissez **Azure AD** comme type de fournisseurs d’identité.
    
    b. Ajoutez le **Nom** du fournisseur d’identité (p. ex. Azure AD).
    
    c. Ajoutez la **Description** du fournisseur d’identité (p. ex. Azure AD).
    
    d. Cliquez sur **Suivant**.
    
16. Dans la page **Identity provider configuration** (Configuration du fournisseur d’identité), cliquez sur le bouton **Next** (Suivant).

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon5b.png)

17. Dans la page **Import SAML IdP Metadata** (Importer les métadonnées du fournisseur d’identité SAML), effectuez les actions suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon5c.png)

    a. Cliquez sur le bouton **Load File** (Charger le fichier) et choisissez le fichier XML de métadonnées que vous avez téléchargé à l’étape 5.

    b. Cliquez sur le bouton **Import** (Importer).
    
    c. Patientez quelques instants jusqu’à la fin de l’importation.
    
    d. Cliquez sur le bouton **Next** (Suivant).
    
18. Dans la page **User ID attribute and transformation** (Attribut d’ID d’utilisateur et transformation), cliquez sur le bouton **Next** (Suivant).

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon5d.png)
    
19. Dans la page **User creation and update** (Création et mise à jour de l’utilisateur), cliquez sur **Save & Next** (Enregistrer et suivant) pour enregistrer les paramètres.   
    
    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon6a.png)
    
20. Dans la page **Test your settings** (Tester vos paramètres), cliquez sur **Skip test & configure manually** (ignorer le test et configurer manuellement) afin d’ignorer le test de l’utilisateur pour l’instant. Cette opération, qui requiert certains paramètres dans le portail Azure, sera effectuée à la section suivante. 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon6b.png)
    
21. Dans la boîte de dialogue **Skipping the test means...** (Ignorer le test signifie...), cliquez sur **OK**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/addon6c.png)

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-samlssojira-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Création d’un utilisateur de test pour SSO SAML pour Jira de resolution GmbH

Pour permettre à des utilisateurs d’Azure AD de se connecter à SSO SAML pour Jira de resolution GmbH, vous devez configurer ceux-ci dans SSO SAML pour Jira de resolution GmbH.  
Dans SSO SAML pour Jira de resolution GmbH, cette configuration est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise SSO SAML pour Jira de resolution GmbH en tant qu’administrateur.

2. Pointez sur la roue dentée, puis cliquez sur **Gestion des utilisateurs**.

    ![Ajouter un employé](./media/active-directory-saas-samlssojira-tutorial/user1.png) 

3. Vous êtes redirigé vers la page d’accès administrateur dans laquelle vous entrez le **mot de passe**, puis cliquez sur le bouton **Confirmer**.

    ![Ajouter un employé](./media/active-directory-saas-samlssojira-tutorial/user2.png) 

4. Sous l’onglet **User management** (Gestion des utilisateurs), cliquez sur **Create user** (Créer un utilisateur).

    ![Ajouter un employé](./media/active-directory-saas-samlssojira-tutorial/user3.png) 

5. Dans la page de boîte de dialogue **Create New User** (Créer un utilisateur), procédez comme suit :

    ![Ajouter un employé](./media/active-directory-saas-samlssojira-tutorial/user4.png) 

    a. Dans la zone de texte **Email address** (Adresse e-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    b. Dans la zone de texte **Full Name** (Nom complet), tapez le nom complet d’un utilisateur, par exemple, Britta Simon.

    c. Dans la zone de texte **Username** (Nom d’utilisateur), tapez l’e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    d. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.

    e. Cliquez sur **Create User** (Créer un utilisateur).   

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous activez Britta Simon pour utiliser l’authentification unique Azure en accordant l’accès à SSO SAML pour Jira de resolution GmbH.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à SSO SAML pour Jira de resolution GmbH, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SSO SAML pour Jira de resolution GmbH**.

    ![Configurer l’authentification unique](./media/active-directory-saas-samlssojira-tutorial/tutorial_samlssojira_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette SSO SAML pour Jira de resolution GmbH dans le panneau d’accès, vous devez être automatiquement authentifiés auprès de votre application SSO SAML pour Jira de resolution GmbH.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-samlssojira-tutorial/tutorial_general_203.png

