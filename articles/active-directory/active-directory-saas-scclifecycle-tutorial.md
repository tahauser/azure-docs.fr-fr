---
title: "Didacticiel : Intégration d’Azure Active Directory avec SCC LifeCycle | Microsoft Docs"
description: "Découvrez comment configurer l'authentification unique entre Azure Active Directory et SCC LifeCycle."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: c7e6cc4a78b3e31b1357671fdb19d8eb9cf927ce
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/05/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Didacticiel : Intégration d’Azure AD avec SCC LifeCycle

Dans ce didacticiel, vous allez apprendre à intégrer SCC LifeCycle avec Azure Active Directory (Azure AD).

L’intégration de SCC LifeCycle avec Azure AD vous offre les avantages suivants :

- Vous pouvez contrôler dans Azure AD qui a accès à SCC LifeCycle.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à SCC LifeCycle (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Conditions préalables

Pour configurer l’intégration d’Azure AD avec SCC LifeCycle, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement SCC LifeCycle pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois ici : [offre d’essai](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de SCC LifeCycle à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-scc-lifecycle-from-the-gallery"></a>Ajout de SCC LifeCycle à partir de la galerie
Pour configurer l’intégration de SCC LifeCycle à Azure AD, vous devez ajouter SCC LifeCycle à partir de la galerie à votre liste d’applications SaaS managées.

**Pour ajouter SCC LifeCycle à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![APPLICATIONS][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![APPLICATIONS][3]

4. Dans la zone de recherche, tapez **SCC LifeCycle**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-scclifecycle-tutorial/tutorial_scclifecycle_search.png)

5. Dans le volet de résultats, sélectionnez **SCC LifeCycle**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-scclifecycle-tutorial/tutorial_scclifecycle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD

Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec SCC LifeCycle sur un utilisateur de test nommé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur SCC LifeCycle équivalent à l’utilisateur dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur SCC LifeCycle associé doit être établie.

Dans SCC LifeCycle, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec SCC LifeCycle, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test SCC LifeCycle](#creating-an-scc-lifecycle-test-user)** pour avoir un équivalent de Britta Simon dans SCC LifeCycle lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** : permet à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application SCC LifeCycle.

**Pour configurer l’authentification unique Azure AD avec SCC LifeCycle, procédez comme suit :**

1. Dans le portail Azure, dans la page d’intégration de l’application **SCC LifeCycle**, cliquez sur **Authentification unique**.

    ![Configure Single Sign-On][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configure Single Sign-On](./media/active-directory-saas-scclifecycle-tutorial/tutorial_scclifecycle_samlbase.png)

3. Dans la section **Domaine et URL SCC LifeCycle**, procédez comme suit :

    ![Configure Single Sign-On](./media/active-directory-saas-scclifecycle-tutorial/tutorial_scclifecycle_url.png)

    a. Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<sub-domain>.scc.com/ic7/welcome/customer/PICTtest.aspx`

    b. Dans la zone de texte **Identificateur**, entrez une URL au format suivant :
    | |
    |--|--|
    | `https://bs1.scc.com/<entity>`|
    | `https://lifecycle.scc.com/<entity>`|
    
    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Pour obtenir ces valeurs, contactez l’[équipe de support technique SCC LifeCycle](mailto:lifecycle.support@scc.com). 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configure Single Sign-On](./media/active-directory-saas-scclifecycle-tutorial/tutorial_scclifecycle_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configure Single Sign-On](./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_400.png)

6. Pour configurer l’authentification unique côté **SCC LifeCycle**, vous devez envoyer le **XML de métadonnées** téléchargé à [l’équipe de support technique SCC LifeCycle](mailto:lifecycle.support@scc.com). Celles-ci configurent ensuite ce paramètre pour que la connexion SSO SAML soit définie correctement des deux côtés.

  >[!NOTE]
  >L’authentification unique doit être activée par l’équipe de support de SCC LifeCycle.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-scclifecycle-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Créer**.
 
### <a name="creating-an-scc-lifecycle-test-user"></a>Création d’un utilisateur de test SCC LifeCycle

Pour se connecter à SCC LifeCycle, les utilisateurs d’Azure AD doivent être approvisionnés dans SCC LifeCycle. Vous n’avez rien à faire pour configurer l’approvisionnement des utilisateurs dans SCC LifeCycle.

Lorsqu’un utilisateur tente de se connecter à SCC LifeCycle, un compte SCC LifeCycle est, au besoin, automatiquement créé.

> [!NOTE]
> Le titulaire du compte Azure Active Directory reçoit un e-mail contenant un lien à suivre pour confirmer son compte et l’activer.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à SCC LifeCycle.

![Affecter des utilisateurs][200] 

**Pour attribuer Britta Simon à SCC LifeCycle, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des annuaires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **SCC LifeCycle**.

    ![Configure Single Sign-On](./media/active-directory-saas-scclifecycle-tutorial/tutorial_scclifecycle_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Si vous cliquez sur la vignette SCC LifeCycle dans le volet d’accès, vous devez vous connecter automatiquement à votre application SCC LifeCycle.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-scclifecycle-tutorial/tutorial_general_203.png

