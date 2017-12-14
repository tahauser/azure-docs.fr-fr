---
title: "Didacticiel : Intégration d’Azure Active Directory à Adaptative Suite | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Adaptive Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.openlocfilehash: 8e128ddf53a93fe30350d8e914657f3539701603
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>Didacticiel : Intégration d’Azure Active Directory à Adaptative Suite

Dans ce didacticiel, vous allez apprendre à intégrer Adaptive Suite dans Azure Active Directory (Azure AD).

L’intégration d’Adaptive Suite dans Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Adaptive Suite
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Adaptive Suite (par le biais de l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes depuis un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD dans Adaptive Suite, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Adaptive Suite pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout d’Adaptive Suite à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-adaptive-suite-from-the-gallery"></a>Ajout d’Adaptive Suite à partir de la galerie
Pour configurer l’intégration d’Adaptive Suite dans Azure AD, vous devez ajouter Adaptive Suite, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Adaptive Suite à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Adaptive Suite**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_search.png)

5. Dans le volet de résultats, sélectionnez **Adaptive Suite**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Adaptive Suite, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Adaptive Suite correspondant dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Adaptive Suite associé doit être établie.

Dans Adaptive Suite, assignez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Adaptive Suite, vous devez suivre les indications des sections ci-dessous :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Adaptive Suite](#creating-an-adaptive-suite-test-user)** pour avoir un équivalent de Britta Simon dans Adaptive Suite lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Adaptive Suite.

**Pour configurer l’authentification unique Azure AD avec Adaptive Suite, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Adaptive Suite**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_samlbase.png)

3. Dans la section **Domaine et URL Adaptive Suite**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Vous pouvez obtenir cette valeur dans la page **SAML SSO Settings** d’Adaptive Suite.
    >  

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration d’Adaptive Suite**, cliquez sur **Configurer Adaptive Suite** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l’**ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**

    ![Configurer l’authentification unique](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_configure.png) 

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Adaptive Suite en tant qu’administrateur.

8. Accédez à **Admin**.
   
    ![Administrateur](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Administrateur")

9. Dans la section **Users and Roles**, cliquez sur **Manage SAML SSO Settings**.
   
    ![Gérer les paramètres d’authentification unique de SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805645.png "Gérer les paramètres d’authentification unique de SAML")

10. Dans la page **SAML SSO Settings** , procédez comme suit :
   
    ![Paramètres d’authentification unique de SAML](./media/active-directory-saas-adaptivesuite-tutorial/IC805646.png "Paramètres d’authentification unique de SAML")

    a. Dans la zone de texte **Identity provider name** , attribuez un nom à votre configuration.
    
    b. Collez la valeur **ID d’entité SAML** copiée à partir du portail Azure dans la zone de texte **ID d’entité du fournisseur d’identité**.
  
    c. Collez la valeur **URL du service d’authentification unique SAML** copiée à partir du portail Azure dans la zone de texte **URL SSO du fournisseur d’identité**.
  
    d. Collez la valeur **URL du service d’authentification unique SAML** copiée à partir du portail Azure dans la zone de texte **Custom logout URL** (URL de connexion personnalisée).
  
    e. Pour charger votre certificat téléchargé, cliquez sur **Choisir un fichier**.
  
    f. Sélectionnez les options suivantes :
    * Pour **SAML user id**, sélectionnez **User’s Adaptive Insights user name**.
    * Pour **SAML user id location**, sélectionnez **User id in NameID of Subject**.
    * Pour **SAML NameID format**, sélectionnez **Email address**.
    * Pour **Enable SAML**, sélectionnez **Allow SAML SSO and direct Adaptive Insights login**.
    
    g. Cliquez sur **Save**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-adaptivesuite-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-an-adaptive-suite-test-user"></a>Création d’un utilisateur de test Adaptive Suite

Pour permettre aux utilisateurs Azure AD de se connecter à Adaptive Suite, vous devez les approvisionner dans Adaptive Suite.  

* Dans le cas d’Adaptive Suite, cet approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :** 

1. Connectez-vous à votre site d’entreprise **Adaptive Suite** en tant qu’administrateur.
2. Accédez à **Admin**.
   
   ![Administrateur](./media/active-directory-saas-adaptivesuite-tutorial/IC805644.png "Administrateur")
3. Dans la section **Users and Roles**, cliquez sur **Add User**.
   
   ![Ajouter un utilisateur](./media/active-directory-saas-adaptivesuite-tutorial/IC805648.png "Ajouter un utilisateur")
4. Dans la section **New User** , procédez comme suit :
   
   ![Envoyer](./media/active-directory-saas-adaptivesuite-tutorial/IC805649.png "Envoyer")   

   a. Tapez le nom, l’identifiant de connexion, l’adresse de messagerie et le mot de passe de l’utilisateur Azure Active Directory valide que vous souhaitez renseigner dans les zones de texte correspondantes, à savoir, **Name**, **Login**, **Email** et **Password**.
  
   b. Sélectionnez un **rôle**.
  
   c. Cliquez sur **Envoyer**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur Adaptive Suite fourni par ce service pour approvisionner des comptes d’utilisateur Azure Active Directory.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Adaptive Suite.

![Affecter des utilisateurs][200] 

**Pour assigner Britta Simon à Adaptive Suite, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Adaptive Suite**.

    ![Configurer l’authentification unique](./media/active-directory-saas-adaptivesuite-tutorial/tutorial_adaptivesuite_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Microsoft Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Adaptive Suite dans le panneau d’accès, vous devez être connecté automatiquement à votre application Adaptive Suite.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adaptivesuite-tutorial/tutorial_general_203.png

