---
title: "Didacticiel : Intégration d’Azure Active Directory avec LinkedIn Learning | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et LinkedIn Learning."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: jeedes
ms.openlocfilehash: 07d19a3fb457093d05afc13e1953c120ece3a280
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Didacticiel : Intégration d’Azure Active Directory à LinkedIn Learning

Dans ce didacticiel, vous allez apprendre à intégrer LinkedIn Learning à Azure Active Directory (Azure AD).

L’intégration de LinkedIn Learning dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à LinkedIn Learning
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à LinkedIn Learning (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à LinkedIn Learning, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement LinkedIn Learning pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de LinkedIn Learning à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-linkedin-learning-from-the-gallery"></a>Ajout de LinkedIn Learning à partir de la galerie
Pour configurer l’intégration de LinkedIn Learning à Azure AD, vous devez ajouter LinkedIn Learning à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter LinkedIn Learning à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **LinkedIn Learning**. Dans le volet de résultats, cliquez sur **LinkedIn Learning** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec LinkedIn Learning avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur LinkedIn Learning équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur LinkedIn Learning associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans LinkedIn Learning.

Pour configurer et tester l’authentification unique Azure AD avec LinkedIn Learning, vous devez suivre les blocs élémentaires suivants :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test LinkedIn Learning](#creating-a-linkedin-learning-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application LinkedIn Learning.

**Pour configurer l’authentification unique Azure AD avec LinkedIn Learning, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **LinkedIn Learning**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedin_01.png)

3. Dans une autre fenêtre de navigateur web, connectez-vous à votre client LinkedIn Learning en tant qu’administrateur.

4. Dans le **Centre des comptes**, cliquez sur **Paramètres globaux** sous **Paramètres**. Sélectionnez aussi **Apprentissage - Par défaut** dans la liste déroulante.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

5. Cliquez sur  **OU cliquez ici pour charger et copier les champs individuels du formulaire**, puis copiez l’**ID d’entité** et l’**URL ACS (Assertion Consumer Access)**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

6. Dans la section **Domaines et URL LinkedIn Learning**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en **Mode initié par IDP**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_01.png)

    a. Dans la zone de texte **Identificateur**, entrez **l’ID d’entité** copié à partir de LinkedIn Portal 

    b. Dans la zone de texte **URL de réponse**, entrez **l’URL ACS** copiée à partir du portail LinkedIn

7. Si vous souhaitez configurer l’authentification unique en mode **Initié par SP**, cliquez sur l’option Afficher les paramètres d’URL avancés dans la section de configuration et configurez l’URL d’authentification avec le format suivant :

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_signon_02.png)   
    
8. Votre application LinkedIn Learning attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML . La capture d’écran suivante montre un exemple : La valeur par défaut pour **Identificateur d’utilisateur** est **user.userprincipalname**, mais LinkedIn Learning s’attend à ce qu’elle soit mappée sur l’adresse de messagerie de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation. 

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/updateusermail.png)
    
9. Dans **Attributs utilisateur**, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** et définissez les attributs. L’utilisateur doit ajouter quatre revendications nommées **email**, **department**, **firstname** et **lastname** et la valeur doit être mappée respectivement à **user.mail**, **user.department**, **user.givenname** et **user.surname**.

    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |
    | email| user.mail |    
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/userattribute.png)
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue associée.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_04.png)

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**.

10. Effectuez les étapes suivantes au niveau de l’attribut **name**.

    a. Cliquez sur l’attribut pour ouvrir la fenêtre **Modifier l’attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinLearning-tutorial/url_update.png)

    b. Supprimez la valeur d’URL dans **namespace**.
    
    c. Cliquez sur **OK** pour enregistrer le paramètre.

11. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_certificate.png) 

12. Cliquez sur **Save**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_400.png)

13. Accédez à la section **Paramètres de l’administrateur LinkedIn**. Chargez le fichier XML que vous avez téléchargé à partir du portail Azure en cliquant sur l’option Charger le fichier XML.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

14. Cliquez sur **Activer** pour activer l’authentification unique. L’état de l’authentification unique passe de **Non connecté** à **Connecté**

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinlearning-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 

### <a name="creating-a-linkedin-learning-test-user"></a>Création d’un utilisateur de test LinkedIn Learning

L’application LinkedIn Learning prend en charge la configuration d’utilisateur juste-à-temps, et après authentification, les utilisateurs sont créés automatiquement dans l’application. Sur la page de paramètres administrateur sur le portail LinkedIn Learning, activez le commutateur **Affecter automatiquement les licences** pour la configuration juste à temps. Cela affectera également une licence à l’utilisateur.
   
   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinLearning-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LinkedIn Learning.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à LinkedIn Learning, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **LinkedIn Learning**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinlearning-tutorial/tutorial-linkedinlearning_0001.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette LinkedIn Learning dans le volet d’accès, vous devriez obtenir la page d’authentification Azure et, après une authentification réussie, vous devriez accéder à votre application LinkedIn Learning.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinlearning-tutorial/tutorial_general_203.png