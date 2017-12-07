---
title: "Didacticiel : intégration d’Azure Active Directory à Druva | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Druva."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: 52212c44c925598b2c19df1b20eb4e8123f974ba
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/28/2017
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Didacticiel : Intégration d’Azure Active Directory à Druva

Dans ce didacticiel, vous apprenez à intégrer Druva dans Azure Active Directory (Azure AD).

L’intégration de Druva dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Druva.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Druva (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Druva, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Druva pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Druva depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-druva-from-the-gallery"></a>Ajout de Druva depuis la galerie
Pour configurer l’intégration de Druva avec Azure AD, vous devez ajouter Druva disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Druva à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Druva**, sélectionnez **Druva** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Druva dans la liste des résultats](./media/active-directory-saas-druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Druva, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Druva correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Druva associé doit être établie.

Dans Druva, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Druva, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test Druva](#create-a-druva-test-user)** pour avoir un équivalent de Britta Simon dans Druva lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Druva.

**Pour configurer l’authentification unique Azure AD avec Druva, procédez comme suit :**

1. Dans le Portail Azure, sur la page d’intégration de l’application **Druva**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-druva-tutorial/tutorial_druva_samlbase.png)

3. Dans la section **Domaine et URL Druva**, si vous souhaitez configurer l’application en mode initié par **IDP**, suivez les étapes ci-dessous :

    ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/tutorial_druva_url.png)

    Dans la zone de texte **Identificateur**, tapez la valeur de chaîne : `druva-cloud`
    
4. Cliquez sur **Afficher les paramètres d’URL avancés**. Si vous souhaitez configurer l’application en mode initié par le **fournisseur de service** :

    ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/tutorial_druva_url1.png)
    
    Dans la zone de texte **URL de connexion**, entrez l’URL : `https://cloud.druva.com/home`

5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-druva-tutorial/tutorial_druva_certificate.png) 

6. Votre application Druva attend les assertions SAML dans un format spécifique, ce qui vous oblige à ajouter des mappages d’attributs personnalisés à votre configuration **Attributs du jeton SAML**. 

    ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/tutorial_druva_attribute.png)

7. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut du jeton SAML comme sur l’image précédente, puis procédez comme suit :

    | Nom de l'attribut      | Valeur de l’attribut      |
    | ------------------- | -------------------- |
    | insync\_auth\_token |Entrez la valeur du jeton générée |
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.
    
    ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/tutorial_attribute_04.png)
    
    ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/tutorial_attribute_05.png)
    
    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.

    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne. La valeur du jeton générée est expliquée plus loin dans le didacticiel.
    
    d. Cliquez sur **OK**.    

8. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/tutorial_general_400.png)

9. Dans la section **Configuration de Druva** , cliquez sur **Configurer Druva** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configurer l’authentification unique](./media/active-directory-saas-druva-tutorial/tutorial_druva_configure.png) 

10. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise Druva en tant qu’administrateur.

11. Accédez à **Manage \> Settings**.

    ![Paramètres](./media/active-directory-saas-druva-tutorial/ic795091.png "Paramètres")

12. Dans la boîte de dialogue Single Sign-On Settings, procédez comme suit :

    ![Paramètres d’authentification unique](./media/active-directory-saas-druva-tutorial/ic795092.png "paramètres d’authentification unique")
    
    a. Dans la zone de texte **ID Provider Login URL**, collez la valeur de l’**URL du service d’authentification unique** que vous avez copiée à partir du portail Azure.
        
    b. Dans la zone de texte **ID Provider Logout URL** (URL de déconnexion du fournisseur d’identité), collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.
        
    c. Ouvrez votre certificat codé en base 64 dans le bloc-notes, copiez son contenu dans le Presse-papiers, puis collez-le dans la zone de texte **ID Provider Certificate** .
     
    d. Pour ouvrir la page **Settings**, cliquez sur**Save**.

13. Dans la page **Settings**, cliquez sur **Generate SSO Token**.

    ![Paramètres](./media/active-directory-saas-druva-tutorial/ic795093.png "Paramètres")

14. Dans la boîte de dialogue **Single Sign-on Authentication Token**, procédez comme suit :

    ![SSO Token](./media/active-directory-saas-druva-tutorial/ic795094.png "SSO Token")
    
    a. Cliquez sur **Copier**, collez la valeur copiée dans la zone de texte **Valeur** de la section **Ajouter un attribut** dans le portail Azure.
    
    b. Cliquez sur **Fermer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-druva-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-druva-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-druva-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-druva-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="create-a-druva-test-user"></a>Créer un utilisateur de test Druva

Pour se connecter à Druva, les utilisateurs d’Azure AD doivent être approvisionnés dans Druva. Dans le cas de Druva, l’approvisionnement est une tâche manuelle.

**Pour configurer l'approvisionnement des utilisateurs, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise **Druva** en tant qu’administrateur.

2. Accédez à **Gérer \> Utilisateurs**.
   
   ![Gestion des utilisateurs](./media/active-directory-saas-druva-tutorial/ic795097.png "Gestion des utilisateurs")

3. Cliquez sur **Create New**.
   
   ![Gestion des utilisateurs](./media/active-directory-saas-druva-tutorial/ic795098.png "Gestion des utilisateurs")

4. Dans la boîte de dialogue Create New User, procédez comme suit :
   
   ![Create NewUser](./media/active-directory-saas-druva-tutorial/ic795099.png "Create NewUser")
   
   a. Dans la zone de texte **Email**, entrez l’adresse e-mail de l’utilisateur, par exemple **brittasimon@contoso.com**.
   
   b. Dans la zone de texte **Nom**, entrez le nom d’un utilisateur, par exemple **Britta Simon**.
   
   c. Cliquez sur **Create User**.

>[!NOTE]
>Vous pouvez utiliser n’importe quel autre outil ou API de création de compte d’utilisateur fourni par Druva pour approvisionner des comptes d’utilisateurs Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Druva.

![Attribuer le rôle d’utilisateur][200] 

**Pour affecter Britta Simon à Druva, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Druva**.

    ![Lien Druva dans la liste des applications](./media/active-directory-saas-druva-tutorial/tutorial_druva_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette Druva dans le volet d’accès, vous devez être connecté automatiquement à votre application Druva.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-druva-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-druva-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-druva-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-druva-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-druva-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-druva-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-druva-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-druva-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-druva-tutorial/tutorial_general_203.png

