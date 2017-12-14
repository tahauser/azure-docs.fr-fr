---
title: "Didacticiel : Intégration d’Azure Active Directory avec TimeLive | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et TimeLive."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 34123629-4ad5-465c-a4c1-8299f857e720
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes
ms.openlocfilehash: 945067f0f413790883d55ab6cc9b2721144e966a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-timelive"></a>Didacticiel : Intégration d’Azure Active Directory avec TimeLive

Ce didacticiel explique comment intégrer TimeLive avec Azure Active Directory (Azure AD).

L’intégration de TimeLive avec Azure AD offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à TimeLive.
- Vous pouvez permettre aux utilisateurs à se connecter automatiquement à TimeLive (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec TimeLive, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement TimeLive pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de TimeLive à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-timelive-from-the-gallery"></a>Ajout de TimeLive à partir de la galerie
Pour configurer l’intégration de TimeLive avec Azure AD, vous devez ajouter TimeLive, disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter TimeLive à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **TimeLive**, sélectionnez **TimeLive** dans le panneau des résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![TimeLive dans la liste des résultats](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec TimeLive, sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur TimeLive correspondant à un utilisateur dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur TimeLive associé doit être établie.

Dans TimeLive, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec TimeLive, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer un utilisateur de test TimeLive](#create-a-timelive-test-user)** pour avoir dans TimeLive un équivalent de Britta Simon lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application TimeLive.

**Pour configurer l’authentification unique Azure AD avec TimeLive, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **TimeLive**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_samlbase.png)

3. Dans la section **Domaine et URL TimeLive**, procédez comme suit :

    ![Informations d’authentification unique dans Domaine et URL TimeLive](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://domainname.livetecs.com/`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://domainname.livetecs.com/`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de connexion réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique TimeLive](mailto:support@livetecs.com). 

4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-timelive-tutorial/tutorial_general_400.png)
    
6. Dans la section **Configuration de TimeLive**, cliquez sur **Configurer TimeLive** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide**.

    ![Configuration de TimeLive](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_configure.png)

7. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise TimeLive en tant qu’administrateur.

8. Sous **Options d’administration**, sélectionnez **Préférences**.

    ![Configuration de TimeLive](./media/active-directory-saas-timelive-tutorial/configure1.png)

9. Dans la section **Préférence d’application**, procédez comme suit :
    
    ![Configuration de TimeLive](./media/active-directory-saas-timelive-tutorial/configure2.png)

    a. Sélectionnez l’onglet **Sécurité**.

    b. Activez la case à cocher **Activer l’authentification unique (SSO)**.

    c. Dans le menu déroulant intitulé **Se connecter à l’aide de l’authentification unique (SSO) avec**, sélectionnez **SAML**.

    d. Collez l’**URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure dans la zone de texte **SAML SSO URL**.

    e. Dans le champ **URL de déconnexion distante**, collez la valeur de l’**URL de déconnexion** que vous avez copiée à partir du portail Azure.

    f. Dans le Bloc-notes, ouvrez le **certificat codé en base 64** téléchargé du portail Azure, copiez son contenu, puis collez-le dans la zone de texte **X.509 Certificate** (Certificat X.509).

    g. Cliquez sur **Update**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-timelive-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-timelive-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-timelive-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-timelive-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="create-a-timelive-test-user"></a>Créer un utilisateur de test TimeLive

L’objectif de cette section est de créer un utilisateur nommé Britta Simon dans TimeLive. TimeLive prend en charge l’approvisionnement juste-à-temps, option activée par défaut. Vous n’avez aucune opération à effectuer dans cette section. Un utilisateur est créé lors d’une tentative d’accès à TimeLive s’il n’existe pas encore.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à TimeLive.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à TimeLive, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **TimeLive**.

    ![Lien TimeLive dans la liste des applications](./media/active-directory-saas-timelive-tutorial/tutorial_timelive_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette TimeLive dans le panneau d’accès, vous devez être connecté automatiquement à votre application TimeLive.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-timelive-tutorial/tutorial_general_203.png

