---
title: "Didacticiel : Intégration d’Azure Active Directory à GitHub | Microsoft Docs"
description: "Découvrez comment configurer l’authentification unique entre Azure Active Directory et GitHub."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4395bd95-05de-4deb-87a5-dc3bc8ac4d95
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 2a0e1df5244ef977bdcccc5bcfea615a05efa3bd
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Didacticiel : Intégration d’Azure Active Directory à GitHub

Dans ce didacticiel, vous allez apprendre à intégrer GitHub à Azure Active Directory (Azure AD).

L’intégration de GitHub à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à GitHub
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à GitHub (via l’authentification unique) avec leur compte Azure AD
- Vous pouvez gérer vos comptes de manière centralisée dans le portail de gestion Azure.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Composants requis

Pour configurer l’intégration d’Azure AD à GitHub, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement GitHub pour lequel l’authentification unique est activée


> [!NOTE]
> Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test. Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de GitHub à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## <a name="adding-github-from-the-gallery"></a>Ajout de GitHub à partir de la galerie
Pour configurer l’intégration de GitHub à Azure AD, vous devez ajouter GitHub à partir de la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter GitHub à partir de la galerie, procédez comme suit :**

1. Dans le panneau de navigation gauche du **[Portail de gestion Azure](https://portal.azure.com)**, cliquez sur l’icône **Azure Active Directory**. 

    ![Active Directory][1]

2. Accédez à **Applications d’entreprise**. Accédez ensuite à **Toutes les applications**.

    ![Applications][2]
    
3. Cliquez sur le bouton **Ajouter** en haut de la boîte de dialogue.

    ![Applications][3]

4. Dans la zone de recherche, tapez **GitHub.com**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search02.png)

5. Dans le volet de résultats, sélectionnez **GitHub**, puis cliquez sur **Ajouter** pour ajouter l’application.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-github-tutorial/tutorial_github_search_result02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec GitHub, avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur GitHub équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et un utilisateur GitHub associé doit être établie.

Pour cela, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans GitHub.

Pour configurer et tester l'authentification unique Azure AD avec GitHub, vous devez suivre les indications des sections suivantes :

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d'un utilisateur de test GitHub](#creating-a-GitHub-test-user)** pour avoir un équivalent de Britta Simon dans GitHub lié à la représentation Azure AD associée.
4. **[Affectation de l’utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d'utiliser l'authentification unique Azure AD.
5. **[Testing Single Sign-On](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail de gestion Azure et configurer l’authentification unique dans votre application GitHub.

**Pour configurer l’authentification unique Azure AD avec GitHub, procédez comme suit :**

1. Dans le portail de gestion Azure, sur la page d’intégration de l’application **GitHub**, cliquez sur **Authentification unique**.

    ![Configurer l’authentification unique][4]

2. Dans la boîte de dialogue **Authentification unique**, pour le **Mode**, sélectionnez **Authentification basée sur SAML** pour activer l’authentification unique.
 
    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_github_01.png)

3. Dans la section **Domaine et URL GitHub**, procédez comme suit :

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_github_saml011.png)

    a. Dans la zone de texte **URL de connexion**, tapez la valeur suivante : `https://github.com/orgs/<entity-id>/sso`

    b. Dans la zone de texte **Identificateur**, tapez une URL au format suivant : `https://github.com/orgs/<entity-id>`

    > [!NOTE] 
    > Notez qu’il ne s’agit pas des valeurs réelles. Vous devez mettre à jour ces valeurs avec l’URL de connexion et l’identificateur réels. Nous vous suggérons d’utiliser ici la valeur de chaîne unique dans l’identificateur. Accédez à la section d’administration de GitHub pour extraire ces valeurs. 

4. Dans la section **Attributs utilisateur**, sélectionnez user.mail en tant qu’**Identificateur utilisateur**.

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_github_attribute_new01.png)
    
5. Dans la section **Certificat de signature SAML**, cliquez sur **Créer un certificat**.

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_github_03.png)

6. Dans la boîte de dialogue **Créer un certificat**, cliquez sur l’icône de calendrier et sélectionnez une **date d’expiration**. Ensuite, cliquez sur le bouton **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_general_300.png)

7. Dans la section **Certificat de signature SAML**, sélectionnez **Activer le nouveau certificat** et cliquez sur **Enregistrer**.

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_github_04.png)

8. Dans la fenêtre contextuelle **Certificat de substitution**, cliquez sur **OK**.

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_general_400.png)

9. Dans la section **Certificat de signature SAML**, cliquez sur **Téléchargez le certificat (Base64)** puis enregistrez le fichier du certificat sur votre ordinateur.

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_github_05.png) 

10. Dans la section **Configuration de GitHub**, cliquez sur **Configurer GitHub** pour ouvrir la fenêtre **Configurer l’authentification**.

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_github_06.png) 

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_github_07.png)

11. Dans une autre fenêtre de navigateur web, connectez-vous à votre site d’entreprise GitHub en tant qu’administrateur.

12. Accédez aux **Paramètres** et cliquez sur **Sécurité**

    ![Paramètres](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_03.png)

