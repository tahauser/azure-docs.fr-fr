---
title: "Didacticiel : Intégration d’Azure Active Directory à QPrism | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et QPrism."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: jeedes
ms.openlocfilehash: 1f697b95074e0fc9dbb3e8c7800e69f8ece9e0b3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Didacticiel : Intégration d’Azure Active Directory à QPrism

Dans ce didacticiel, vous allez apprendre à intégrer QPrism dans Azure Active Directory (Azure AD).

L’intégration de QPrism dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à QPrism.
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à QPrism (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes dans un emplacement central : le portail Azure.

Pour plus d’informations sur l’intégration des applications SaaS à Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à QPrism, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement QPrism pour lequel l’authentification unique est activée

Pour tester la procédure de ce didacticiel, suivez les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez [obtenir un essai d’un mois](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de QPrism à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="add-qprism-from-the-gallery"></a>Ajouter QPrism à partir de la galerie
Pour configurer l’intégration de QPrism avec Azure AD, vous devez ajouter QPrism disponible dans la galerie, à votre liste d’applications SaaS gérées.

**Pour ajouter QPrism à partir de la galerie :**

1. Dans le volet gauche du [portail Azure](https://portal.azure.com), sélectionnez **Azure Active Directory**. 

    ![Bouton Azure Active Directory][1]

2. Accédez à **Applications d’entreprise** > **Toutes les applications**.

    ![Panneau Applications d’entreprise][2]
    
3. Pour ajouter une application, en haut de la boîte de dialogue, sélectionnez **Nouvelle application**.

    ![Bouton Nouvelle application][3]

4. Dans la zone de recherche, tapez **QPrism**, puis sélectionnez **QPrism** dans le volet de résultats. Ensuite, pour ajouter l’application, cliquez sur **Ajouter**.

    ![QPrism dans la liste des résultats](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurer et tester l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD auprès de QPrism avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur QPrism équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur QPrism associé doit être établie.

Pour établir cette relation, dans QPrism, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur**.

Pour configurer et tester l’authentification unique Azure AD avec QPrism, suivez les indications des sections suivantes :

1. [Configurer l’authentification unique Azure AD](#configure-azure-ad-single-sign-on) pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. [Créer un utilisateur de test Azure AD](#create-an-azure-ad-test-user) pour tester l’authentification unique Azure AD avec Britta Simon.
3. [Créer un utilisateur de test QPrism](#create-a-qprism-test-user) pour avoir dans QPrism un équivalent de Britta Simon lié à la représentation Azure AD associée.
4. [Affecter l’utilisateur de test Azure AD](#assign-the-azure-ad-test-user) pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. [Tester l’authentification unique](#test-single-sign-on) pour vérifier si la configuration fonctionne.

### <a name="configure-azure-ad-single-sign-on"></a>Configurer l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application QPrism.

1. Dans le portail Azure, dans la page d’intégration de l’application **QPrism**, cliquez sur **Authentification unique**.

    ![Lien Configurer l’authentification unique][4]

2. Pour activer l’authentification unique, dans la boîte de dialogue **Authentification unique**, configurez **Mode** sur **Authentification basée sur SAML**.
 
    ![Boîte de dialogue Authentification unique](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. Dans la section **Domaine et URL Absorb QPrism**, effectuez les étapes suivantes :

    ![Informations d’authentification unique dans Domaine et URL QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<customer domain>.qmyzone.com/login`

    b. Dans la zone de texte **Identificateur**, saisissez une URL au format suivant : `https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support QPrism](mailto:qsupport-ce@quatrro.com). 

4. Pour générer l’URL des **métadonnées**, effectuez les étapes suivantes :

    a. Sélectionnez **Inscriptions d’applications**.
    
    ![Configuration des inscriptions d’applications pour l’authentification unique](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appregistrations.png)
   
    b. Cliquez sur **Points de terminaison** pour ouvrir la boîte de dialogue **Points de terminaison**.  
    
    ![Configuration du point de terminaison pour l’authentification unique](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpointicon.png)

    c. Cliquez sur le bouton Copier pour copier l’URL du document de métadonnées de fédération (**FEDERATION METADATA DOCUMENT**), puis collez-la dans le Bloc-notes.
    
    ![Configuration du point de terminaison pour l’authentification unique](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpoint.png)
     
    d. Accédez maintenant à la page de propriétés de **QPrism**, puis copiez **l’ID d’application** à l’aide du bouton **Copier**. Ensuite, collez-le dans le Bloc-notes.
 
    ![Configuration de l’ID d’application pour l’authentification unique](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appid.png)

    e. Générez **l’URL des métadonnées** en utilisant le modèle suivant : `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`. 

5. Sélectionnez **Enregistrer**.

    ![Bouton Enregistrer de la page Configurer l’authentification unique](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. Pour configurer l’authentification unique côté **QPrism**, envoyez **l’URL des métadonnées** à [l’équipe de support QPrism](mailto:qsupport-ce@quatrro.com). L’équipe vérifiera que la connexion SAML à authentification unique est correctement configurée des deux côtés.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application. Après avoir ajouté cette application à partir de la section **Active Directory** > **Applications d’entreprise**, cliquez sur l’onglet **Authentification unique**, puis accédez à la documentation incorporée par le biais de la section **Configuration** située au bas de la page. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Créer un utilisateur de test Azure AD

L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

   ![Créer un utilisateur de test Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD**

1. Dans le volet gauche du portail Azure, sélectionnez **Azure Active Directory**.

    ![Bouton Azure Active Directory](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.

    ![Liens « Utilisateurs et groupes » et « Tous les utilisateurs »](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. Pour ouvrir la boîte de dialogue **Utilisateur**, sélectionnez **Ajouter** en haut de la boîte de dialogue **Tous les utilisateurs**.

    ![Bouton Ajouter](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :

    ![Boîte de dialogue Utilisateur](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. Dans la zone **Nom**, tapez **BrittaSimon**.

    b. Dans la zone **Nom d’utilisateur** , tapez l’adresse e-mail de l’utilisateur Britta Simon.

    c. Cochez la case **Afficher le mot de passe**, puis notez la valeur affichée dans le champ **Mot de passe**.

    d. Sélectionnez **Créer**.
 
### <a name="create-a-qprism-test-user"></a>Créer un utilisateur de test QPrism

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans QPrism. Collaborez avec [l’équipe de support QPrism](mailto:qsupport-ce@quatrro.com) pour ajouter des utilisateurs dans la plateforme QPrism. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assign-the-azure-ad-test-user"></a>Affecter l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à QPrism.

![Attribuer le rôle utilisateur][200] 

**Pour attribuer Britta Simon à QPrism :**

1. Dans le portail Azure, affichez les applications, puis accédez à la vue des répertoires. Accédez à **Applications d’entreprise**, puis sélectionnez **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **QPrism**.

    ![Lien QPrism dans la liste des applications](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. Dans le menu de gauche, sélectionnez **Utilisateurs et groupes**.

    ![Lien « Utilisateurs et groupes »][202]

4. Sélectionnez **Ajouter**. Sous **Ajouter une attribution**, sélectionnez **Utilisateurs et groupes**.

    ![Volet Ajouter une attribution][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste **Utilisateurs**.

6. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Sélectionner**.

7. Sous **Ajouter une attribution**, sélectionnez **Attribuer**.
    
### <a name="test-single-sign-on"></a>Tester l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la vignette QPrism dans le volet d’accès, vous devez être connecté automatiquement à votre application QPrism.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

