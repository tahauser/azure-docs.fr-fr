---
title: "Didacticiel : Intégration d’Azure Active Directory avec Kantega SSO pour JIRA | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kantega SSO pour JIRA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e2af4891-e3c8-43b3-bdcb-0500c493e9b4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: dd3c97a753b67ab27033d32c3990c9b096287069
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Didacticiel : Intégration d’Azure Active Directory avec Kantega SSO pour JIRA

Dans ce didacticiel, vous allez apprendre à intégrer Kantega SSO pour JIRA avec Azure Active Directory (Azure AD).

L’intégration de Kantega SSO pour JIRA avec Azure AD offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à Kantega SSO pour JIRA.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Kantega SSO pour JIRA (authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec Kantega SSO pour JIRA, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Kantega SSO pour JIRA pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Kantega SSO pour JIRA à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>Ajout de Kantega SSO pour JIRA à partir de la galerie
Pour configurer l’intégration de Kantega SSO pour JIRA dans Azure AD, vous devez ajouter Kantega SSO pour JIRA à partir de la galerie à la liste des applications SaaS managées.

**Pour ajouter Kantega SSO pour JIRA à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Kantega SSO pour JIRA**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_search.png)

5. Dans le volet de résultats, sélectionnez **Kantega SSO pour JIRA**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Kantega SSO pour JIRA sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur équivalent à l’utilisateur Azure AD dans Kantega SSO pour JIRA. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Kantega SSO pour JIRA associé doit être établie.

Dans Kantega SSO pour JIRA, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **Username** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Kantega SSO pour JIRA, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Kantega SSO pour JIRA](#creating-a-kantega-sso-for-jira-test-user)** pour avoir un équivalent de Britta Simon dans Kantega SSO pour JIRA qui soit lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Kantega SSO pour JIRA.

**Pour configurer l’authentification unique Azure AD avec Kantega SSO pour JIRA, procédez comme suit :**

1. Sur le portail Azure, dans la page d’intégration de l’application **Kantega SSO pour JIRA**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_samlbase.png)

3. En mode initié **IDP**, dans la section **Domaine et URL Kantega SSO pour JIRA**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_url1.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. En mode initié **SP**, activez **Afficher les paramètres d’URL avancés**, puis procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_url2.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur, l’URL de réponse et l’URL de connexion réels. Ces valeurs sont reçues durant la configuration du plug-in JIRA qui est décrite plus loin dans le didacticiel.

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_400.png)
    
7. Dans une autre fenêtre de navigateur web, connectez-vous à votre serveur local JIRA en tant qu’administrateur.

8. Pointez sur le roue dentée, puis cliquez sur **Modules complémentaires**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon1.png)

9. Sous l’onglet Modules complémentaires, cliquez sur **Find new add-ons** (Trouver de nouveaux modules complémentaires). Recherchez **Kantega SSO for JIRA (SAML & Kerberos)**, puis cliquez sur le bouton **Installer** pour installer le nouveau plug-in SAML.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon2.png)

10. L’installation du plug-in démarre.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon3.png)

11. Une fois l’installation terminée. Cliquez sur **Fermer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon33.png)

12. Cliquez sur **Gérer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon34.png)
    
13. Le nouveau plug-in est répertorié sous **INTÉGRATIONS**. Cliquez sur **Configurer** pour configurer le nouveau plug-in.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon35.png)

14. Dans la section **SAML**. Dans le menu déroulant **Ajouter le fournisseur d’identité**, sélectionnez **Azure Active Directory (Azure AD)**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon4.png)

15. Sélectionnez le niveau d’abonnement **De base**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon5.png)     

16. Dans la section **Propriétés de l’application**, procédez comme suit : 

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon6.png)

    a. Copiez la valeur **URI ID d'application** et utilisez-la en tant que **Identifier, Reply URL, and Sign-On URL** (Identificateur, URL de réponse et URL de connexion) dans la section **Domaine et URL Kantega SSO pour JIRA** du portail Azure.

    b. Cliquez sur **Suivant**.

17. Dans la section **Metadata import** (Importation des métadonnées), procédez comme suit : 

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon7.png)

    a. Sélectionnez **Metadata file on my computer** (Fichier de métadonnées sur mon ordinateur), puis chargez le fichier de métadonnées que vous avez téléchargé à partir du portail Azure.

    b. Cliquez sur **Suivant**.

18. Dans la section **Name and SSO location** (Nom et emplacement de l’authentification unique), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon8.png)
    
    a. Ajoutez le nom du fournisseur d’identité dans la zone de texte **Identity provider name** (Nom du fournisseur d’identité) (par exemple, Azure AD).

    b. Cliquez sur **Suivant**.

19. Vérifiez le certificat de signature, puis cliquez sur **Suivant**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon9.png)

20. Dans la section **JIRA user accounts** (Comptes d’utilisateur JIRA), procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon10.png)

    a. Sélectionnez **Create users in JIRA’s internal Directory if needed** (Créer des utilisateurs dans l’annuaire interne de JIRA si nécessaire) et saisissez le nom de groupe approprié pour les utilisateurs (il peut s’agir de plusieurs groupes séparés par des virgules).

    b. Cliquez sur **Suivant**.

21. Cliquez sur **Terminer**.   

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon11.png)

22. Dans la section **Known domains for Azure AD** (Domaines connus pour Azure AD), procédez comme suit : 

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/addon12.png)

    a. Sélectionnez **Known domains** (Domaines connus) dans le volet gauche de la page.

    b. Entrez le nom de domaine dans la zone de texte **Known domains** (Domaines connus).

    c. Cliquez sur **Enregistrer**. 

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kantegassoforjira-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-kantega-sso-for-jira-test-user"></a>Création d’un utilisateur de test Kantega SSO pour JIRA

Pour se connecter à JIRA, les utilisateurs d’Azure AD doivent être approvisionnés dans JIRA. Dans Kantega SSO pour JIRA, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre serveur local JIRA en tant qu’administrateur.

2. Pointez sur la roue dentée, puis cliquez sur **Gestion des utilisateurs**.

    ![Ajouter un employé](./media/active-directory-saas-kantegassoforjira-tutorial/user1.png) 

3. Sous l’onglet **User management** (Gestion des utilisateurs), cliquez sur **Create user** (Créer un utilisateur).

    ![Ajouter un employé](./media/active-directory-saas-kantegassoforjira-tutorial/user2.png) 

4. Dans la page de boîte de dialogue **Create New User** (Créer un utilisateur), procédez comme suit :

    ![Ajouter un employé](./media/active-directory-saas-kantegassoforjira-tutorial/user3.png) 

    a. Dans la zone de texte **Email address** (Adresse e-mail), tapez l’adresse e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    b. Dans la zone de texte **Full Name** (Nom complet), tapez le nom complet d’un utilisateur, par exemple, Britta Simon.

    c. Dans la zone de texte **Username** (Nom d’utilisateur), tapez l’e-mail d’un utilisateur, par exemple, Brittasimon@contoso.com.

    d. Dans la zone de texte **Password** (Mot de passe), tapez le mot de passe de l’utilisateur.

    e. Cliquez sur **Create User** (Créer un utilisateur).   

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Kantega SSO pour JIRA.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Kantega SSO pour JIRA, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Kantega SSO pour JIRA**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_kantegassoforjira_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Kantega SSO pour JIRA dans le volet d’accès, vous devez être automatiquement connecté à votre application Kantega SSO pour JIRA.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kantegassoforjira-tutorial/tutorial_general_203.png

