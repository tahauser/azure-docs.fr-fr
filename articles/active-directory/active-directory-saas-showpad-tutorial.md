---
title: "Didacticiel : Intégration d’Azure Active Directory à Showpad | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Showpad."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 48b6bee0-dbc5-4863-964d-75b25e517741
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 3fa246fe3e6264189e6b727b970708f26a787020
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-showpad"></a>Didacticiel : intégration d’Azure Active Directory à Showpad

Dans ce didacticiel, vous allez apprendre à intégrer Showpad avec Azure Active Directory (Azure AD).

L’intégration de Showpad dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Showpad.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Showpad (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD à Showpad, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Abonnement Showpad pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Showpad à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-showpad-from-the-gallery"></a>Ajout de Showpad à partir de la galerie

Pour configurer l’intégration de Showpad à Azure AD, vous devez ajouter Showpad à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Showpad à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Showpad**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_search.png)

5. Dans le volet de résultats, sélectionnez **Showpad**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Showpad sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Showpad équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Showpad associé doit être établie.

Dans Showpad, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation de lien.

Pour configurer et tester l’authentification unique Azure AD avec Showpad, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Showpad](#creating-a-showpad-test-user)** - pour avoir un équivalent de Britta Simon dans Showpad lié à sa représentation dans Azure AD.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Showpad.

**Pour configurer l’authentification unique Azure AD avec Showpad, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Showpad**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_samlbase.png)

3. Dans la section **Domaine et URL Showpad**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<comapany-name>.showpad.biz/login`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company-name>.showpad.biz`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Showpad](https://help.showpad.com). 
 


4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-showpad-tutorial/tutorial_general_400.png)

6. Connectez-vous à votre client Showpad en tant qu’administrateur.

7. Dans le menu situé en haut, cliquez sur **Paramètres**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_001.png) 

8. Accédez à **Authentification unique** et cliquez sur **Activer**.
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_002.png)

9. Dans la boîte de dialogue **Add a SAML 2.0 Service** (Ajouter un service SAML 2.0), procédez comme suit :
   
    ![Configurer l’authentification unique côté application](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_003.png) 
   
    a. Dans la zone de texte **Nom**, entrez le nom du fournisseur d’identificateurs (par exemple : le nom de votre entreprise).
   
    b. Dans **Source des métadonnées**, sélectionnez **XML**.
   
    c. Copiez le contenu du fichier XML de métadonnées téléchargé à partir du portail Azure, puis collez-le dans la zone de texte **XML de métadonnées**.
   
    d. Sélectionnez **Auto-provision accounts for new users when they log in**(Configurer automatiquement les comptes des nouveaux utilisateurs au moment de la connexion).
   
    e. Cliquez sur **Envoyer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-showpad-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-showpad-test-user"></a>Création d’un utilisateur de test Showpad

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Showpad. 

Showpad prend en charge l’approvisionnement juste-à-temps. Vous l’avez déjà activé dans **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)**. 

Vous n’avez aucune opération à effectuer dans cette section. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Showpad.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Showpad, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Showpad**.

    ![Configurer l’authentification unique](./media/active-directory-saas-showpad-tutorial/tutorial_showpad_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Showpad dans le volet d’accès, vous devez être connecté automatiquement à l’application Showpad.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-showpad-tutorial/tutorial_general_203.png

