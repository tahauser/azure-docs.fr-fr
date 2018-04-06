---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Domo | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Domo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 058626e4-73b3-4dc2-86ca-b060d002d70a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: jeedes
ms.openlocfilehash: c7f74d3bd8a2f0173f8f3cc69c611696044875a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/23/2018
---
# <a name="tutorial-azure-active-directory-integration-with-domo"></a>Didacticiel : intégration d’Azure Active Directory à Domo

Dans ce didacticiel, vous allez apprendre à intégrer Domo à Azure Active Directory (Azure AD).

L’intégration de Domo dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Domo
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Domo (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD avec Domo, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Domo pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Domo depuis la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-domo-from-the-gallery"></a>Ajout de Domo depuis la galerie
Pour configurer l’intégration de Domo avec Azure AD, vous devez ajouter Domo, à partir de la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter Domo à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, tapez **Domo**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-domo-tutorial/tutorial_domo_search.png)

5. Dans le volet de résultats, sélectionnez **Domo**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-domo-tutorial/tutorial_domo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Domo, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Domo correspondant dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Domo associé doit être établie.

Dans Domo, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Domo, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Domo](#creating-a-domo-test-user)** pour avoir un équivalent de Britta Simon dans Domo lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Domo.

**Pour configurer l’authentification unique Azure AD avec Domo, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Domo**, cliquez sur **Authentification unique**.

    ![Configure Single Sign-On][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_samlbase.png)

3. Dans la section **Domaine et URL Domo**, procédez comme suit :

    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<companyname>.domo.com`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant :     

    | |
    |--|    
    | `https://<companyname>.domo.com` |
    | `https://<companyname>.beta.domo.com` |
    | `https://<companyname>.demo.domo.com` |
    | `https://<companyname>.dev.domo.com` | 
    | `https://<companyname>.fastage1.domo.com` |       
    | `https://<companyname>.frdev.domo.com` |       
    | `https://<companyname>.gastage.domo.com` |       
    | `https://<companyname>.load.domo.com` |       
    | `https://<companyname>.local.domo.com` |       
    | `https://<companyname>.qa.domo.com` |
    | `https://<companyname>.stage.domo.com` |
    
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique de Domo](mailto:support@domo.com).

4. L’application Domo attend les assertions SAML dans un format spécifique. Configurez les revendications suivantes pour cette application. Vous pouvez gérer les valeurs de ces attributs à partir de la section « **Attributs utilisateur** » sur la page d’intégration des applications. La capture d’écran suivante montre un exemple de cette configuration. 

    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_attributes.png)
    
5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique**, configurez l’attribut de jeton SAML comme sur l’image et procédez comme suit :
    
    | Nom de l'attribut | Valeur de l’attribut |
    | ------------------- | -------------------- |    
    | Nom | user.displayname |
    | email | user.mail |
    
    a. Cliquez sur **Ajouter un attribut** pour ouvrir la boîte de dialogue **Ajouter un attribut**.

    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_attribute_04.png)

    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_attribute_05.png)

    b. Dans la zone de texte **Attribut**, indiquez le nom d’attribut pour cette ligne.
    
    c. Dans la liste **Valeur** , saisissez la valeur d’attribut affichée pour cette ligne.
    
    d. Cliquez sur **OK**. 
 
6. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_general_400.png)


8. Dans la section **Configuration de Domo**, cliquez sur **Configurer Domo** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**   

   ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_configure.png) 

9. Pour configurer l’authentification unique côté **Domo**, accédez à l’article de la Base de connaissances de Domo [ici](http://knowledge.domo.com?cid=azuread), puis suivez les instructions.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-domo-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-a-domo-test-user"></a>Création d’un utilisateur de test Domo

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Domo. Domo prend en charge l’approvisionnement juste-à-temps, option activée par défaut.

Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à Domo s’il n’existe pas déjà.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Domo.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Domo, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Domo**.

    ![Configure Single Sign-On](./media/active-directory-saas-domo-tutorial/tutorial_domo_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.
Lorsque vous cliquez sur la mosaïque Domo dans le volet d’accès, vous devez être connecté automatiquement à votre application Domo.

Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-domo-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-domo-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-domo-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-domo-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-domo-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-domo-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-domo-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-domo-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-domo-tutorial/tutorial_general_203.png

