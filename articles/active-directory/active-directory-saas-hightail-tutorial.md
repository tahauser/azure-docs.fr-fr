---
title: "Didacticiel : Intégration d’Azure Active Directory à Hightail | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Hightail."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 2333a56cd9369cb66c4944304472462d2f2f7511
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Didacticiel : Intégration d’Azure Active Directory à Hightail

Dans ce didacticiel, vous allez apprendre à intégrer Hightail à Azure Active Directory (Azure AD).

L’intégration de Hightail à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Hightail
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Hightail (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à Hightail, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Hightail pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Hightail à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-hightail-from-the-gallery"></a>Ajout de Hightail à partir de la galerie
Pour configurer l’intégration de Hightail à Azure AD, vous devez ajouter Hightail, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Hightail à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **Hightail**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_search.png)

5. Dans le panneau des résultats, sélectionnez **Hightail**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Hightail avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Hightail équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Hightail associé doit être établie.

Dans Hightail, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Hightail, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Hightail](#creating-a-hightail-test-user)** pour avoir un équivalent de Britta Simon dans Hightail lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Hightail.

**Pour configurer l’authentification unique Azure AD avec Hightail, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Hightail**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_samlbase.png)

3. Dans la section **Domaine et URL Hightail**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url.png)

     Dans la zone de texte **URL de réponse**, tapez l’URL : `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE] 
    > La valeur ci-dessus n’est pas une valeur réelle. Vous mettrez à jour la valeur avec l’URL de réponse réelle. La procédure est expliquée plus loin dans le didacticiel.
 
4. Dans la section **Domaine et URL Hightail**, si vous souhaitez configurer l’application en **Mode initié par SP**, procédez comme suit :
    
    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_url1.png)

    a. Cliquez sur **Afficher les paramètres d’URL avancés**.

    b. Dans la zone de texte **URL d’authentification**, saisissez l’URL : `https://www.hightail.com/loginSSO`

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_certificate.png) 

5. L’application Hightail attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de l’onglet **Attribut** de l’application. La capture d’écran suivante montre un exemple : 

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_attribute.png) 

6. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |    
    | UserIdentity | user.mail |
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_04.png)

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_officespace_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.

    d. Laissez le champ **Espace de noms** vide.
    
    e. Cliquez sur **OK**.

7. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_general_400.png)

8. Dans la section **Configuration de Hightail** , cliquez sur **Configurer Hightail** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_configure.png) 

    >[!NOTE] 
    >Avant de configurer l’authentification unique sur l’application Hightail, ajoutez votre domaine de messagerie à la liste approuvée de l’équipe Hightail afin que tous les utilisateurs de ce domaine puissent utiliser la fonctionnalité d’authentification unique.


9. Pour que l’authentification unique soit configurée pour votre application, vous devez vous connecter à votre locataire Hightail en tant qu’administrateur.
   
    a. Dans le menu situé en haut, cliquez sur l’onglet **Compte** et sélectionnez **Configure SAML** (Configurer SAML).
 
    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png) 

    b. Cochez la case **Enable SAML Authentication**(Activer l’authentification SAML).

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png) 

    c. Ouvrez dans le Bloc-notes votre certificat codé en base 64 téléchargé à partir du portail Azure, copiez son contenu dans le Presse-papiers et collez-le dans la zone de texte **Certificat de signature de jeton SAML**.

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png) 

    d. Dans la zone de texte **Autorité SAML (fournisseur d’identité)**, collez la valeur de l’**URL du service d’authentification unique SAML** que vous avez copiée sur le portail Azure.

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

    e. Si vous souhaitez configurer l’application en **mode lancé par le fournisseur d’identité**, sélectionnez **Identity Provider (IdP) initiated log in** (Connexion lancée par le fournisseur d’identité). En **mode lancé par le fournisseur de service**, sélectionnez **Service Provider (SP) initiated log in** (Connexion lancée par le fournisseur de service).

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

    f. Copiez l’URL de consommateur SAML pour votre instance et collez-la dans la zone de texte **URL de réponse** de la section **Domaine et URL Hightail** du portail Azure.
    
    g. Cliquez sur **Save**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-hightail-test-user"></a>Création d’un utilisateur de test Hightail

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Hightail. 

Vous n’avez aucune opération à effectuer dans cette section. Hightail prend en charge l’approvisionnement juste-à-temps d’utilisateurs basé sur les revendications personnalisées. Si vous avez configuré les revendications personnalisées comme indiqué dans la section **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** ci-dessus, un utilisateur est automatiquement créé dans l’application s’il n’existe pas encore. 

>[!NOTE]
>Si vous avez besoin de créer un utilisateur manuellement, vous devez contacter [l’équipe de support de Hightail](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Hightail.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Hightail, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Hightail**.

    ![Configurer l’authentification unique](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

L’objectif de cette section est de tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Hightail dans le volet d’accès, vous devez être connecté automatiquement à votre application Hightail.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png

