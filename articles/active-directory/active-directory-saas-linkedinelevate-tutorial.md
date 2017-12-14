---
title: "Didacticiel : Intégration d’Azure Active Directory à LinkedIn Elevate | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et LinkedIn Elevate."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: jeedes
ms.openlocfilehash: 5b46323dc487bbc714c2306ed006afffe8c1bb6a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Didacticiel : Intégration d’Azure Active Directory à LinkedIn Elevate

Dans ce didacticiel, vous allez apprendre à intégrer LinkedIn Elevate à Azure Active Directory (Azure AD).

L’intégration de LinkedIn Elevate à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à LinkedIn Elevate
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à LinkedIn Elevate (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec LinkedIn Elevate, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement LinkedIn Elevate pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de LinkedIn Elevate à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Ajout de LinkedIn Elevate à partir de la galerie
Pour configurer l’intégration de LinkedIn Elevate à Azure AD, vous devez ajouter LinkedIn Elevate à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter LinkedIn Elevate à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **LinkedIn Elevate**. Dans le volet de résultats, cliquez sur **LinkedIn Elevate** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec LinkedIn Elevate, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur LinkedIn Elevate équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur LinkedIn Elevate associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans LinkedIn Elevate.

Pour configurer et tester l’authentification unique Azure AD avec LinkedIn Elevate, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test LinkedIn Elevate](#creating-a-linkedin-elevate-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application LinkedIn Elevate.

**Pour configurer l’authentification unique Azure AD avec LinkedIn Elevate, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **LinkedIn Elevate**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedin_01.png)

3. Dans une autre fenêtre de navigateur web, connectez-vous à votre client LinkedIn Elevate en tant qu’administrateur.

4. Dans le **Centre des comptes**, cliquez sur **Paramètres globaux** sous **Paramètres**. En outre, sélectionnez **Elevate - Test AAD Elevate** dans la liste déroulante.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_01.png)

5. Cliquez sur **OU cliquez ici pour charger et copier des champs du formulaire** et copiez **ID d’entité** et **URL ACS**

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_03.png)

6. Dans le portail Azure, sous **Domaines et URL LinkedIn Elevate**, suivez les étapes ci-dessous si vous souhaitez configurer le SSO en mode **Initié par IDP**

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_01.png)

    a. Dans la zone de texte **Identificateur**, entrez **l’ID d’entité** copié à partir de LinkedIn Portal 

    b. Dans la zone de texte **URL de réponse**, entrez **l’URL ACS** copiée à partir du portail LinkedIn

7. Si vous souhaitez configurer l’authentification unique en mode **Initié par SP**, cliquez sur l’option Afficher les paramètres d’URL avancés dans la section de configuration et configurez l’URL d’authentification avec le format suivant :

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 
    
    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_signon_02.png) 
    
8. Votre application LinkedIn Elevate attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration Attributs du jeton SAML . La capture d’écran suivante montre un exemple : La valeur par défaut pour **Identificateur d’utilisateur** est **user.userprincipalname**, mais LinkedIn Elevate s’attend à ce qu’elle soit mappée sur l’adresse de messagerie de l’utilisateur. Pour cela, vous pouvez utiliser l’attribut **user.mail** dans la liste ou utiliser la valeur d’attribut appropriée en fonction de la configuration de votre organisation. 

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/updateusermail.png)

9. Dans **Attributs utilisateur**, cliquez sur **Afficher et modifier tous les autres attributs utilisateur** et définissez les attributs. Vous devez ajouter une autre revendication nommée **service**, et la valeur doit être mappée à **user.department**.

    | Nom de l'attribut | Valeur de l’attribut |
    | --- | --- |    
    | department| user.department |

      ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/userattribute.png)

      a. Cliquez sur Ajouter un attribut pour ouvrir la page de détails de l’attribut et ajouter l’attribut de département comme illustré ci-dessous-

      ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/adduserattribute.png)

      b. Cliquez sur **OK** pour enregistrer l’attribut.

      c. Remplacez le nom de l’attribut **emailaddress** par **email**.


10. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier XML sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_certificate.png) 

11. Cliquez sur **Save**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_400.png)

12. Accédez à la section **Paramètres de l’administrateur LinkedIn**. Téléchargez le fichier XML que vous venez de télécharger à partir du portail Azure en cliquant sur l’option Télécharger fichier XML.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_metadata_03.png)

13. Cliquez sur **Activer** pour activer l’authentification unique. L’état de l’authentification unique passera de **Non connecté** à **Connecté**

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 

### <a name="creating-a-linkedin-elevate-test-user"></a>Création d’un utilisateur test LinkedIn Elevate

L’application LinkedIn Elevate prend en charge la configuration d’utilisateur juste à temps, et après authentification, les utilisateurs sont créés automatiquement dans l’application. Sur la page de paramètres administrateur sur le portail LinkedIn Elevate, activez le commutateur **Affecter automatiquement les licences** pour la configuration juste à temps. Cela affectera également une licence à l’utilisateur.

   ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-linkedinElevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à LinkedIn Elevate.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à LinkedIn Elevate, procédez comme suit :**

1. Dans le portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **LinkedIn Elevate**.

    ![Configurer l’authentification unique](./media/active-directory-saas-linkedinElevate-tutorial/tutorial-linkedinElevate_0001.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque LinkedIn Elevate dans le volet d’accès, vous devriez obtenir la page d’authentification Azure et, après une authentification réussie, vous devriez accéder à votre application LinkedIn Elevate.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Didacticiel : Configuration de LinkedIn Elevate pour l’approvisionnement automatique d’utilisateurs avec Azure Active Directory](active-directory-saas-linkedinelevate-provisioning-tutorial.md)
* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinElevate-tutorial/tutorial_general_203.png
