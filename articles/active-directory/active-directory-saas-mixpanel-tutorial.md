---
title: "Didacticiel : Intégration d’Azure Active Directory à Mixpanel | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et Mixpanel."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a2df26ef-d441-44ac-a9f3-b37bf9709bcb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: e48a192ada32a764b7f23634c0728c04c226b3d3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-mixpanel"></a>Didacticiel : Intégration d’Azure Active Directory à Mixpanel

Dans ce didacticiel, vous allez apprendre à intégrer Mixpanel à Azure Active Directory (Azure AD).

L’intégration de Mixpanel dans Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Mixpanel
- Vous pouvez autoriser les utilisateurs à se connecter automatiquement à Mixpanel (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes à partir d’un emplacement central : le portail Azure

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD avec Mixpanel, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Mixpanel pour lequel l’authentification unique est activée

> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.

Vous devez en outre suivre les recommandations ci-dessous :

- N’utilisez pas votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Mixpanel à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD

## <a name="adding-mixpanel-from-the-gallery"></a>Ajout de Mixpanel à partir de la galerie
Pour configurer l’intégration de Mixpanel à Azure AD, vous devez ajouter Mixpanel à votre liste d’applications SaaS gérées à partir de la galerie.

**Pour ajouter Mixpanel à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **[portail Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Pour ajouter l’application, cliquez sur le bouton **Nouvelle application** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, entrez **Mixpanel**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_search.png)

5. Dans le panneau des résultats, sélectionnez **Mixpanel**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Mixpanel avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Mixpanel équivalent dans Azure AD. En d’autres termes, il faut établir une relation entre l’utilisateur Azure AD et l’utilisateur Mixpanel associé.

Dans Mixpanel, assignez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** pour établir la relation.

Pour configurer et tester l’authentification unique Azure AD avec Mixpanel, vous devez suivre les blocs élémentaires suivants :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Mixpanel](#creating-a-mixpanel-test-user)** pour obtenir un équivalent de Britta Simon dans Mixpanel lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure et configurer l’authentification unique dans votre application Mixpanel.

**Pour configurer l’authentification unique Azure AD avec Mixpanel, procédez comme suit :**

1. Dans le portail Azure, sur la page d’intégration de l’application **Mixpanel**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_samlbase.png)

3. Dans la section **Domaine et URL Mixpanel**, effectuez les étapes suivantes :

    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_url.png)

     Dans la zone de texte **URL d’authentification**, tapez l’URL : `https://mixpanel.com/login/`

    > [!NOTE] 
    > Inscrivez-vous à l’adresse [https://mixpanel.com/register/](https://mixpanel.com/register/) pour configurer vos informations d’identification et contactez [l’équipe du support technique Mixpanel](mailto:support@mixpanel.com) pour activer les paramètres d’authentification unique de votre abonné. Vous pouvez également obtenir la valeur de l’URL de connexion si nécessaire à partir de votre équipe de support Mixpanel. 
 
4. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_certificate.png) 

5. Cliquez sur le bouton **Enregistrer** .

    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_general_400.png)

6. Dans la section **Configuration de Mixpanel**, cliquez sur **Configurer Mixpanel** pour ouvrir la fenêtre **Configurer l’authentification**. Copiez l**’URL du service d’authentification unique SAML** à partir de la **section Référence rapide.**

    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_configure.png) 

7. Dans une autre fenêtre de navigateur, connectez-vous à votre application Mixpanel en tant qu’administrateur.

8. Dans le coin gauche en bas de la page, cliquez sur l’icône représentant un petit **engrenage** . 
   
    ![Authentification unique Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_06.png) 

9. Cliquez sur l’onglet **Sécurité d’accès**, puis cliquez sur **Modifier les paramètres**.
   
    ![Paramètres Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_08.png) 

10. Sur la page **Modifier votre certificat**, cliquez sur **Choisir un fichier** pour importer votre certificat téléchargé, puis cliquez sur **SUIVANT**.
   
    ![Paramètres Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_09.png) 

11.  Dans la zone de texte URL d’authentification, sur la page de dialogue **Modifier l’URL d’authentification**, collez la valeur de l’**URL du service d’authentification unique SAML**, que vous avez copiée sur le portail Azure. Puis cliquez sur **SUIVANT**.
   
   ![Paramètres Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_10.png) 

12. Cliquez sur **Done**.

> [!TIP]
> Vous pouvez maintenant lire une version concise de ces instructions dans le [portail Azure](https://portal.azure.com), pendant que vous configurez l’application.  Après avoir ajouté cette application à partir de la section **Active Directory > Applications d’entreprise**, cliquez simplement sur l’onglet **Authentification unique** et accédez à la documentation incorporée par le biais de la section **Configuration** en bas. Vous pouvez en savoir plus sur la fonctionnalité de documentation incorporée ici : [Documentation incorporée Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le portail Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **portail Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_01.png) 

2. Pour afficher la liste des utilisateurs, accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs**.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_02.png) 

3. Pour ouvrir la boîte de dialogue **Utilisateur**, cliquez sur **Ajouter** en haut de la boîte de dialogue.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-mixpanel-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**.
 
### <a name="creating-a-mixpanel-test-user"></a>Création d’un utilisateur de test Mixpanel

L’objectif de cette section est de créer un utilisateur appelé Britta Simon dans Mixpanel. 

1. Connectez-vous à votre site d’entreprise Mixpanel en tant qu’administrateur.

2. Dans le coin gauche en bas de la page, cliquez sur le bouton représentant un petit engrenage pour ouvrir la fenêtre **Paramètres** .

3. Cliquez sur l’onglet **Équipe** .

4. Dans la zone de texte **membre de l’équipe** , entrez l’adresse de messagerie de Britta dans le portail Azure.
   
    ![Paramètres Mixpanel](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_11.png) 

5. Cliquez sur **Inviter**. 

> [!Note]
> L’utilisateur recevra un e-mail pour configurer son profil.

### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Mixpanel.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à Mixpanel, procédez comme suit :**

1. Dans le portail Azure, ouvrez la vue des applications, accédez à la vue des répertoires, accédez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **Mixpanel**.

    ![Configurer l’authentification unique](./media/active-directory-saas-mixpanel-tutorial/tutorial_mixpanel_app.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    
### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Mixpanel dans le volet d’accès, vous êtes connecté automatiquement à votre application Mixpanel.
Pour plus d’informations sur le panneau d’accès, consultez [Présentation du panneau d’accès](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mixpanel-tutorial/tutorial_general_203.png