13. Cochez la case **Activer l’authentification SAML**, révélant ainsi les champs de configuration de l’authentification unique. Utilisez en suite la valeur d’URL d’authentification unique pour mettre à jour l’URL d’authentification unique dans la configuration d’Azure AD.

    ![Paramètres](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_13.png)

14. Configurez les champs suivants :

    a. **URL d’authentification** : entrez l’**URL du service d’authentification unique SAML** à partir de la section **Configurer GitHub** sur Azure AD

    b. **Émetteur**: entrez l’**ID d’entité SAML** à partir de la section **Configurer GitHub** sur Azure AD

    c. **Certificat public** : ouvrez le certificat téléchargé à partir d’Azure AD dans un bloc-notes et copiez-en le contenu, de « BEGIN CERTIFICATE » jusqu’à « END CERTIFICATE »

    ![Paramètres](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_051.png)

15. Cliquez sur **Tester la configuration SAML** pour vérifier l’absence d’échecs ou d’erreurs de validation pendant l’authentification unique (SSO).

    ![Paramètres](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_06.png)

16. Cliquez sur **Enregistrer**.

### <a name="creating-an-azure-ad-test-user"></a>Création d’un utilisateur de test Azure AD
L’objectif de cette section est de créer un utilisateur de test appelé Britta Simon dans le Portail de gestion Azure.

![Créer un utilisateur Azure AD][100]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le panneau de navigation gauche du **Portail de gestion Azure**, cliquez sur l’icône **Azure Active Directory**.

    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_01.png) 

2. Accédez à **Utilisateurs et groupes**, puis cliquez sur **Tous les utilisateurs** pour afficher la liste des utilisateurs.
    
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_02.png) 

3. En haut de la boîte de dialogue, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Utilisateur**.
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_03.png) 

4. Dans la boîte de dialogue **Utilisateur**, procédez comme suit :
 
    ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-github-tutorial/create_aaduser_04.png) 

    a. Dans la zone de texte **Nom**, entrez **BrittaSimon**.

    b. Dans la zone de texte **Nom d’utilisateur**, tapez **l’adresse e-mail** de Britta Simon.

    c. Sélectionnez **Afficher le mot de passe** et notez la valeur du **mot de passe**.

    d. Cliquez sur **Create**. 


### <a name="creating-a-github-test-user"></a>Création d’un utilisateur de test GitHub

Pour permettre aux utilisateurs Azure AD de se connecter à GitHub, vous devez les approvisionner dans GitHub.  
Dans le cas de GitHub, cet approvisionnement est une tâche manuelle.

**Pour approvisionner un compte d’utilisateur, procédez comme suit :**

1. Connectez-vous à votre site d’entreprise GitHub en tant qu’administrateur.

2. Cliquez sur **People**.

    ![Personnes](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_08.png "Personnes")

3. Cliquez sur **Invite member**.

    ![Inviter des utilisateurs](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_09.png "Inviter des utilisateurs")

4. Dans la boîte de dialogue **Invite member**, procédez comme suit :

    a. Dans la zone de texte **E-mail** , tapez l’adresse de messagerie du compte de Britta Simon.

    ![Inviter des personnes](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_10.png "inviter des personnes")
    
    b. Cliquez sur **Send Invitation**.

    ![Inviter des personnes](./media/active-directory-saas-github-tutorial/tutorial_github_config_github_11.png "inviter des personnes")

    > [!NOTE]
    > Le titulaire du compte Azure Active Directory reçoit un message électronique contenant un lien à suivre pour confirmer son compte et l’activer.


### <a name="assigning-the-azure-ad-test-user"></a>Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à GitHub.

![Affecter des utilisateurs][200] 

**Pour affecter Britta Simon à GitHub, procédez comme suit :**

1. Dans le Portail de gestion Azure, ouvrez la vue des applications, accédez à la vue des répertoires, allez à **Applications d’entreprise**, puis cliquez sur **Toutes les applications**.

    ![Affecter des utilisateurs][201] 

2. Dans la liste des applications, sélectionnez **GitHub.com**.

    ![Configurer l’authentification unique](./media/active-directory-saas-github-tutorial/tutorial_github_search_result021.png) 

3. Dans le menu de gauche, cliquez sur **Utilisateurs et groupes**.

    ![Affecter des utilisateurs][202] 

4. Cliquez sur le bouton **Ajouter**. Ensuite, sélectionnez **Utilisateurs et groupes** dans la boîte de dialogue **Ajouter une affectation**.

    ![Affecter des utilisateurs][203]

5. Dans la boîte de dialogue **Utilisateurs et groupes**, sélectionnez **Britta Simon** dans la liste des utilisateurs.

6. Cliquez sur le bouton **Sélectionner** dans la boîte de dialogue **Utilisateurs et groupes**.

7. Cliquez sur le bouton **Affecter** dans la boîte de dialogue **Ajouter une affectation**.
    


### <a name="testing-single-sign-on"></a>Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la vignette GitHub dans le volet d’accès, vous devez être connecté à votre application GitHub. Vous allez vous connecter en tant qu’organisation, mais vous devrez ensuite vous connecter avec votre compte personnel.


## <a name="additional-resources"></a>Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-github-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-github-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-github-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-github-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-github-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-github-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-github-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-github-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-github-tutorial/tutorial_general_203.png
