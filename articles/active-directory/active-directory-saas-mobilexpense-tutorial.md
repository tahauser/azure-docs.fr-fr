---
title: "Didacticiel : Intégration d’Azure Active Directory à MobileXpense | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et MobileXpense."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e649fc4e-3e15-4948-b977-00bfe9f7db13
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2017
ms.author: jeedes
ms.openlocfilehash: f1d65a2404fc90943bba4921158f5366bc9778ac
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mobilexpense"></a>Didacticiel : Intégration d’Azure Active Directory à MobileXpense

Dans ce didacticiel, vous allez apprendre à intégrer MobileXpense à Azure Active Directory (Azure AD).

L’intégration de MobileXpense à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à MobileXpense
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à MobileXpense (via l’authentification unique) avec leurs comptes Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prérequis

Pour configurer l’intégration d’Azure AD à MobileXpense, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement MobileXpense pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de MobileXpense à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-mobilexpense-from-the-gallery"></a>Ajout de MobileXpense à partir de la galerie
Pour configurer l’intégration de MobileXpense à Azure AD, vous devez ajouter MobileXpense à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter MobileXpense à partir de la galerie, effectuez les étapes suivantes :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **MobileXpense**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_search.png)

5. Dans le volet de résultats, sélectionnez **MobileXpense**, puis cliquez sur le bouton **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec MobileXpense pour un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur MobileXpense équivalent à un utilisateur dans Azure AD. En d’autres termes, une relation doit être établie entre un utilisateur Azure AD et l’utilisateur associé dans MobileXpense.

Dans MobileXpense, affectez la valeur du **nom d’utilisateur** dans Azure AD comme valeur du **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec MobileXpense, vous devez effectuer les deux actions essentielles suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test MobileXpense](#creating-a-mobilexpense-test-user)** pour avoir un équivalent de Britta Simon dans MobileXpense lié à la représentation Azure AD de l’utilisateur.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application MobileXpense.

**Pour configurer l’authentification unique Azure AD avec MobileXpense, effectuez les étapes suivantes :**

1. Dans le portail Azure, sur la page d’intégration de l’application **MobileXpense**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_samlbase.png)

3. Dans la section **Domaines et URL MobileXpense**, si vous souhaitez configurer l’application en mode initié par le **fournisseur d’identité (IDP)** :

    ![Configurer l’authentification unique](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_url11.png)

    Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<sub domain>.mobilexpense.com/SSO/SAML20/SAML/AssertionConsumerService.aspx`

4. Cochez **Afficher les paramètres d’URL avancés** si vous souhaitez configurer l’application en mode initié par le **fournisseur de service** :

    ![Configurer l’authentification unique](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_url22.png)

Dans la zone de texte **URL de connexion**, tapez une URL au format suivant : `https://<sub domain>.mobilexpense.com/<customername>`

> [!NOTE] 
> Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’URL de réponse et l’URL de connexion réelles. Pour obtenir ces valeurs, contactez l’[équipe de support technique MobileXpense](http://www.mobilexpense.net/contact). 

5. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_certificate.png) 

6. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_400.png)

8. Pour configurer l’authentification unique côté **MobileXpense**, vous devez envoyer le fichier **XML de métadonnées** téléchargé à l’[équipe de support technique MobileXpense](http://www.mobilexpense.net/contact).

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mobilexpense-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mobilexpense-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mobilexpense-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mobilexpense-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-mobilexpense-test-user"></a>Création d’un utilisateur de test MobileXpense

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans MobileXpense. Collaborez avec l’[équipe du support technique MobileXpense](http://www.mobilexpense.net/contact) pour ajouter des utilisateurs dans la plateforme MobileXpense. Les utilisateurs doivent être créés et activés avant que vous utilisiez l’authentification unique. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en accordant l’accès à MobileXpense.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à MobileXpense, effectuez les étapes suivantes :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **MobileXpense**.

    ![Configurer l’authentification unique](./media/active-directory-saas-mobilexpense-tutorial/tutorial_mobilexpense_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Quand vous cliquez sur la mosaïque MobileXpense dans le volet d’accès, vous devez être automatiquement connecté à votre application MobileXpense.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](https://msdn.microsoft.com/library/dn308586). 

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mobilexpense-tutorial/tutorial_general_203.png

