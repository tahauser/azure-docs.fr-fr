---
title: "Didacticiel : Intégration d’Azure Active Directory à Sequr | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Sequr."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a491e2ce-b4e8-41b8-8f4a-a2e263e462c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: 8a2984bc2c77e9d92d4236b01c21912c27e167f9
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sequr"></a>Didacticiel : Intégration d’Azure Active Directory à Sequr

Dans ce didacticiel, vous apprenez à intégrer Sequr à Azure Active Directory (Azure AD).

L’intégration de Sequr dans Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Sequr.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Sequr (par authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à Sequr, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Sequr pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Sequr à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-sequr-from-the-gallery"></a>Ajout de Sequr à partir de la galerie
Pour configurer l’intégration de Sequr dans Azure AD, vous devez ajouter Sequr, disponible dans la galerie, à votre liste d’applications SaaS managées.

**Pour ajouter Sequr à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Sequr**, sélectionnez **Sequr** dans le panneau de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Sequr dans la liste des résultats](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous configurez et testez l’authentification unique Azure AD avec Sequr sur un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Sequr équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Sequr associé doit être établie.

Dans Sequr, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Sequr, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test Sequr](#create-a-sequr-test-user)** pour avoir un équivalent de Britta Simon dans Sequr lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous activez l’authentification unique Azure AD dans le portail Azure et configurez l’authentification unique dans votre application Sequr.

**Pour configurer l’authentification unique Azure AD avec Sequr, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Sequr**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_samlbase.png)

3. Dans la section **Domaines et URL Sequr**, suivez les étapes ci-dessous si vous souhaitez configurer l’application en mode initié par **IDP** :

    ![Informations d’authentification unique dans Domaine et URL Sequr](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_url.png)

    Dans la zone de texte **Identificateur**, tapez l’URL : `https://login.sequr.io`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Sequr](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_url1.png)

    a. Dans la zone de texte **URL de connexion**, tapez l’URL : `https://login.sequr.io`

    b. Dans la zone de texte **État de relais**, vous obtenez cette valeur, qui est expliquée plus loin dans le didacticiel.
     
5. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-sequr-tutorial/tutorial_general_400.png)
    
7. Dans la section **Configuration de Sequr**, cliquez sur **Configurer Sequr** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Sequr](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_configure.png)

8. Dans une autre fenêtre de navigateur web, connectez-vous au site de votre entreprise Sequr en tant qu’administrateur.

9. Cliquez sur **Intégrations** à partir du panneau de navigation gauche.

    ![Configuration de Sequr](./media/active-directory-saas-sequr-tutorial/configure1.png)

10. Faites défiler jusqu’à la section **Authentification unique** et cliquez sur **Gérer**.

    ![Configuration de Sequr](./media/active-directory-saas-sequr-tutorial/configure2.png)

11. Dans la section **Gérer l’authentification unique**, suivez ces étapes :

    ![Configuration de Sequr](./media/active-directory-saas-sequr-tutorial/configure3.png)

    a. Dans la zone de texte **URL d’authentification unique du fournisseur d’identité**, collez la valeur **URL du service d’authentification unique SAML** copiée dans le portail Azure.

    b. Faites glisser et déposez le fichier de **certificat** que vous avez téléchargé à partir du portail Azure, ou entrez manuellement le contenu du certificat.

    c. Après avoir enregistré la configuration, la valeur d’état de relais est générée. Copiez la valeur d’**état de relais** et collez-la dans la zone de texte **État de relais** de la section **Domaine et URL Sequr** dans le portail Azure.

    d. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-sequr-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-sequr-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-sequr-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-sequr-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-sequr-test-user"></a>Créer un utilisateur de test Sequr

Dans cette section, vous créez un utilisateur appelé Britta Simon dans Sequr. Collaborez avec l’[équipe du support technique Sequr](mailto:support@sequr.io) pour ajouter des utilisateurs sur la plateforme Sequr. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous autorisez Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Sequr.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Sequr, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Sequr**.

    ![Lien Sequr dans la liste des applications](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

En cliquant sur la vignette Sequr dans le panneau d’accès, vous êtes désormais automatiquement connecté à votre application Sequr.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_203.png

