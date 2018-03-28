---
title: 'Didacticiel : Intégration d’Azure Active Directory avec Clear Review | Microsoft Docs'
description: Découvrez comment configurer l’authentification unique entre Azure Active Directory et Clear Review.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: jeedes
ms.openlocfilehash: 1e7bd01c9c0f79a2cf96d7fd38dba57c4a407960
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2018
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Didacticiel : Intégration d’Azure Active Directory avec Clear Review

Dans ce didacticiel, vous allez apprendre à intégrer Clear Review avec Azure Active Directory (Azure AD).

L’intégration de Clear Review à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Clear Review.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Clear Review (par le biais de l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis


Pour configurer l’intégration d’Azure AD à Clear Review, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Clear Review pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Clear Review à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-clear-review-from-the-gallery"></a>Ajout de Clear Review à partir de la galerie
Pour configurer l’intégration de Clear Review à Azure AD, vous devez ajouter Clear Review depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Clear Review à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **Clear Review**, sélectionnez **Clear Review** dans le volet de résultats, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Clear Review dans la liste des résultats](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Clear Review, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Clear Review équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur Clear Review associé doit être établie.

Dans Clear Review, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Clear Review, vous devez suivre les indications des sections suivantes :

1. **[Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Créer utilisateur de test Clear Review](#create-a-clear-review-test-user)** pour avoir un équivalent de Britta Simon dans Clear Review lié à la représentation Azure AD associée.
4. **[Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Tester l’authentification unique](#test-single-sign-on)** : pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Clear Review.

**Pour configurer l’authentification unique Azure AD avec Clear Review, effectuez les étapes suivantes :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Clear Review**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_samlbase.png)

3. Dans la section **Domaines et URL Clear Review**, suivez les étapes ci-dessous pour configurer l’application en mode **lancé par IdP** :

    ![Informations d’authentification unique dans Domaine et URL Clear Review](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<customer name>.clearreview.com/sso/metadata/`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<customer name>.clearreview.com/sso/acs/`

4. Si vous souhaitez configurer l’application en **mode démarré par le fournisseur de service**, cochez **Afficher les paramètres d’URL avancés**, puis effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL Clear Review](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_url_sp.png)

    Dans la zone de texte **URL de connexion**, tapez une URL au format suivant :`https://<customer name>.clearreview.com`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion, l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez [l’équipe de support technique Clear Review](https://clearreview.com/contact/).

5. L’application Clear Review attend la valeur d’identificateur utilisateur unique dans la revendication Identificateur de nom. Vous devez mapper la valeur d’identificateur utilisateur à **user.mail**.

    ![La section de l’attribut](./media/active-directory-saas-clearreview-tutorial/attribute.png)


6. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Lien de téléchargement du certificat](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_certificate.png)

7. Cliquez sur le bouton **Enregistrer** .

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-clearreview-tutorial/tutorial_general_400.png)

8. Dans la section **Configuration de Clear Review**, cliquez sur **Configurer Clear Review** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez **l’URL de déconnexion, l’ID d’entité SAML et l’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configuration de Clear Review](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_configure.png) 

9. Pour configurer l’authentification unique du côté de **Clear Review**, ouvrez le portail **Clear Review** avec des informations d’identification d’administrateur.

10. Sélectionnez **Admin** dans le volet de navigation de gauche.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app_admin1.png)

11. En bas de la page, sélectionnez **Change** (Changer).

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app_admin2.png)

12. Dans la page **Single Sign On Settings** (Paramètres de l’authentification unique), effectuez les étapes suivantes :

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. Dans la zone de texte **Issuer URL** (URL de l’émetteur), collez la valeur **SAML Entity ID** (ID d’entité SAML) que vous avez copiée à partir du portail Azure.

    b. Dans la zone de texte **SAML Endpoint** (Point de terminaison SAML), collez la valeur de **l’URL du service d’authentification unique SAML** que vous avez copiée à partir du portail Azure.    

    c. Dans la zone de texte **SLO Endpoint** (Point de terminaison SLO), collez la valeur de **l’URL du service d’authentification unique** que vous avez copiée à partir du portail Azure. 

    d. Ouvrez le certificat que vous avez téléchargé dans le Bloc-notes, puis collez son contenu dans la zone de texte **Certificat X.509**.   

13. Cliquez sur **Enregistrer**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet gauche du Portail Azure, cliquez sur le bouton **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-clearreview-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-clearreview-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-clearreview-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-clearreview-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Cliquez sur **Créer**.
  
### <a name="create-a-clear-review-test-user"></a>Créer un utilisateur de test Clear Review

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Clear Review. Collaborez avec [l’équipe de support Clear Review](https://clearreview.com/contact/) pour ajouter des utilisateurs dans la plateforme Clear Review.

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Clear Review.

![Attribuer le rôle utilisateur][200] 

**Pour affecter Britta Simon à Clear Review, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Clear Review**.

    ![Lien Clear Review dans la liste des applications](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app.png)  

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette Clear Review dans le Panneau d’accès, vous devez être connecté automatiquement à votre application Clear Review.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_203.png
