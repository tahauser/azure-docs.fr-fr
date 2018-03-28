---
title: 'Tutoriel : intégration d’Azure Active Directory à Trisotech Digital Enterprise Server | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Trisotech Digital Enterprise Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d54d20c-eca1-4fa6-b56a-4c3ed0593db0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: jeedes
ms.openlocfilehash: 82e88b0b2b7f04f2849bf5c3a780df3c8f1c9849
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-azure-active-directory-integration-with-trisotech-digital-enterprise-server"></a>Tutoriel : intégration d’Azure Active Directory à Trisotech Digital Enterprise Server

Dans ce tutoriel, vous allez apprendre à intégrer Trisotech Digital Enterprise Server avec Azure Active Directory (Azure AD).

L’intégration de Trisotech Digital Enterprise Server à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Trisotech Digital Enterprise Server.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Trisotech Digital Enterprise Server (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à Trisotech Digital Enterprise Server, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Trisotech Digital Enterprise Server pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Trisotech Digital Enterprise Server à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-trisotech-digital-enterprise-server-from-the-gallery"></a>Ajout de Trisotech Digital Enterprise Server à partir de la galerie
Pour configurer l’intégration de Trisotech Digital Enterprise Server à Azure AD, vous devez ajouter Trisotech Digital Enterprise Server, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Trisotech Digital Enterprise Server à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Trisotech Digital Enterprise Server**, sélectionnez **Trisotech Digital Enterprise Server** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Trisotech Digital Enterprise Server dans la liste des résultats](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Trisotech Digital Enterprise Server grâce à un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Trisotech Digital Enterprise Server équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Trisotech Digital Enterprise Server associé doit être établie.

Pour configurer et tester l’authentification unique Azure AD avec Trisotech Digital Enterprise Server, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test Trisotech Digital Enterprise Server](#create-a-trisotech-digital-enterprise-server-test-user)** pour avoir un équivalent de Britta Simon dans Trisotech Digital Enterprise Server, lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Trisotech Digital Enterprise Server.

**Pour configurer l’authentification unique Azure AD avec Trisotech Digital Enterprise Server, effectuez les étapes suivantes :**

1. Dans le portail Azure, dans la page d’intégration de l’application **Trisotech Digital Enterprise Server**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_samlbase.png)

3. Dans la section **Domaine et URL Trisotech Digital Enterprise Server**, effectuez les étapes suivantes :

    ![Informations d’authentification unique de domaine et d’URL Trisotech Digital Enterprise Server](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.trisotech.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<companyname>.trisotech.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Contactez l’[équipe de support Trisotech Digital Enterprise Server](mailto:support@trisotech.com) pour obtenir ces valeurs. 

4. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_400.png)

5. Pour générer l’URL des **métadonnées**, effectuez les étapes suivantes :

    a. Cliquez sur **Inscriptions des applications**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_appregistrations.png)
   
    b. Cliquez sur **Points de terminaison** pour ouvrir la boîte de dialogue **Points de terminaison**.  
    
    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_endpointicon.png)

    c. Cliquez sur le bouton Copier pour copier l’URL du document de métadonnées de fédération (**FEDERATION METADATA DOCUMENT**), puis collez-la dans le Bloc-notes.
    
    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_endpoint.png)
     
    d. Accédez maintenant à la page de propriétés de **Trisotech Digital Enterprise Server**, puis copiez l’**ID d’application** à l’aide du bouton **Copier** et collez-le dans le Bloc-notes.
 
    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_appid.png)

    e. Générez l’**URL des métadonnées** en utilisant le format suivant : `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

6. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Trisotech Digital Enterprise Server Configuration en tant qu’administrateur.

7. Cliquez sur l’**icône de menu**, puis sélectionnez **Administration**.

    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user1.png)

8. Sélectionnez **User Provider (Fournisseur d’utilisateur)**.

    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user2.png)

9. Dans la section **User Provider Configuration**, effectuez les étapes suivantes :

    ![Configure Single Sign-On](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/user3.png)

    a. Sélectionnez **Secured Assertion Markup Language 2 (SAML 2)** dans la liste déroulante dans **Authentication Method**.

    b. Dans la zone de texte **Metadata URL**, collez la valeur **URL des métadonnées** générée à partir du portail Azure.

    c. Dans la zone de texte **Application ID**, entrez l’URL au format suivant : `https://<companyname>.trisotech.com`.

    d. Cliquez sur **Enregistrer**.

    e. Entrez le nom de domaine dans la zone de texte **Allowed Domains (empty means everyone)**. Des licences sont affectées automatiquement pour les utilisateurs correspondant aux domaines autorisés.

    f. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-trisotech-digital-enterprise-server-test-user"></a>Créer un utilisateur de test Trisotech Digital Enterprise Server

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Trisotech Digital Enterprise Server. Trisotech Digital Enterprise Server prend en charge le provisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. S’il n’existe pas encore, un utilisateur est créé au moment d’une tentative d’accès à Trisotech Digital Enterprise Server.
>[!Note]
>Si vous avez besoin de créer un utilisateur manuellement, contactez l’[équipe de support Trisotech Digital Enterprise Server](mailto:support@trisotech.com).

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Trisotech Digital Enterprise Server.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Trisotech Digital Enterprise Server, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Trisotech Digital Enterprise Server**.

    ![Le lien Trisotech Digital Enterprise Server dans la liste des Applications](./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_trisotechdigitalenterpriseserver_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Trisotech Digital Enterprise Server dans le volet d’accès, vous devez être connecté automatiquement à votre application Trisotech Digital Enterprise Server.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trisotechdigitalenterpriseserver-tutorial/tutorial_general_203.png

