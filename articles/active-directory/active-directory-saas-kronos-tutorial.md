---
title: "Didacticiel : Intégration d’Azure Active Directory à Kronos | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Kronos."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e28d6191-c375-43c6-b2df-22daa88d9939
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: d24bbc4ad4be23dc983e9c4dfa922e7ff9c7c2ff
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-kronos"></a>Didacticiel : Intégration d’Azure Active Directory à Kronos

Dans ce didacticiel, vous allez apprendre à intégrer Kronos à Azure Active Directory (Azure AD).

L’intégration de Kronos dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Kronos
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Kronos (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Kronos, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement **Kronos Workforce Central** pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Kronos à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-kronos-from-the-gallery"></a>Ajout de Kronos à partir de la galerie
Pour configurer l’intégration de Kronos avec Azure AD, vous devez ajouter Kronos à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Kronos à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Kronos**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_search.png)

5. Dans le volet de résultats, sélectionnez **Kronos**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Kronos avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Kronos équivalent dans Azure AD. En d’autres termes, une relation entre l’utilisateur Azure AD et l’utilisateur Kronos associé doit être établie.

Dans Kronos, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Kronos, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Kronos](#creating-a-kronos-test-user)** pour avoir un équivalent de Britta Simon dans Kronos lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Kronos.

**Pour configurer l’authentification unique Azure AD avec Kronos, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Kronos**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_samlbase.png)

3. Dans la section **Domaine et URL Kronos**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_url.png)

    a. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://<company name>.kronos.net/`

    b. Dans la zone de texte **URL de réponse** , tapez une URL au format suivant : `https://<company name>.kronos.net/wfc/navigator/logonWithUID`

    > [!NOTE] 
    > Il ne s’agit pas de valeurs réelles. Mettez à jour ces valeurs avec l’identificateur et l’URL de réponse réels. Pour obtenir ces valeurs, contactez l’[l’équipe de support technique de Kronos](https://www.kronos.in/contact/en-in/form).
 
4. Votre application Kronos attend les assertions SAML dans un format spécifique. Collaborez avec [l’équipe de support de Kronos](https://www.kronos.in/contact/en-in/form) pour identifier tout d’abord l’identificateur utilisateur correct qui est mappé à l’application. Suivez également les instructions sur l’attribut à utiliser pour ce mappage.
 
     Microsoft recommande d’utiliser l’attribut **« NameIdentifier »** en tant qu’identificateur utilisateur. Vous pouvez gérer les valeurs de ces attributs à partir de la section **« Attributs utilisateur »** sur la page d’intégration des applications.
     
     La capture d’écran suivante montre un exemple : Ici, nous avons mappé **l’identificateur d’utilisateur (nameid)** avec la fonction **ExtractMailPrefix()** de **user.userprincipalname**. Cela donne la valeur de préfixe de l’adresse de messagerie de l’utilisateur qui est l’ID d’utilisateur unique. Celle-ci est envoyée à l’application Kronos pour chaque réponse correcte. 
     
    ![Configurer l’authentification unique](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_attribute.png)

5. Dans la section **Attributs utilisateur** de la boîte de dialogue **Authentification unique** :

    a. Dans la liste déroulante Identificateur de l’utilisateur, sélectionnez **ExtractMailPrefix**.

    b. Dans la liste déroulante **E-mail**, sélectionnez **user.userprincipalname**.

6. Dans la section **Certificat de signature SAML**, cliquez sur **Métadonnées XML** puis enregistrez le fichier de métadonnées sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_certificate.png) 

7. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-kronos-tutorial/tutorial_general_400.png)

8. Pour configurer l’authentification unique côté **Kronos**, vous devez envoyer le **XML de métadonnées** téléchargé à [l’équipe de support technique de Kronos](https://www.kronos.in/contact/en-in/form). 

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kronos-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kronos-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kronos-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-kronos-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-kronos-test-user"></a>Création d’un utilisateur de test Kronos

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Kronos. Tous les utilisateurs de Kronos doivent être configurés dans cette application avant de procéder à l’authentification unique. 

Collaborez avec l’[équipe de support technique de Kronos](https://www.kronos.in/contact/en-in/form) pour approvisionner tous ces utilisateurs dans l’application. 

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Kronos.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Kronos, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Kronos**.

    ![Configurer l’authentification unique](./media/active-directory-saas-kronos-tutorial/tutorial_kronos_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration SSO Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Kronos dans le volet d’accès, vous devez être connecté automatiquement à votre application Kronos.

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kronos-tutorial/tutorial_general_203.png

